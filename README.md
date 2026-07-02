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
| Phantom | [Phantom [M
