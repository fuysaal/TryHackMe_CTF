```bash
rustscan -a 10.10.123.74 

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
I scanned my computer so many times, it thinks we're dating.

[~] The config file is expected to be at "/root/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.10.123.74:80
[~] Starting Script(s)
[~] Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-24 08:42 EDT
Initiating Ping Scan at 08:42
Scanning 10.10.123.74 [4 ports]
Completed Ping Scan at 08:42, 0.38s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 08:42
Completed Parallel DNS resolution of 1 host. at 08:42, 0.50s elapsed
DNS resolution of 1 IPs took 0.50s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 08:42
Scanning 10.10.123.74 [1 port]
Discovered open port 80/tcp on 10.10.123.74
Completed SYN Stealth Scan at 08:42, 0.19s elapsed (1 total ports)
Nmap scan report for 10.10.123.74
Host is up, received reset ttl 63 (0.33s latency).
Scanned at 2025-07-24 08:42:58 EDT for 0s

PORT   STATE SERVICE REASON
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 1.17 seconds
           Raw packets sent: 5 (196B) | Rcvd: 2 (84B)
```

---

```bash

nmap -Pn -sV -A 10.10.190.125 -p 80

Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-24 09:16 EDT
Nmap scan report for 10.10.190.125
Host is up (0.091s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Welcome to FUEL CMS
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/fuel/
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.4 (97%), Android 9 - 10 (Linux 4.9 - 4.14) (96%), Linux 3.2 - 4.14 (96%), Linux 5.4 (96%), Linux 4.15 (95%), Linux 4.15 - 5.19 (95%), Linux 3.10 - 3.13 (95%), Linux 2.6.32 - 3.10 (95%), Linux 3.10 - 4.11 (94%), Linux 3.13 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   84.55 ms 10.9.0.1
2   84.64 ms 10.10.190.125

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.05 seconds

```

