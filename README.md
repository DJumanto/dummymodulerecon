# Reconnaissance and Digital Footprinting

## Topik Pembelajaran
- Kenapa harus recon?
- Reconnaissance and Digital Footprinting tools
- How to use NMAP as Service scanner
- How to use Rustscan as Port scanner
- Hot to use Gobuster as active directory scanning
- How to verify service vulnerability using NMAP script
- Introduction to WebKiller
- Introduction to WafWuf

## Mohon Dibaca Sebelum Lanjut

**&#9888;** **Peringatan:** Segala bentuk kegiatan _reconnaissance_ (pengintaian) tanpa izin pada pemilik layanan/sistem dapat dikenakan **sanksi pidana yang serius**. Segala informasi yang dipaparkan di sini **hanya untuk tujuan pembelajaran** dan **tidak boleh** digunakan untuk aktivitas ilegal.

## Kenapa harus recon?

<figure style="text-align: center;">
  <img src="imgs/image.png" alt="Recon" style="width: 50%; height: 50%;">
  <figcaption><i>DJumanto the recon guy</i></figcaption>
</figure>

Recon merupakan tahap paling awal dari penetration testing. Recon   merupakan tahap pengumpulan informasi terkait service, aplikasi, device, dan lain-lain yang akan kita uji keamanannya. Sederhananya, recon adalah tahap krusial agar kita tidak buta saat melaksanakan pengujian. Proses recon yang baik dengan tools yang tepat akan sangat mempermudah kita untuk menemukan celah ataupun informasi krusial terkait sistem.

## Reconnaissance and Digital Footprinting tools
Dalam melakukan scanning, tools yang tepat diperlukan agar kita bisa mendapatkan informasi yang sesuai dengan yang kita butuhkan.

Berikut merupakan tools yang umum digunakan oleh web penetration tester untuk mengumpulkan informasi:

