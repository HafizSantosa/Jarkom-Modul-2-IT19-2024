# Laporan Resmi Praktikum Jaringan Komputer Modul 2 Kelompok IT19

| NRP | Nama Anggota |
|-----|--------------|
| 5027221061 | Hafiz Akmaldi Santosa |
| 5027221049 | Arsyad Rizantha Maulana Salim |

## 0. Membuat jaringan komputer dengan topologi nomor 4 

Berikut adalah topologi yang telah dibuat berdasarkan topologi nomor 4.

![](/image/Topologi%204.png "test")

Dalam topologi ini terdapat node *Pochinki* server utama, *Georgopol* sebagai server backup, *Rozhok*, *School*, dan *FerryPier* sebagai node client, *Mylta* sebagai load balancer, serta *severny*, *stalber*,dan *lipovka* sebagai web server.

## 1. Membuat *Pochinki* sebagai DNS Master dan *Georgopol* sebagai DNS Slave yang mengarah ke *Pochinki*.

#### DNS Master
Untuk membuat sebuah node menjadi DNS Master, kita perlu meng-install `bind`. Pastikan node sudah dapat mengakses internet.
```
apt-get update
apt-get install bind9 -y
```
Setelah berhasil meng-install bind, selanjutnya kita akan menambahkan konfigurasi untuk DNS Master di file config pada folder bind.
```
nano /etc/bind/named.conf.local
```
Tambahkan konfigurasi berikut kedalam `named.conf.local`.
```
zone "Pochinki.com" {
    type master;
	file "/etc/bind/jarkom/pochinki.com";
	};
```
Kemudian kita akan membuat folder berdasarkan path yang kita masukkan tadi di file config bind, lalu copy template config dari bd.local pada folder bind.
```
mkdir /etc/bind/jarkom
cp /etc/bind/db.local /etc/bind/jarkom/pochinki.com
```
Masuk ke dalam file `pochinki.com` lalu sesuaikan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pochinki.com. root.pochinki.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      pochinki.com.
@       IN      A       10.73.1.3 //IP Address Pochinki
@       IN      AAAA    ::1
```
Kemudian restart bind pada node agar config tadi bisa berjalan.
```
service bind9 restart
```
#### DNS Slave
Selanjutnya kita perlu untuk menambahkan config pada file `named.config.local` untuk menjadikan *Georgopol* sebagai Slave.
```
zone "pochinki.com" {
  type master;
    notify yes;
  	also-notify { 10.73.1.2; };
  	allow-transfer { 10.73.1.2; };
  	file "/etc/bind/jarkom/pochinki.com";
	};
```
Kemudian kita akan berpindah ke node *Georgopol*, dimana kita akan mengubah config di file `named.config.local` sebagai berikut.
```
zone "pochinki.com" {
    type slave;
    masters { 10.73.1.3; };
    file "/var/lib/bind/pochinki.com";
};
```
Berbeda dengan config di *Pochinki*, disini kita menggunankan `type slave;` karena kita bermaksud membuat *Georgopol* sebagai slave.

## 2. Membuat domain `airdrop.it19.com` yang mengarah ke *Stalber*.

Karena disini *Pochinki* adalah DNS Master, maka kita akan memasukan config di dalam *Pochinki* namun mengarahkan IP Address ke node *Stalber*

```
nano /etc/bind/named.conf.local
```
Tambahkan konfigurasi berikut kedalam `named.conf.local`.
```
zone "airdrop.it19.com" {
	type master;
	file "/etc/bind/jarkom/airdrop.it19.com";
	};
```
Lalu copy template config dari bd.local pada folder bind.
```
cp /etc/bind/db.local /etc/bind/jarkom/airdrop.it19.com
```
Masuk ke dalam file `airdrop.it19.com` lalu sesuaikan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it19.com. root.airdrop.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it19.com.
@       IN      A       10.73.3.3
www     IN      CNAME   airdrop.it19.com.
@       IN      AAAA    ::1
```
Kemudian restart bind pada node agar config tadi bisa berjalan.
```
service bind9 restart
```

## 3. Membuat domain `redzone.it19.com` yang mengarah ke *Severny*.

Karena disini *Pochinki* adalah DNS Master, maka kita akan memasukan config di dalam *Pochinki* namun mengarahkan IP Address ke node *Severny*

