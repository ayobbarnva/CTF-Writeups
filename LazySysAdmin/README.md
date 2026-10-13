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

### WordPress Configuration File Discovery

Using the shell we obtained, we accessed the WordPress configuration files and retrieved `wp-config.php` using the `GET` command. By examining the file contents, we discovered the following credentials:

- **Username:** `Admin`
- **Password:** `TogieMYSQL12345^^`

![WordPress Configuration File](screenshots/4.png)

### WordPress Login

After discovering the username and password in the `wp-config.php` file, we needed to determine where these credentials could be used.

Using **Dirb**, we discovered the following WordPress administration URL:

`http://192.168.43.166/wordpress/wp-admin/user/`

We entered the credentials we had found:

- **Username:** `Admin`
- **Password:** `TogieMYSQL12345^^`

We successfully logged in to the WordPress interface using these credentials.

![Successful WordPress Login](screenshots/login_wp.png)