| Logo | Tools     | Usage   |
|----|----------|-----------|
|![nmap](https://nmap.org/images/sitelogo-2x.png) | NMAP | Tools powerful yang dapat digunakan untuk port dan service scanning. Output hasil scanning cukup lengkap, dilengkapi dengan NSE script yang mempermudah untuk validasi vulnerability (dibahas lebih lanjut di bawah)     | 
|![rustscan](https://repository-images.githubusercontent.com/278933035/8389af00-ebe8-11ea-8c69-439b2883097a) | Rustscan    | Tools port scanning mirip nmap, tetapi berfokus pada port scanning. jauh lebih cepat dari nmap karena didukung oleh teknologi dari rust. Hasil dapat langsung di-pipe ke nmap, yang dapat digunakan untuk analisis lebih lanjut |
|![Gobuster](https://www.kali.org/tools/gobuster/images/gobuster-logo.svg)| Gobuster     | Directory scanner, tools yang common digunakan untuk mencari active directory sehingga scope analisis bisa lebih luas dan mempermudah mencari titik lemah pada aplikasi website |
|![WafW00f](https://miro.medium.com/v2/resize:fit:1400/1*X8NcJM997CvbbjfcelFD7Q.png)| WafW00f      | The Web Application Firewall Fingerprinting Toolkit. Toolkit yang sangat berguna untuk mendeteksi firewall firewall yang ada. Sehingga bisa lebih menghemat waktu dalam proses pentesting. |
|![WebKiller](https://media.geeksforgeeks.org/wp-content/uploads/20210705190259/20.PNG) | WebKiller     | Webkiller merupakan tools multifungsi yang bisa digunakan untuk Bypass Cloud Flare, Cms Detect, Trace Toute, Reverse IP, Port Scan, IP location Finder, Show HTTP Header, Find Shared DNS, Whois, DNS Lookup, Robots Scanner, Admin Page Finder, plugin detection, dan username guessing. Untuk sementara hanya mendukung wordpress untuk cms scanning.|

## How to use NMAP as Service scanner

Nmap merupakan tools berbasis lua yang digunakan untuk melakukan port dan service scanning pada sebuah aplikasi, opsi pada tools ini sangatlah luas. Karena selain hanya port dan scanning biasa, nmap dapat digunakan sebaik mungkin untuk melakukan scanning dengan meminimalisir deteksi oleh IDS (Intrusion Detection System) yang dimiliki oleh sistem. Selain itu nmap bisa digunakan untuk memvalidasi kelemahan pada sebuah service menggunakan NSE script yang akan dibahas mendekati akhir modul

### Installation
```sh
sudo apt update && sudo apt install nmap
```

### Basic Usage
```sh
nmap [Scan Type(s)] [Options] {target specification}
```

Berikut adalah commands yang umum digunakan pada nmap (silahkan overvasi lebih sendiri untuk option lainnya)

| Command | Use |
|--------|------|
| -h    | Informasi tentang penggunaan nmap |
| -sS | Syn Scan |
| -sU | UDP Scan |
| -O | OS Detection |
| -sV | Deteksi versi dari service yang terdapat pada target |
| -V | Mendapatkan informasi lebih lengkap (slower) |
| -vv | Sama, tapi lebih lengkap |
| -oN | Simpan hasil scanning pada file tertentu |
| -oG | simpan hasil ke grep-able format |
| -A | Mode agressive untuk memperdalam service scanning (slower)
| -p [port] atau -p [portstart - portend] | Digunakan untuk menentukan range port|
| -p- | scan semua port (slower)
| -T[0-5] | Menentukan timing packet yang dikirim kan (semakin besar semakin cepat) |
|-sn | ping check, digunakan semata mata hanya untuk mengecek apakah host menyala atau mati tanpa perlu mengecek semua port

Contoh penggunaan adalah sebagai berikut:

```sh
nmap -sV -sC -oN nmaplog.log 10.10.10.1

# -sV: Mengetahui versi dari service
# -sC: mengtahui informasi terkait service menggunakan default nmap script
# -oN nmaplog.log: menyimpan hasil scanning pada file nmaplog.log
```

Contoh lain:
```sh
nmap -p 21-1337 -T1 10.10.10.1

# -p: scan port dari 21 sampai 1337
# -T1: set timing menjadi mode paling lambat
```

contoh lain:
```sh
nmap -sn 192.168.0.0/16

# -sn: ping scan
# 192.168.0.0/16: scan semua ip 192.168.0.0 yang satu subnet dengan subnet mask 255.255.0.0
```

contoh hasil output dari nmap
```sh
# Nmap 7.93 scan initiated Fri Apr 28 23:40:37 2023 as: nmap -sV -sC -oN nmap/initials 10.10.99.99
Nmap scan report for 10.10.99.99
Host is up (0.21s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.5
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3ad834149e95d168d3b0f057be2c0ae (RSA)
|   256 f8277d642997e6f865546522f7c81d8a (ECDSA)
|_  256 5a06edebb6567e4c01ddeabcbafa3379 (ED25519)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100003  2,3,4       2049/udp   nfs
|   100003  2,3,4       2049/udp6  nfs
|   100005  1,2,3      35841/tcp   mountd
|   100005  1,2,3      46034/udp   mountd
|   100005  1,2,3      50105/tcp6  mountd
|   100005  1,2,3      54672/udp6  mountd
|   100021  1,3,4      33494/udp6  nlockmgr
|   100021  1,3,4      36727/tcp6  nlockmgr
|   100021  1,3,4      37027/tcp   nlockmgr
|   100021  1,3,4      56620/udp   nlockmgr
|   100227  2,3         2049/tcp   nfs_acl
|   100227  2,3         2049/tcp6  nfs_acl
|   100227  2,3         2049/udp   nfs_acl
|_  100227  2,3         2049/udp6  nfs_acl
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp open  nfs_acl     2-3 (RPC #100227)
Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h40m00s, deviation: 2h53m12s, median: 0s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-04-28T16:40:54
|_  start_date: N/A
|_nbstat: NetBIOS name: KENOBI, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: kenobi
|   NetBIOS computer name: KENOBI\x00
|   Domain name: \x00
|   FQDN: kenobi
|_  System time: 2023-04-28T11:40:54-05:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Apr 28 23:41:04 2023 -- 1 IP address (1 host up) scanned in 27.11 seconds
```

## How to use Rustscan as Port scanner

Rustscan merupakan salah tools protscanning yang jauh lebih cepat dari Nmap, tetap dengan keterbatasan pada pilihannya. seperti tiadanya service depth scanning, vulenerbaility checking, dll. Namun ketika kita hanya memerlukan informasi terkait port yang ada dengan cepat lalu hanya mengoperasikan nmap pada port port tertentu yang telah kita dapatkan, maka Rustscan adalah sahabat untuk orang tidak sabaran seperti kita.

### Installation

Pastikan telah menginstall NMAP

lalu

```sh
wget -q https://github.com/RustScan/RustScan/releases/download/2.0.1/rustscan_2.0.1_amd64.deb

dpkg -i rustscan_2.0.1_amd64.deb


rustscan --version
```

rustscan telah terinstall

### Basic Usage

```sh
rustscan [FLAGS] [OPTIONS] [-- <command>...]
```

contoh penggunaan:
```sh
rustscan -a 127.0.0.1 -r 1-80

#scan port pada 127.0.0.1 dengan range ip dari 1-80

rustscan -a 127.0.0.1 -p 80

#scan port pada 127.0.0.1 pada port 80

rustscan -a 127.0.0.1 -p 80,443,21,22,2121,2222

#scan port pada 127.0.0.1 pada port 80, 443, 21, 22, 2121, 2222
```

contoh lain:
```sh
rustscan -a 127.0.0.1 -r 1-80 -b 2

# Port scanning pada range port 1-80 dengan batch scanning 2 port tiap iterasinya. (memperlambat proses scanning)

rustscan -a 'hosts.txt'

# Scan beberapa host sekalius yang terdapat pada file hosts.txt
```

Yang keren dari rustscan adalah, commandnya bisa dikombinasikan dengan option pada nmap, berikut contohnya:

```sh
rustscan -a 127.0.0.1 -r 1-20 -- -A -sC -sV -oN initials

# Port yang discan adalah 1-20. Lalu lakukan port service checking menggunakan command -A -sC -sV -oN (-- merupakan separator untuk meng-pipe hasil scan ke nmap command)
```

## How to use Gobuster as active directory scanning

Gobuster merupakan tools yang berguna untuk scanning active directory yang terdapat pada suatu web application. Menemukan active directory pada suatu aplikasi web membuat tester memiliki jangkauan yang lebih luas untuk menganalisis kelemahan yang terdapat pada suatu web. Sederhananya, semakin besar fungsionalitas, semakin potensial sekumpulan developer melakukan kesalahan implementasi logic atau konfigutasi.

### Installation
```sh
sudo apt install gobuster -y
```

### Basic Usage
```sh
gobuster [command]
```

Selain untuk active directory scanning, gobuster juga memiliki kemampuan untuk scanning subdomain pada suatu domain, berikut adalah list dari kemampuan scanning dari gobuster:

```txt
Available Commands:
dir         Uses directory/file enumeration mode
dns         Uses DNS subdomain enumeration mode
fuzz        Uses fuzzing mode. Replaces the keyword FUZZ in the URL, Headers and the request body
gcs         Uses gcs bucket enumeration mode
help        Help about any command
s3          Uses aws bucket enumeration mode
tftp        Uses TFTP enumeration mode
version     shows the current version
vhost       Uses VHOST enumeration mode (you most probably want to use the IP address as the URL parameter)
```

Pada modul kali ini kita akan berfokus pada director scanning / enumeration.

Contoh penggunaan:

```sh
gobuster dir -u {URL} -w {WordLists}

# Melakukan directory listing menggunakan wordlist

gobuster dns -u {URL} -w {WordList}

# Melakukan subdomain listing menggunakan wordlist
```
contoh output dari hasil gobuster:
```sh
gobuster dir -u https://{NONEXIST}.com/ -w /usr/share/wordlists/rockyou.txt -o gobuster-output.log
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     https://{NONEXIST}.com/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/rockyou.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/12345                (Status: 200) [Size: 148529]
/password             (Status: 200) [Size: 143908]
/12345678             (Status: 200) [Size: 140969]
/123456789            (Status: 200) [Size: 146472]
/1234567              (Status: 200) [Size: 140891]
/princess             (Status: 200) [Size: 146444]
/123456               (Status: 200) [Size: 144954]
/abc123               (Status: 200) [Size: 140777]
/iloveyou             (Status: 200) [Size: 140943]
/rockyou              (Status: 200) [Size: 152007]
/nicole               (Status: 200) [Size: 144433]
/daniel               (Status: 200) [Size: 157431]
/babygirl             (Status: 200) [Size: 139766]
/lovely               (Status: 200) [Size: 120512]
/jessica              (Status: 200) [Size: 147014]
/111111               (Status: 200) [Size: 149895]
/ashley               (Status: 200) [Size: 155243]
/qwerty               (Status: 200) [Size: 140786]
/000000               (Status: 200) [Size: 140820]
/654321               (Status: 200) [Size: 139620]
/monkey               (Status: 200) [Size: 207348]
/iloveu               (Status: 200) [Size: 140813]
/michael              (Status: 200) [Size: 170539]
/tigger               (Status: 200) [Size: 145761]
/sunshine             (Status: 200) [Size: 146218]
/michelle             (Status: 200) [Size: 169810]
/chocolate            (Status: 200) [Size: 141082]
/friends              (Status: 429) [Size: 9270]
/anthony              (Status: 429) [Size: 9270]
/butterfly            (Status: 429) [Size: 9270]
/password1            (Status: 429) [Size: 9270]
/soccer               (Status: 200) [Size: 140817]
/purple               (Status: 429) [Size: 9270]
/angel                (Status: 429) [Size: 9270]
/jordan               (Status: 429) [Size: 9270]
/justin               (Status: 429) [Size: 9270]
/fuckyou              (Status: 429) [Size: 9270]
/123123               (Status: 429) [Size: 9270]
/football             (Status: 429) [Size: 9270]
/loveme               (Status: 429) [Size: 9270]
/secret               (Status: 429) [Size: 9270]
/liverpool            (Status: 429) [Size: 9270]
/andrea               (Status: 429) [Size: 9270]
/carlos               (Status: 429) [Size: 9270]
/jennifer             (Status: 429) [Size: 9270]
```

Untuk wordlists kalian bisa mengambil dari repository 
[wordlist](https://github.com/3ndG4me/KaliLists.git)

Alternatif lain, kalian bisa menggunakan dirbuster sebagai pengganti gobuster. Dirbuster telah menyediaan wordlist dari common, small, sampai ukuran big dengan puluhan ribu possible directory wordlists.

# How to verify service vulnerability using NMAP script

Anggap kalian telah berhasil menemukan lists of service yang ada pada suatu sistem. Lalu kalian menemukan sumber yang mengatakan bahwa versi dari service yang kalian temukan vulnerable terhadap suatu serangan, tetapi kalian belum bsia memastikan bahwa serangan tersebut benar benar bekerja atau tidak. Disinilah salah satu kemampuan dari NMAP bisa kalian gunakan. please welcome, **NSE Script**

>_**Peringatan**, Sript NSE bagaimanapun juga merupakan tindakan penyerangan terhadap service, jangan gunakan tanpa seizin pemilik layanan_

## NSE Script
Nmap Scripting Engine (NSE) merupakan kumpulan kode bawaan dari NMAP yang berfungsi untuk praktik reconnaissance. Berguna dari sekedar validasi kelemahan, hingga otomasi serangan. Penggunaan dari NSE script bisa mempermudah kalian untuk memverifikasi apakah suatu service benar-benar memiliki celah atau tidak.

## Scripts type available
- **safe**: Tidak akan mempengaruhi target
- **intrusive**: Kemungkinan besar akan mempengaruhi target
- **vuln**: Memindai kerentanan
- **exploit**: Mencoba untuk mengeksploitasi kerentanan
- **auth**: Mencoba untuk melewati autentikasi untuk layanan yang berjalan (misalnya Masuk ke server FTP secara anonim)
- **brute**: Mencoba untuk melakukan brute force pada kredensial untuk layanan yang berjalan
- **discovery**: Mencoba untuk meminta informasi lebih lanjut tentang jaringan dari layanan yang berjalan (misalnya meminta server SNMP)
- **Malware**: Menguji apakah platform target terinfeksi oleh malware atau backdoor

Untuk informasi lebih lengkap mengenai jenis script, kalian bisa mengunjungi website dari [NMAP](https://nmap.org/book/nse-usage.html)


## Basic usage
```sh
nmap {URL} -p 80 --script {Script apa yang ingin digunakan} --script-args {Argument dari script}
```
Argumen dari masing-masing script bias berbeda beda, kalian bisa mengeceknya di web [NMAP-NSE-script](https://nmap.org/nsedoc/scripts/). Klik salah satu script, dan akan muncul argument yang diperlukan oleh masing-masingg script

Contoh penggunaan NSE script dengan argument:
```sh
nmap {URL} -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'
```

## Script Searching
Karena script dari NSE sangatlah banyak, kalian bisa mencari kumpulan script dari nmap pada directory ``/usr/share/nmap/scripts/script.db``

Untuk teknik mencari script secara spesifik, kalian bisa menggunakan kombinasi **cat** dan **grep**:
```sh
cat /usr/share/nmap/scripts/script.db | grep {parameter}

untuk multiple parameter

cat /usr/share/nmap/scripts/script.db | grep {parameter1} | grep {parameter2}
```

Contoh hasil:
```sh
cat /usr/share/nmap/scripts/script.db | grep smb | grep malware

Entry { filename = "smb-double-pulsar-backdoor.nse", categories = { "malware", "safe", "vuln", } }
```

Contoh mencari script untuk verifikasi apakah smb server memiliki kelemahan eternal blue:
```sh
cat /usr/share/nmap/scripts/script.db | grep ms17-010

Entry { filename = "smb-vuln-ms17-010.nse", categories = { "safe", "vuln", } }
```

Dan berikut adalah hasil apabila sebuah service positif dapat diserang menggunakan exploit yang speifik:
```sh
Host script results:
| smb-vuln-ms17-010:
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|
|     Disclosure date: 2017-03-14
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|_      https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
```

# Introduction to WebKiller

![WebKiller](https://media.geeksforgeeks.org/wp-content/uploads/20210705190259/20.PNG)

Webkiller merupakan interactive tools yang dapat kalian gunakan untuk melakukan reconnaissance. Tools ini berguna untuk melakukan:

1. Bypass Cloud Flare
2. Cms Detect
3. Trace Toute
4. Reverse IP
5. Port Scan
6. IP Location Finder
7. Show HTTP Header
8. Find Shared DNS
9. Whois
10. DNS Lookup
11. Robots Scanner
12. Admin Page Finder

Kemampuan lain dari webkiller adalah yakni bisa melakukan beberapa scanning pada CMS wordpress, seperti top 1000 plugin dan scan username.

# Introducion to WafW00f

WafW00f merupakan tools untuk melakukan scanning firewall apa yang terdapat di depan sebuah service. Kenapa perlu scanning firewall? terkadang sebuah serangan yang possible terjadi tidak dapat dieksekusi dikarenakan terdapat sebuah dinding di depan service tersebut. sehingga mengetahui firewall apa yang terpasang dan menentukan apakah firewall tersebut dapat dibypass atau tidak akan sangat menghemat waktu kalian dalam melakukan serangan nantinya.

## Basic Usage
```sh
wafw00f url1 url2 ... urln [options] {args}
```
List options:
```sh
Options:
  -h, --help            show this help message and exit
  -v, --verbose         Enable verbosity, multiple -v options increase
                        verbosity
  -a, --findall         Find all WAFs which match the signatures, do not stop
                        testing on the first one
  -r, --noredirect      Do not follow redirections given by 3xx responses
  -t TEST, --test=TEST  Test for one specific WAF
  -o OUTPUT, --output=OUTPUT
                        Write output to csv, json or text file depending on
                        file extension. For stdout, specify - as filename.
  -f FORMAT, --format=FORMAT
                        Force output format to csv, json or text.
  -i INPUT, --input-file=INPUT
                        Read targets from a file. Input format can be csv,
                        json or text. For csv and json, a `url` column name or
                        element is required.
  -l, --list            List all WAFs that WAFW00F is able to detect
  -p PROXY, --proxy=PROXY
                        Use an HTTP proxy to perform requests, examples:
                        http://hostname:8080, socks5://hostname:1080,
                        http://user:pass@hostname:8080
  -V, --version         Print out the current version of WafW00f and exit.
  -H HEADERS, --headers=HEADERS
                        Pass custom headers via a text file to overwrite the
                        default header set.
```

Contoh hasil output:
```sh
wafw00f {SomeURL}.net

                   ______
                  /      \
                 (  Woof! )
                  \  ____/                      )
                  ,,                           ) (_
             .-. -    _______                 ( |__|
            ()``; |==|_______)                .)|__|
            / ('        /|\                  (  |__|
        (  /  )        / | \                  . |__|
         \(_)_))      /  |  \                   |__|

                    ~ WAFW00F : v2.2.0 ~
    The Web Application Firewall Fingerprinting Toolkit

[*] Checking https://{SomeURL}.net
[+] The site https://{SomeURL}.net is behind Cloudflare (Cloudflare Inc.) WAF.
```