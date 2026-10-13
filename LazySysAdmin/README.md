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

### SMB Service Enumeration

We used Nmap with the `-sV` option to identify the services and their versions running on the target machine. The scan revealed SMB (Samba) on ports 139 and 445.

SMB is a network protocol used for sharing files and resources between devices. We will investigate the SMB service to check whether any shared resources are accessible without authentication.

![Nmap Scan Results](screenshots/2.png)

### SMB Anonymous Access

We successfully connected to the SMB service without providing a password. This indicates that the service allows access without authentication. We can now explore the available shared resources.

![SMB Anonymous Access](screenshots/3.png)
