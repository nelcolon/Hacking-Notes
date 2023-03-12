```bash
$ nmap -vv -p 22,80 -sCV -A 10.129.223.3
Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-07 20:45 GMT
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
Initiating Ping Scan at 20:45
Scanning 10.129.223.3 [2 ports]
Completed Ping Scan at 20:45, 0.01s elapsed (1 total hosts)
Initiating Connect Scan at 20:45
Scanning soccer.htb (10.129.223.3) [2 ports]
Discovered open port 22/tcp on 10.129.223.3
Discovered open port 80/tcp on 10.129.223.3
Completed Connect Scan at 20:45, 0.00s elapsed (2 total ports)
Initiating Service scan at 20:45
Scanning 2 services on soccer.htb (10.129.223.3)
Completed Service scan at 20:45, 6.01s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.223.3.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.20s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.01s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
Nmap scan report for soccer.htb (10.129.223.3)
Host is up, received syn-ack (0.0072s latency).
Scanned at 2023-03-07 20:45:47 GMT for 7s

PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ad:0d:84:a3:fd:cc:98:a4:78:fe:f9:49:15:da:e1:6d (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQChXu/2AxokRA9pcTIQx6HKyiO0odku5KmUpklDRNG+9sa6olMd4dSBq1d0rGtsO2rNJRLQUczml6+N5DcCasAZUShDrMnitsRvG54x8GrJyW4nIx4HOfXRTsNqImBadIJtvIww1L7H1DPzMZYJZj/oOwQHXvp85a2hMqMmoqsljtS/jO3tk7NUKA/8D5KuekSmw8m1pPEGybAZxlAYGu3KbasN66jmhf0ReHg3Vjx9e8FbHr3ksc/MimSMfRq0lIo5fJ7QAnbttM5ktuQqzvVjJmZ0+aL7ZeVewTXLmtkOxX9E5ldihtUFj8C6cQroX69LaaN/AXoEZWl/v1LWE5Qo1DEPrv7A6mIVZvWIM8/AqLpP8JWgAQevOtby5mpmhSxYXUgyii5xRAnvDWwkbwxhKcBIzVy4x5TXinVR7FrrwvKmNAG2t4lpDgmryBZ0YSgxgSAcHIBOglugehGZRHJC9C273hs44EToGCrHBY8n2flJe7OgbjEL8Il3SpfUEF0=
|   256 df:d6:a3:9f:68:26:9d:fc:7c:6a:0c:29:e9:61:f0:0c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIy3gWUPD+EqFcmc0ngWeRLfCr68+uiuM59j9zrtLNRcLJSTJmlHUdcq25/esgeZkyQ0mr2RZ5gozpBd5yzpdzk=
|   256 57:97:56:5d:ef:79:3c:2f:cb:db:35:ff:f1:7c:61:5c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ2Pj1mZ0q8u/E8K49Gezm3jguM3d8VyAYsX0QyaN6H/
80/tcp open  http    syn-ack nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Soccer - Index 
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:45
Completed NSE at 20:45, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.76 seconds
```

## Web enumeration
```bash
$ feroxbuster --url http://soccer.htb --wordlist /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt 

 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.3.3
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://soccer.htb
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.3.3
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔃  Recursion Depth       │ 4
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Cancel Menu™
──────────────────────────────────────────────────
301        7l       12w      178c http://soccer.htb/tiny
301        7l       12w      178c http://soccer.htb/tiny/uploads
[####################] - 10s   186840/186840  0s      found:2       errors:0      
[####################] - 7s     62280/62280   8095/s  http://soccer.htb
[####################] - 8s     62280/62280   7117/s  http://soccer.htb/tiny
[####################] - 8s     62280/62280   7052/s  http://soccer.htb/tiny/uploads
```

## Resources:
https://github.com/prasathmani/tinyfilemanager/wiki/Security-and-User-Management