```
nano /etc/bind/named.conf.local
```
Tambahkan konfigurasi berikut kedalam `named.conf.local`.
```
zone "redzone.it19.com" {
	type master;
	file "/etc/bind/jarkom/redzone.it19.com";
	};
```
Lalu copy template config dari bd.local pada folder bind.
```
cp /etc/bind/db.local /etc/bind/jarkom/redzone.it19.com
```
Masuk ke dalam file `redzone.it19.com` lalu sesuaikan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it19.com. root.redzone.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it19.com.
@       IN      A       10.73.3.2
www     IN      CNAME   redzone.it19.com.
@       IN      AAAA    ::1
```
Kemudian restart bind pada node agar config tadi bisa berjalan.
```
service bind9 restart
```

## 4. Membuat domain `loot.it19.com` yang mengarah ke *Mylta*.

Karena disini *Pochinki* adalah DNS Master, maka kita akan memasukan config di dalam *Pochinki* namun mengarahkan IP Address ke node *Mylta*

```
nano /etc/bind/named.conf.local
```
Tambahkan konfigurasi berikut kedalam `named.conf.local`.
```
zone "loot.it19.com" {
	type master;
	file "/etc/bind/jarkom/loot.it19.com";
	};
```
Lalu copy template config dari bd.local pada folder bind.
```
cp /etc/bind/db.local /etc/bind/jarkom/loot.it19.com
```
Masuk ke dalam file `loot.it19.com` lalu sesuaikan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loot.it19.com. root.loot.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      loot.it19.com.
@       IN      A       10.73.2.5
www     IN      CNAME   loot.it19.com.
@       IN      AAAA    ::1
```
Kemudian restart bind pada node agar config tadi bisa berjalan.
```
service bind9 restart
```

## 5. Memastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di *Erangel*

Agar sebuah node dapat diakses suatu client, kita harus menghubungkan kedua node tersebut ke internet. Pada Erangel, jalankan command berikut:
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.73.0.0/16
```
kemudian masukkan `nameserver 192.168.122.1` pada file config `resolv.conf`
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```
lakukan hal yang sama ke semua node agar dapat terhubung satu dengan lainnya. Untuk melakukan cek apakah sudah bisa terhubung atau belum bisa dengan menggunakan command untuk ping suatu domain seperti `ping google.com`.

## 6. *Pointer record* pada `redzone.it19.com`

