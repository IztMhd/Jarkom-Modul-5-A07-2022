# Jarkom-Modul-5-A07-2022

## Anggota Kelompok

- I Putu Bagus Adhi Pradana (5025201010)
- Izzati Mukhammad (5025201075)
- Muhammad Damas Abhirama (5025201271)

## Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Loid dibawah ini:

![topologi!](img/topologi.png)

## Pembagian IP
Berikut merupakan pembagian IP

![pembagianIP!](img/pembagianIP.png)

## Routing
- Strix

      auto eth0
      iface eth0 inet dhcp

      auto eth1
      iface eth1 inet static
              address 192.172.0.1
              netmask 255.255.255.252

      auto eth2
      iface eth2 inet static
               address 192.172.0.5
               netmask 255.255.255.252
               
- Westalis

      auto eth0
      iface eth0 inet static
              address 192.172.0.2
              netmask 255.255.255.252
              gateway 192.172.0.1

      auto eth1
      iface eth1 inet static
              address 192.172.0.17
              netmask 255.255.255.248

      auto eth2
      iface eth2 inet static
               address 192.172.0.129
               netmask 255.255.255.128

      auto eth3
      iface eth3 inet static
               address 192.172.4.1
               netmask 255.255.252.0

- Ostania

      auto eth0
      iface eth0 inet static
                address 192.172.0.6
                netmask 255.255.255.252
                gateway 192.172.0.5

      auto eth1
      iface eth1 inet static
                address 192.172.0.25
                netmask 255.255.255.248

      auto eth2
      iface eth2 inet static
                address 192.172.2.1
                netmask 255.255.254.0

      auto eth3
      iface eth3 inet static
                address 192.172.1.1
                netmask 255.255.255.0

- Eden

      auto eth0
      iface eth0 inet static
            address 192.172.0.130
            netmask 255.255.255.128
            gateway 192.172.0.129
            
- WISE

      auto eth0
      iface eth0 inet static
              address 192.172.0.19
              netmask 255.255.255.248
              gateway 192.172.0.17

- Garden

      auto eth0
      iface eth0 inet static
                address 192.172.0.26
                netmask 255.255.255.248
                gateway 192.172.0.25

- SSS

      auto eth0
      iface eth0 inet static
                address 192.172.0.27
                netmask 255.255.255.248
                gateway 192.172.0.25

- Blackbell, Forger, Desmond, Briar

      auto eth0
      iface eth0 inet dhcp
      
## Anya, putri pertama Loid, juga berpesan kepada anda agar melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
- Strix

      route add -net 192.172.1.0 netmask 255.255.255.0 gw 192.172.0.6                                     
      route add -net 192.172.2.0 netmask 255.255.254.0 gw 192.172.0.6                                   
      route add -net 192.172.0.24 netmask 255.255.255.248 gw 192.172.0.6                                    
      route add -net 192.172.4.0 netmask 255.255.252.0 gw 192.172.0.2                                         
      route add -net 192.172.0.128 netmask 255.255.255.128 gw 192.172.0.2                                 
      route add -net 192.172.0.16 netmask 255.255.255.248 gw 192.172.0.2 

- Westalis

      route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.172.0.1

- Ostania

      route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.172.0.5
      
      
# Jarkom-Modul-5-A07-2022

## Anggota Kelompok

- I Putu Bagus Adhi Pradana (5025201010)
- Izzati Mukhammad (5025201075)
- Muhammad Damas Abhirama (5025201271)

## Topologi
Membuat topologi sebagai berikut

![topologi!](img/topologi.png)

## Pembagian IP
Berikut merupakan pembagian IP

![pembagianIP!](img/pembagianIP.png)

## Routing

## No. 1
> Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

Untuk mengganti `MASQUERADE` maka digunakannya `SNAT`. Source akan diubah dari yang awalnya `0.0` ke __Strix__ dengan `--to-source 192.168.122.213`.

```
iptables -t nat -A POSTROUTING -s 192.172.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.213
```

## No. 2
> Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

Untuk soal ini dilakukan penambahan `iptable` pada __WISE__

    iptables -A FORWARD -d 192.172.7.129/29 -i eth0 -p tcp -j DROP
    iptables -A FORWARD -d 192.172.7.129/29 -i eth0 -p udp -j DROP
    
## No. 3
> Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

Untuk soal ini sendiri, pada __WISE__ dan __Eden__ selaku DHCP dan DNS server, digunakan flag `-p` dengan nilai `icmp` dan menambahkan limit dengan `--connlimit-above 2` juga menambahkan target `DROP` agar hanya 2 koneksi yang dapat diterima.
    
    iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -$
    

## No. 4
> Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

Untuk mebatasi waktu digunakan `-m time` dengan `--timestart` yang bernilai 07.00 dan `--timestop` yang bernilai 16.00, serta membatasi hari berlaku dengan `--weekdays` untuk hari Senin hingga Jumat. Terdapat flag `-j` yang menentukan kapan akses akan di `ACCEPT` atau `REJECT`.
- Pada __DNS Server__
    ```
    iptables -A INPUT -s 192.172.7.136/29 -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
    iptables -A INPUT -s 192.172.7.136/29 -j REJECT
    ```
- Pada __Web Server__    
    ```
    iptables -A INPUT -m time --timestart 07:00 --timestop 16:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
    iptables -A INPUT -j REJECT
    ```
    
## No. 5
> Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

Masing-masing node di konfigurasi dengan port 80 dan 443 dengan menggunakan `--dport`. Selain itu, akan dibatasi secara bergantian dengan menggunakan `--every 2` sehingga pendistribusiannya akan bergantian dengan mengarahkan ke node lain dengan menggunakan `--to-destination`.

    iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.172.7.138 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.172.7.138:80
    iptables -A PREROUTING -t nat -p tcp --dport 80 -d 192.172.7.138 -j DNAT --to-destination 192.172.7.139:80
    iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.172.7.139 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.172.7.139:443
    iptables -A PREROUTING -t nat -p tcp --dport 443 -d 192.172.7.139 -j DNAT --to-destination 192.172.7.138:443

## No. 6
> 

Restart __WISE__ dengan :

```
service isc-dhcp-server restart
service isc-dhcp-server restart
```

Pada __WISE__ tambahkan sylog :

```
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: "
iptables -A LOGGING -j DROP
```    
