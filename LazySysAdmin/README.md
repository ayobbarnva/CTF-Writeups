## Overview

LazySysAdmin: 1 is a beginner-friendly boot-to-root CTF challenge hosted on VulnHub.
It simulates a Linux server with security weaknesses caused by poor system administration.
Participants must enumerate services, discover exposed information, and identify vulnerabilities.
The ultimate goal is to gain root access to the target system.
### Network Discovery

Using `netdiscover`, we identified two active devices on the local network:

- `192.168.43.220` — Our Kali Linux machine.
- `192.168.43.166` — The target virtual machine (LazySysAdmin).

The target IP address identified for further enumeration is `192.168.43.166`.

![Netdiscover Results](screenshots/one.png)
