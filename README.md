# Jarkom Modul 3 E01 - 2022

Anggota:

- 5025201002 - Tegar Ganang Satrio Priambodo
- 5025201042 - Cindi Dwi Pramudita

**Daftar Soal:**

- [Soal 1](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-1)
- [Soal 2](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-2)
- [Soal 3](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-3)
- [Soal 4](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-4)
- [Soal 5](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-5)
- [Soal 6](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-6)
- [Soal 7](https://github.com/ThomasFel/Jarkom-Modul-3-A08-2021#Soal-7)

## Narasi Pendahuluan

Loid bersama Franky berencana membuat peta seperti berikut:

<img src="https://user-images.githubusercontent.com/37539546/141499741-55668306-c5dd-4510-99a0-626dc5b8fdb4.JPG" width=600>

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
    address 10.3.1.1
    netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
    address 10.3.2.1
    netmask 255.255.255.0

  auto eth3
    iface eth3 inet static
    address 10.3.3.1
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
    address 10.3.2.2
    netmask 255.255.255.0
    gateway 10.3.2.1
  ```
- Berlint (Proxy Server)

```
auto eth0
iface eth0 inet static
  address 10.3.2.3
  netmask 255.255.255.0
  gateway 10.3.2.1
```

- Westalis (DHCP Server)
  ```
  auto eth0
  iface eth0 inet static
    address 10.3.2.4
    netmask 255.255.255.0
    gateway 10.3.2.1
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
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.3.0.0/16
```

(Note: _Prefix_ IP yang digunakan sesuai _Prefix_ IP Kelompok, dalam hal ini kelompok E01 adalah **10.22**).

Dan ketikkan _command_ ini pada `Ostania` untuk melihat IP DNS:

```
cat /etc/resolv.conf
```

Akan muncul _nameserver_ yang akan digunakan pada konfigurasi selanjutnya.

<img src="https://user-images.githubusercontent.com/37539546/139521833-df4ae23e-08eb-4927-bd0e-992ec548670f.JPG" width="350">

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

<img src="https://user-images.githubusercontent.com/37539546/141504252-90f1983b-2380-4739-93e1-42ccbb75d38c.JPG" width="600">

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

<img src="https://user-images.githubusercontent.com/37539546/141606487-f2d9486e-5c42-4769-b61e-692cb276fd78.JPG" width="600">

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut. Sebagai catatan, di konfigurasi ini juga sekalian mengatur _node_ yang ada di Switch2.

<img src="https://user-images.githubusercontent.com/37539546/141607080-33803e75-71e1-419a-b082-0f33fa1ba469.JPG" width="450">

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

Perlu diketahui, apabila muncul _fail_ seperti di bawah, dapat melakukan _restart_ DHCP Server kembali.

<img src="https://user-images.githubusercontent.com/37539546/141606738-ea9007d7-d26e-4f15-ac6a-8246d6fa7df7.JPG" width="600">

Untuk memastikan apakah DHCP Server berjalan, dapat menggunakan _command_ berikut.

```
service isc-dhcp-server status
```

## Soal 4

### (Lanjutan) 3. Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.60 - [prefix IP].3.85.

### Jawaban:

### Wstalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

<img src="https://user-images.githubusercontent.com/37539546/141607041-0874be37-7f43-44f7-b578-f8f619e1ca5d.JPG" width="450">

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

## Soal 5

### (Lanjutan) 4. Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut.

### Jawaban:

### Westalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

<img src="https://user-images.githubusercontent.com/37539546/141607397-fe6b2ae3-8b5f-4da9-af3d-4ba56659df2c.jpg" width="450">

<img src="https://user-images.githubusercontent.com/37539546/141607402-9e1552af-2714-4019-99c8-90ce4be3b280.jpg" width="450">

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

<img src="https://user-images.githubusercontent.com/37539546/141607951-e806b4f2-c11c-4de7-8f9f-d11d8d04fdf9.JPG" width="600">

_Restart_ **bind9**.

```
service bind9 restart
```

## Soal 6

### (Lanjutan) 5. Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

### Jawaban:

### Westalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

<img src="https://user-images.githubusercontent.com/37539546/141608332-7efb39e5-c91c-4d85-b5bd-3bc136fcc229.jpg" width="450">

<img src="https://user-images.githubusercontent.com/37539546/141608339-be9a109c-2b1c-4049-9c6d-bd7b0c27c244.jpg" width="450">

_Restart_ **DHCP Server**.

```
service isc-dhcp-server restart
```

### SSS, Garden, Eden, NewstonCastle, KemonoPark

_Restart_ semua _client_ terlebih dahulu. Kemudian lakukan _testing_ IP dan _nameserver_ pada _client_. Salah satu contoh hasilnya pada **SSS** adalah seperti di bawah ini:

<img src="https://user-images.githubusercontent.com/37539546/141608177-54d51e68-089f-4e50-a05c-58686b6cf8ac.JPG" width="600">

_Testing_ juga _client_ apakah sudah bisa terkoneksi dengan internet dengan `ping` ke [**google.com**](https://www.google.com). Sebagai contoh pada `SSS`:

<img src="https://user-images.githubusercontent.com/37539546/139522122-43ddb61d-0b3a-484f-a4a5-433109dd6529.JPG" width="600">

## Soal 7

### Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13

### Jawaban:

### Westalis

Buka _file_ lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.

<img src="https://user-images.githubusercontent.com/37539546/141608558-e0876061-f5b3-46c4-8881-7d760d2eebb8.JPG" width="450">

### KemonoPark

`Edit network configuration` yang ada di menu `Configure` dan tambahkan baris perintah berikut.

```
hwaddress ether a6:e5:6b:b6:37:ed
```

Sebagai catatan, nilai **hwaddress** atau **hardware ethernet** diperoleh dari _interface_ yang ada di _node_ **Kemonopark** dengan menggunakan _command_ `ip a`.

<img src="https://user-images.githubusercontent.com/37539546/141608876-d2d03424-6580-4042-a5b0-fc8eee17e657.JPG" width="600">

Jadi nilainya akan berbeda tiap _project_.

Kemudian _restart node_. _Testing_ lagi dengan _command_ `ip a`, maka akan terlihat bahwa IP telah berubah.

<img src="https://user-images.githubusercontent.com/37539546/141608971-93d85ee6-85af-42ec-b7c0-3319379c9fc4.jpg" width="600">
