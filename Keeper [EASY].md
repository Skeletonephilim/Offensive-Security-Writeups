
Target : 10.129.229.41

Date : 10/06/2026

```bash
>  echo "10.129.229.41 keeper.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.229.41 keeper.htb  
>  nmap -sC -sV -Pn -O -p- --min-rate=3000 -T4 10.129.229.41  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-10 20:34 +0200  
Warning: 10.129.229.41 giving up on port because retransmission cap hit (6).  
Nmap scan report for keeper.htb (10.129.229.41)  
Host is up (0.058s latency).  
Not shown: 58894 closed tcp ports (reset), 6639 filtered tcp ports (no-response)  
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)  
|_  256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)  
80/tcp open  http    nginx 1.18.0 (Ubuntu)  
|_http-title: Site doesn't have a title (text/html).  
|_http-server-header: nginx/1.18.0 (Ubuntu)  
Device type: general purpose|router  
Running: Linux 5.X, MikroTik RouterOS 7.X  
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3  
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 67.81 seconds
```

So we only have ports `ssh 22/tcp` and `http 80/tcp` open.

We'll run a quick curl on it :

```bash
>  curl -I http://10.129.229.41  
HTTP/1.1 200 OK  
Server: nginx/1.18.0 (Ubuntu)  
Date: Wed, 10 Jun 2026 18:37:21 GMT  
Content-Type: text/html  
Content-Length: 149  
Last-Modified: Wed, 24 May 2023 14:04:44 GMT  
Connection: keep-alive  
ETag: "646e197c-95"  
Accept-Ranges: bytes  
  
>  curl -L http://10.129.229.41  
<html>  
 <body>  
   <a href="http://tickets.keeper.htb/rt/">To raise an IT support ticket, please visit tickets.keeper.htb/rt/</a>  
 </body>  
</html>
```

So we got `tickets.keeper.htb` which we'll add to our hosts.

```bash
>  curl -L http://tickets.keeper.htb/rt  
  
<!DOCTYPE html>  
  
<html lang="en">  
 <head>  
   <title>Login</title>  
   <meta http-equiv="X-UA-Compatible" content="IE=edge" />  
   <!-- The X-UA-Compatible <meta> tag above must be very early in <head> -->  
  
  
<script>  
window.RT = {};  
RT.CurrentUser = {};  
RT.Config      = {"MessageBoxRichText":1,"WebPath":"\/rt","MessageBoxUseSystemContextMenu":0,"MaxAttachmentSize":10485760,"rtname":"tickets.keeper.htb","MessageBoxRichTextHeight":200};  
  
RT.I18N = {};  
RT.I18N.Catalog = {"loading":"Loading...","remove":"Remove","attachment_warning_regex":"\\b(re)?attach","shortcut_help_error":"Unable to open shortcut help. Reason:","check":"Check","try_again":"Try again","quo  
te_in_filename":"Filenames with double quotes can not be uploaded.","history_scroll_error":"Could not load ticket history. Reason:","error":"Error"};  
</script>  
  
  
<link rel="stylesheet" href="/rt/NoAuth/css/rudder/squished-1d23a49d22745117f5e143fd93989aaf.css" type="text/css" media="all" />  
  
<link rel="shortcut icon" href="/rt/static/images/favicon.png" type="image/png" />  
  
  
<!--[if lt IE 8]>  
<link rel="stylesheet" href="/rt/static/css/rudder/msie.css" type="text/css" media="all" />  
<![endif]-->  
  
  
<!-- Site CSS from theme editor -->  
<style type="text/css" media="all" id="sitecss">  
</style>  
  
  
<script type="text/javascript" src="/rt/NoAuth/js/squished-8fc547a59ef3d7e8792af18a242ba6f9.js"></script>  
  
<script type="text/javascript"><!--  
jQuery( loadTitleBoxStates );  
   jQuery(function () { jQuery('\x23user').focus() });  
--></script>  
  
  
<script>  
if (window.top !== window.self) {  
   document.write = "";  
  
   window.top.location = window.self.location;  
  
   setTimeout(function(){  
       document.body.innerHTML = "";  
   }, 1);  
  
   window.self.onload = function(){  
       document.body.innerHTML = "";  
   };  
}  
</script>  
  
  
</head>  
 <body class="rudder sidebyside" id="comp-index">  
  
<div id="logo">  
<a href="http://bestpractical.com"><img  
   src="/rt/static/images/request-tracker-logo.png"  
   alt="Request Tracker logo" /></a>  
   <span class="rtname">RT for tickets.keeper.htb</span>  
</div>  
  
  
<div id="quickbar">  
 <div id="quick-personal">  
   <span class="hide"><a href="#skipnav">Skip Menu</a> | </span>  
   <span id="not-logged-in">Not logged in.</span>  
</div>  
  
</div>  
<div id="header"><h1>Login</h1></div>  
  
  
  
<div id="body" class="login-body">  
  
  
  
  
<div id="login-box">  
<div class="">  
 <div class="titlebox" id="">  
 <div class="titlebox-title">  
   <span class="left">Login</span>  
   <span class="right">                4.4.4+dfsg-2ubuntu1    </span>  
 </div>  
 <div class="titlebox-content " id="TitleBox--_index_html------TG9naW4_---0">  
  
  
  
  
  
<form id="login" name="login" method="post" action="/rt/NoAuth/Login.html">  
  
<div class="input-row">  
   <span class="label">Username:</span>  
   <span class="input"><input name="user" value="" id="user" /></span>  
</div>  
  
<div class="input-row">  
   <span class="label">Password:</span>  
   <span class="input"><input type="password" name="pass" /></span>  
</div>  
  
<input type="hidden" name="next" value="[REDACTED]" />  
  
<script type="text/javascript">  
jQuery(function(){  
   if (window.location.hash) {  
       var form = jQuery("form[name=login]");  
       form.attr('action', form.attr('action') + '#' + window.location.hash.replace(/^#/, ''));  
   }  
});  
</script>  
  
<div class="button-row">  
   <span class="input"><input type="submit" class="button" value="Login" /></span>  
</div>  
  
  
</form>  
   <hr class="clear" />  
 </div>  
</div>  
  
  
  
  
</div>  
  
  
</div><!-- #login-box -->  
</div><!-- #login-body -->  
 <hr class="clear" />  
</div>  
<div id="footer" title="Best Practical Solutions, LLC, copyright and logo">  
 <p id="time"><span>Time to display: 0.276607</span></p>  
 <p id="bpscredits"><span>&#187;&#124;&#171; RT 4.4.4+dfsg-2ubuntu1 (Debian) Copyright 1996-2019 <a href="http://www.bestpractical.com?rt=4.4.4+dfsg-2ubuntu1">Best Practical Solutions, LLC</a>.  
</span></p>  
 <p id="legal">Distributed under <a href="http://www.gnu.org/licenses/gpl-2.0.html">version 2 of the GNU GPL</a>.<br />To inquire about support, training, custom development or licensing, please contact <a hre  
f="mailto:sales@bestpractical.com">sales@bestpractical.com</a>.<br /></p>  
</div>  
 </body>  
</html>
```