#### *Set pointer record*
Untuk menambahkan *pointer record* pada domain `redzone.it19.com` kita akan menambah lagi pada file config `named.config.local` sebagai berikut:
```
zone "2.3.73.10.in-addr.arpa" {
  type master;
  file "/etc/bind/jarkom/2.3.73.10.in-addr.arpa";
};
```
copy template config dari bd.local pada folder bind.
```
cp /etc/bind/db.local /etc/bind/jarkom/3.73.10.in-addr.arpa
```
Masuk ke dalam file `3.73.10.in-addr.arpa` lalu sesuaikan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it19.com. root.redzone.it19.com. (
                     2022100601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.73.10.in-addr.arpa.   IN      NS      redzone.it19.com.
2                       IN      PTR     redzone.it19.com.
```
Kemudian restart bind pada node agar config tadi bisa berjalan.
```
service bind9 restart
```
#### Testing
Setelah melakukan setup tadi, kita akan cek apakah berhasil. Melalui client kita akan cek menggunakan command host. Namun sebelumnya kita akan install dengan command berikut:
```
apt-get update
apt-get install dnsutils
```
Kemudian jalankan command host untuk cek *pointer record*
```
host -t PTR 10.73.3.2
```

## 7. Buat *Georgopol* sebagai slave semua master di *Pochinki*.

Pada node *Georgopol* kita akan menambahkan config di file `named.config.local` sebagai berikut.
```
zone "airdrop.it19.com" {
    type slave;
    masters { 10.73.3.3; };
    file "/var/lib/bind/airdrop.it19.com";
};

zone "redzone.it19.com" {
    type slave;
    masters { 10.73.3.2; };
    file "/var/lib/bind/redzone.it19.com";
};

zone "loot.it19.com" {
    type slave;
    masters { 10.73.2.5; };
    file "/var/lib/bind/loot.it19.com";
};
```
IP Address master tetap mengarah ke node web server masing-masing.
Kemudian restart bind pada node agar config tadi bisa berjalan.
```
service bind9 restart
```

## 8. Membuat subdomain `medkit.airdrop.xxxx.com`
Untuk membuat sebuah subdomain pada domain tertentu, kita perlu menambahkan pada file config domain.
```
nano /etc/bind/jarkom/airdrop.it19.com
```
Lalu tambahkan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     airdrop.it19.com. root.airdrop.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it19.com.
@       IN      A       10.73.3.3
www     IN      CNAME   airdrop.it19.com.
medkit  IN      A       10.73.3.4         // Tambahkan line ini
@       IN      AAAA    ::1
```
Lalu restart bind9 dengan `service bind9 restart`.

## 9. Membuat subdomain `siren.redzone.xxxx.com` dan mendelegasikan subdomain tersebut ke Georgopol
### Konfigurasi Ponchiki:
Yang pertama adalah jalankan command berikut:
```
mkdir /etc/bind/siren
```
Kemudian
```
cp /etc/bind/redzone/redzone.it19.com /etc/bind/siren/siren.redzone.it19.com
nano /etc/bind/siren/siren.redzone.it19.com
```
Lalu tambahkan config sebagai berikut:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     redzone.it25.com. root.redzone.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it19.com.
@       IN      A       10.73.2.3
www     IN      CNAME   redzone.it19.com.
siren   IN      A       10.73.4.2
ns1     IN      A       10.73.4.2
siren   IN      NS      ns1
@       IN      AAAA    ::1
```
Lalu edit file `etc/bind/named.conf.options` pada Pochinki dan tambahkan line berikut:
```
allow-query{any;};
```
Kemudian jalankan `nano /etc/bind/named.conf.local`, lalu tambahkan konfigurasi berikut:
```
zone "siren.redzone.it29.com" {
    type master;
    file "/etc/bind/siren/siren.redzone.it29.com";
    allow-transfer { 10.73.4.2; };
};
```
Lalu restart bind9 dengan `service bind9 restart`.

### Konfigurasi Georgopol
Langkah pertama adalah edit file `/etc/bind/named.conf.options` pada Georgopol
```
nano /etc/bind/named.conf.options
```
Lalu tambahkan line berikut:
```
allow-query{any;};
```
Kemudian jalankan `nano /etc/bind/named.conf.local`, dan tambahkan konfigurasi seperti berikut:
```
zone "siren.redzone.it19.com" {
    type master;
    file "/etc/bind/siren/siren.redzone.it19.com";
};
```
Jalankan command:
```
mkdir /etc/bind/siren
cp /etc/bind/db.local /etc/bind/siren/siren.redzone.it19.com
```
Buka dan edit konfigurasi file `/etc/bind/siren/siren.redzone.it19.com`:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     siren.redzone.it19.com. root.siren.redzone.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      siren.redzone.it19.com.
@       IN      A       10.73.4.2
www     IN      CNAME   siren.redzone.it19.com.
@       IN      AAAA    ::1
```
Restart bind9 dengan `service bind9 restart`.

## 10. Buat subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com
Jalankan command:
```
nano /etc/bind/siren/siren.redzone.it19.com
```
Lalu edit menjadi seperti di bawah ini:
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     siren.redzone.it19.com. root.siren.redzone.it19.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      siren.redzone.it19.com.
@       IN      A       10.73.4.2
www     IN      CNAME   siren.redzone.it19.com.
log     IN      A       10.73.4.2
www.log IN      CNAME   siren.redzone.it19.com.
@       IN      AAAA    ::1
```
Terakhir restart bind9 dengan `service bind9 restart`.

## 11. Konfigurasi agar warga Erangel yang berada diluar Pochinki dapat mengakses jaringan luar melalui DNS Server Pochinki
Yang pertama yaitu jalankan command:
```
nano /etc/bind/named.conf.options
```
Kemudian uncomment forwarder seperti pada gambar, Untuk IP diisi dengan IP Erangel.

![](image/No%2011.png)

Lalu restart bind9 dengan `service bind9 restart`.

## 12. Melakukan deploy web untuk melihat available sites.

instalasi apache2 dan nginx

```
apt-get update
apt-get install lynx apache2 php libapache2-mod-php7.0 nginx -y
```

masuk ke /etc/apache2/sites-available lalu copy file defaultnya

```
cp 000-default.conf jarkom-it19.conf
```

ubah isi filenya jarkom-it19.conf

Hanya ubah bagian ini saja

```
<VirtualHost *:8080>  //ubah portnya menjadi 8080

ServerAdmin webmaster@localhost
DocumentRoot /var/www/jarkom-it19 //ubah document rootnya
```

Tambahkan port 8080 di /etc/apache2/ports,conf

```
Listen 8080
```

Aktifkan konfigurasi

```
a2ensite jarkom-it19.conf
```

Restart apache

```
service apache2 restart
```

buat directory jarkom-it19 di /var/www lalu tambahkan index phpnya

```
mkdir jarkom-it19


<?php
$hostname = gethostname();
$date = date('Y-m-d H:i:s');
$php_version = phpversion();
$username = get_current_user();



echo "Hello World!<br>";
echo "Saya adalah: $username<br>";
echo "Saat ini berada di: $hostname<br>";
echo "Versi PHP yang saya gunakan: $php_version<br>";
echo "Tanggal saat ini: $date<br>";
?>
```

jalankan

```
lynx http://10.73.3.2:8080
```