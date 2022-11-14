# Jarkom Modul 3 E01 - 2022

Anggota:

- 5025201002 - Tegar Ganang Satrio Priambodo
- 5025201042 - Cindi Dwi Pramudita

**Daftar Soal:**

- [Soal 1](#soal-1)
- [Soal 2](#soal-2)
- [Soal 3](#soal-3)
- [Soal 4](#soal-4)
- [Soal 5](#soal-5)
- [Soal 6](#soal-6)
- [Soal 7](#soal-7)

## Narasi Pendahuluan

Loid bersama Franky berencana membuat peta seperti berikut:

![image](https://user-images.githubusercontent.com/85062827/201646623-1aeeeac6-ae6a-4f05-83c8-b6c8b21935af.png)

## Soal 1

### Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

### Jawaban:

Pertama, membuat topologi sesuai permintaan soal. Kemudian _setting network_ masing-masing _node_ dengan fitur `Edit network configuration` yang ada di menu `Configure`. Karena akan menggunakan DHCP, maka _setting network_ akan sedikit berbeda dengan [Praktikum Modul 2](https://github.com/tegar-ganang/Jarkom-Modul-2-E01-2022). _Setting_ awal yang sudah ada dapat dihapus dan diganti dengan konfigurasi berikut:

- Ostania (Router & DHCP Relay)

  ```
  auto eth0
  iface eth0 inet dhcp

  auto eth1
  iface eth1 inet static
    address 10.22.1.1
    netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
    address 10.22.2.1
    netmask 255.255.255.0

  auto eth3
    iface eth3 inet static
    address 10.22.3.1
    netmask 255.255.255.0
  ```

- SSS (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
- Garden (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
- Wise (DNS Server)
  ```
  auto eth0
  iface eth0 inet static
    address 10.22.2.2
    netmask 255.255.255.0
    gateway 10.22.2.1
  ```
- Berlint (Proxy Server)

```
auto eth0
iface eth0 inet static
  address 10.22.2.3
  netmask 255.255.255.0
  gateway 10.22.2.1
```

- Westalis (DHCP Server)
  ```
  auto eth0
  iface eth0 inet static
    address 10.22.2.4
    netmask 255.255.255.0
    gateway 10.22.2.1
  ```
- Eden (Client)

  ```
  auto eth0
  iface eth0 inet dhcp
  ```

- NewstonCastle (Client)

  ```
  auto eth0
  iface eth0 inet dhcp
  ```

- KemonoPark (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```

### Ostania

_Restart_ semua _node_. Lalu jalankan _command_ berikut pada _router_ `Ostania` untuk pengaturan lalu lintas komputer.

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.22.0.0/16
```

(Note: _Prefix_ IP yang digunakan sesuai _Prefix_ IP Kelompok, dalam hal ini kelompok E01 adalah **10.22**).

Dan ketikkan _command_ ini pada `Ostania` untuk melihat IP DNS:

```
cat /etc/resolv.conf
```

Akan muncul _nameserver_ yang akan digunakan pada konfigurasi selanjutnya.

![image](https://user-images.githubusercontent.com/85062827/201647083-f360c35a-9e86-4b1f-95d6-bae60554622b.png)


### Semua node (kecuali Ostania)

Agar _node_-_node_ lainnya dapat mengakses internet, jalankan _command_ berikut dan gunakan IP DNS dari `Ostania` tadi.

```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

_Restart_ semua _node_ kembali. Lalu, _testing_ semua _node_ apakah sudah terkoneksi dengan internet dengan `ping` ke [**google.com**](https://www.google.com). Sebagai contoh pada `SSS`:

<img src="https://user-images.githubusercontent.com/37539546/139522122-43ddb61d-0b3a-484f-a4a5-433109dd6529.JPG" width="600">

### Semua client (SSS, Garden, Eden, NewstonCastle, KemonoPark)

Karena _client_ nantinya digunakan untuk _testing_/membuka suatu web, perlu dilakukan instalasi **Lynx** menggunakan perintah berikut.

```
apt-get install lynx -y
```

## Soal 2

### Dan Ostania sebagai `DHCP Relay`.

### Jawaban:

### Ostania

Melakukan instalasi **DHCP Relay** terlebih dahulu pada `Ostania` dengan _update package list_. _Command_ yang dijalankan adalah sebagai berikut.

```
apt-get update
apt-get install isc-dhcp-relay -y
```

Buka _file_ **/etc/default/isc-dhcp-relay** dan edit seperti konfigurasi berikut.

![image](https://user-images.githubusercontent.com/85062827/201648269-b418438c-6cf4-42ae-8e58-90f5d57e8a98.png)

_Restart_ **DHCP Relay**.

```
service isc-dhcp-relay restart
```

Untuk memastikan apakah DHCP Relay berjalan, dapat menggunakan _command_ berikut.

```
service isc-dhcp-relay status
```

## Soal 3

### Loid dan Franky menyusun peta tersebut dengan hati-hati dan teliti.

Ada beberapa kriteria yang ingin dibuat oleh Loid dan Franky, yaitu:

1.  Semua _client_ yang ada **HARUS** menggunakan konfigurasi IP dari **DHCP Server**.
2.  _Client_ yang melalui **Switch1** mendapatkan _range_ IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155.

### Jawaban:

### Westalis

Melakukan instalasi **DHCP Server** terlebih dahulu pada `Westalis` dengan _update package list_. _Command_ yang dijalankan adalah sebagai berikut.

```
apt-get update
apt-get install isc-dhcp-server -y
```

Buka _file_ **/etc/default/isc-dhcp-server** dan edit seperti konfigurasi berikut.

```
INTERFACES="eth0"
```

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut. Sebagai catatan, di konfigurasi ini juga sekalian mengatur _node_ yang ada di Switch2.

```
subnet 10.22.1.0 netmask 255.255.255.0 {
    range 10.22.1.50 10.22.1.88;
    range 10.22.1.120 10.22.1.155;
    option routers 10.22.1.1;
    option broadcast-address 10.22.1.255;
    option domain-name-servers 10.22.2.2;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 10.22.2.0 netmask 255.255.255.0 {
    option routers 10.22.2.1;
}
```

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

Perlu diketahui, apabila muncul _fail_ seperti di bawah, dapat melakukan _restart_ DHCP Server kembali.

![image](https://user-images.githubusercontent.com/85062827/201649888-0e6a6179-5def-45a4-8cc6-94ec28f49a69.png)

Untuk memastikan apakah DHCP Server berjalan, dapat menggunakan _command_ berikut.

```
service isc-dhcp-server status
```

## Soal 4

### (Lanjutan) 3. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.60 - [prefix IP].3.85.

### Jawaban:

### Wstalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

```
subnet 10.22.3.0 netmask 255.255.255.0 {
    range 10.22.3.10 10.22.3.30;
    range 10.22.3.60 10.22.3.85;
    option routers 10.22.1.1;
    option broadcast-address 10.22.3.255;
    option domain-name-servers 10.22.2.2;
    default-lease-time 720;
    max-lease-time 7200;
}
```

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

## Soal 5

### (Lanjutan) 4. Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut.

### Jawaban:

### Westalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

```
option domain-name-servers 10.22.2.2;
```

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

### WISE

Melakukan instalasi **bind9** terlebih dahulu pada `WISE` dengan _update package list_. _Command_ yang dijalankan adalah sebagai berikut.

```
apt-get update
apt-get install bind9 -y
```

Buka _file_ **/etc/bind/named.conf.options** dan edit seperti konfigurasi berikut.

```
forwarders {
      192.168.122.1;
};

allow-query{any;};
```

_Restart_ **bind9**.

```
service bind9 restart
```

## Soal 6

### (Lanjutan) 5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

### Jawaban:

### Westalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit waktu

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

### SSS, Garden, Eden, NewstonCastle, KemonoPark

_Restart_ semua _client_ terlebih dahulu. Kemudian lakukan _testing_ IP dan _nameserver_ pada _client_. Salah satu contoh hasilnya pada **SSS** adalah seperti di bawah ini:

![image](https://user-images.githubusercontent.com/85062827/201653710-b2f5eeff-f5bc-4d3c-b49e-f2de0ec88a0d.png)

_Testing_ juga _client_ apakah sudah bisa terkoneksi dengan internet dengan `ping` ke [**google.com**](https://www.google.com). 

## Soal 7

### Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13

### Jawaban:

### Westalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

```
host Eden {
  hardware ethernet 46:eb:80:d4:01:7c;
  fixed-address 10.22.3.13;
}
```

### Eden

`Edit network configuration` yang ada di menu `Configure` dan tambahkan baris perintah berikut.

```
hwaddress ether 46:eb:80:d4:01:7c
```

Sebagai catatan, nilai **hwaddress** atau **hardware ethernet** diperoleh dari _interface_ yang ada di _node_ **Eden** dengan menggunakan _command_ `ip a`.

![image](https://user-images.githubusercontent.com/85062827/201655084-58b4dd45-0821-458e-bff9-02741bb49f8a.png)

Jadi nilainya akan berbeda tiap _project_.

Kemudian _restart node_. _Testing_ lagi dengan _command_ `ip a`, maka akan terlihat bahwa IP telah berubah.

![image](https://user-images.githubusercontent.com/85062827/201655454-f9ee0b1d-f294-4825-aa7c-d8cf960ad50b.png)
