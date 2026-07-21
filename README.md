# Offensive Security Writeups

This repository contains my offensive security lab writeups, mostly from Hack The Box.

## Where the writeups live (read this first)
This repo uses **Git branches as folders**. The `main` branch is only the index (this file). **Every writeup is on another branch.**
### Switch branch on GitHub (web)
1. Open this repo in the browser.
2. Top-left, click the branch dropdown (it probably says **`main`**).
3. Pick one of:

| [`Active-Directory`](https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Active-Directory) | AD / Windows lab writeups (Sendai, Pirate, Shibuya, …) | And Purple writeups (Tombwatcher) with a lot more detail, ATT&CK, Event ID / SIEM correlation

| [`Linux`](https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Linux) | Linux HTB writeups (Store, Jarvis lane, Cronos, …) |

| [`Pro-Labs-/-AI-/-Miscellaneous`](https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Pro-Labs-/-AI-/-Miscellaneous) | Pro labs, prompt injection, Mac OS + iPhone notes |*


5. The file list updates — that's the branch's "folder."

**Direct links (no dropdown):**
- AD: https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Active-Directory  
- Linux: https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Linux  
- Pro Labs / Mac OS / iOS / LLM (Misc) : https://github.com/Skeletonephilim/Offensive-Security-Writeups/tree/Pro-Labs-/-AI-/-Miscellaneous  


# Active Directory Branch

Active Directory Boxes `LDAP 389/tcp, kerberos 88/tcp, msrpc 135/tcp, netBIOS 139/tcp, smb 445/tcp, WinRM 5985/tcp` with `Windows`, often with quirks (`Reel` phishing excel file, `Phantom` /mnt/ ve[...]

The `type` of box I enjoy the most.

# Linux Branch

`Linux` tagged boxes (can be `web application`, `dev exposure / web database abuse through path traversal`, `sudo misconfiguration`...)

# Pro Labs / AI / Miscellaneous

`Pro Labs` and `Mini-Pro Labs` with `C2 Sliver/Mythical` and `Active Directory`
`AI/ML` Challenges, `Prompt Injection` Challenges
`Purple` Theory
`Fundamentals` with `Offensive Perspective`
`Mac OS` penetration testing
`iPhone` penetration testing

# Box doctrine - Blind First (Grey/Black Box)

The writeups are blind-first: I work from my own enumeration, hypotheses, tooling, and debugging process. I do use AI assistance for explanation, Python helper scripts, methodology checks, and occ[...]
I use this method to humble myself although it's not as strict as my Black-Box repository.

## Redaction Policy

Public versions are sanitized.

I redact:

- Flags
- Passwords and hashes
- Reusable tokens or private keys
- Sensitive target-specific material that should not be republished

Commands and methodology are preserved where they are useful for learning and reproducibility.

## Workflow

My default workflow is:

1. Establish port and service truth
2. Cut the noise and identify the target's potential vulnerabilities / entry points
4. Try new techniques to improve my methodology
5. Pause to understand better the theoretical implications during an engagement
6. Try to understand the underlying processes
7. Think about the Blue Team side (that is absent in boxes) and what kind of workarounds I'd use in a real-life engagement
8. Review theoretical attack vectors that could be stopped/mitigated by a Security Engineer and improve my `Purple Team` skills
