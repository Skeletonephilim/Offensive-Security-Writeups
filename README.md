# Active Directory — Hack The Box Writeups

Blind-first Windows / AD lab notes from [Offensive-Security-Writeups](https://github.com/Skeletonephilim/Offensive-Security-Writeups).

**Parent index:** switch to [`main`](https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/main) for repo-wide doctrine, redaction policy, and other branches ([`Linux`](https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Linux), [`Pro-Labs-/-AI-/-Miscellaneous`](https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Pro-Labs-/-AI-/-Miscellaneous)).

## What this branch is

Writeups for HTB machines where the spine is **Windows Active Directory** or AD-shaped Windows infra: Kerberos, LDAP, SMB, WinRM, domain users/groups, lateral movement, and privilege escalation on member servers or DCs.

Typical port truth on these boxes:

`53/tcp` DNS · `88/tcp` Kerberos · `135/tcp` RPC · `139/445/tcp` SMB · `389/636/tcp` LDAP · `5985/tcp` WinRM (when exposed)

Not every box is a full DC compromise chain. Some stop at interesting identity pivots; the writeup follows what the target actually gave.

## How to read these

- **Blind-first:** enumeration → hypothesis → artifact → next branch. No walkthrough-as-script.
- **AI-assisted:** I use AI for explanation, syntax breakdown, methodology checks, and occasional unblocks — not to replace my own recon.
- **Redacted:** flags, passwords, hashes, and reusable secrets are stripped. Commands and reasoning stay.

## Themes I keep coming back to
- **Coercion / forced authentication** — UNC paths, Responder, `.ini` / share plants, WebDAV-style callbacks
- **Credential reuse & spraying** — one crack → `lookupsid` / user list → `nxc smb`
- **Share ACL truth** — verify WRITE with a real upload, not one tool's label
- **Identity vs execution** — who *runs* the code (service account, human, App Pool) vs who *wrote* the file
- **Token abuse** — `SeImpersonatePrivilege`, potato family, IIS `w3wp.exe` contexts
- **Dual web stacks** — Apache/PHP vs IIS/ASPX on the same host (common on HTB Windows)

## Boxes (by difficulty)
### Easy
| Machine | File |
|---------|------|
| Lock | [Lock [EASY].md](Lock%20%5BEASY%5D.md) |
| Optimum | [Optimum [EASY].md](Optimum%20%5BEASY%5D.md) |
| Return | [Return [EASY].md](Return%20%5BEASY%5D.md) |
| Sauna | [Sauna [EASY].md](Sauna%20%5BEASY%5D.md) |
### Medium
| Machine | File |
|---------|------|
| Administrator | [Administrator [MEDIUM].md](Administrator%20%5BMEDIUM%5D.md) |
| BabyTwo | [BabyTwo [MEDIUM].md](BabyTwo%20%5BMEDIUM%5D.md) |
| Breach | [Breach [MEDIUM].md](Breach%20%5BMEDIUM%5D.md) |
| Phantom | [Phantom [MEDIUM].md](Phantom%20%5BMEDIUM%5D.md) |
| Sendai | [Sendai [MEDIUM].md](Sendai%20%5BMEDIUM%5D.md) |
### Hard
| Machine | File |
|---------|------|
| Blackfield | [Blackfield [HARD].md](Blackfield%20%5BHARD%5D.md) |
| DarkZero | [DarkZero [HARD].md](DarkZero%20%5BHARD%5D.md) |
| Flight | [Flight [HARD].md](Flight%20%5BHARD%5D.md) |
| Pirate | [Pirate [HARD].md](Pirate%20%5BHARD%5D.md) |
| Redelegate | [Redelegate [HARD].md](Redelegate%20%5BHARD%5D.md) |
| Reel | [Reel [HARD].md](Reel%20%5BHARD%5D.md) |
| Shibuya | [Shibuya [HARD].md](Shibuya%20%5BHARD%5D.md) |
