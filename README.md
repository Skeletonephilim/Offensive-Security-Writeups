# Offensive Security Writeups

This repository contains my offensive security lab writeups, mostly from Hack The Box.

The writeups are blind-first: I work from my own enumeration, hypotheses, tooling, and debugging process. I do use AI assistance for explanation, Python helper scripts, methodology checks, and occasional nudges when blocked, but I do use writeups/walkthroughs.
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
5. Pause to understand certain theoretical concepts
6. Try to understand the underlying processes
7. Think about the Blue Team side (that is absent in boxes) and what kind of workarounds I'd use in a real-life engagement
8. Review theoretical attack vectors that could be stopped/mitigated by a Security Engineer
