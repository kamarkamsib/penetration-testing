# 📡 Server-Side Request Forgery (SSRF)

## 1. Definisi
**Server-Side Request Forgery (SSRF)** adalah kerentanan keamanan di mana penyerang dapat memaksa aplikasi web di sisi server (*server-side*) untuk membuat permintaan HTTP/network ke alamat yang ditentukan penyerang. Penyerang biasanya memanfaatkan server korban sebagai batu loncatan (proxy) untuk melakukan pemindaian atau eksploitasi terhadap jaringan internal lokal server (yang biasanya dilindungi oleh firewall/tidak diakses publik), atau mengakses API metadata penyedia layanan cloud (seperti AWS, GCP, Azure) untuk mencuri kredensial sensitif.

---

## 2. Jenis-Jenis SSRF

### A. Regular/Basic SSRF
Server mengirimkan permintaan ke alamat yang diminta penyerang dan mengembalikan seluruh atau sebagian respon (baik berupa gambar, file teks, atau kode HTML) kembali ke penyerang. 
* **Keuntungan Penyerang**: Mudah divalidasi karena penyerang dapat melihat hasil data yang dicuri secara langsung.

### B. Blind SSRF
Server mengeksekusi permintaan ke alamat yang diminta penyerang, tetapi tidak mengembalikan data respons dari server tujuan ke layar pengguna. Penyerang hanya bisa mengamati perbedaan perilaku server (seperti perbedaan waktu respon, kode status HTTP) atau mendeteksi aktivitas koneksi keluar menggunakan server pemantau eksternal (seperti Burp Collaborator / Interactsh).

---

## 3. Titik Deteksi & Cara Kerja SSRF

Kerentanan SSRF biasanya ditemukan pada fitur aplikasi yang menerima input berupa URL eksternal untuk diproses oleh server, misalnya:
* Fitur impor gambar/file dari URL (misalnya mengunduh avatar pengguna dari link luar).
* Fitur integrasi webhook.
* Fitur pratinjau halaman web (*webpage preview*).
* Fitur penerjemahan halaman web.

### 🛑 Skenario Target Utama SSRF:

1. **Jaringan Internal Lokal (Loopback & LAN)**:
   Mencoba mengakses layanan internal yang berjalan pada localhost atau port tertutup:
   - `http://127.0.0.1:80` (Akses halaman admin lokal)
   - `http://127.0.0.1:6379` (Akses database Redis tanpa password)
   - `http://192.168.1.1` (Akses halaman router internal)

2. **Cloud Metadata Services (Sangat Kritis)**:
   Bila aplikasi dideploy di penyedia cloud (AWS, GCP, Azure), infrastruktur tersebut memiliki endpoint API metadata khusus yang hanya bisa diakses secara lokal oleh instance server untuk mengambil informasi kredensial.
   
   * **AWS (Amazon Web Services) & OpenStack**:
     `http://169.254.169.254/latest/meta-data/`
     `http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME` (Mencuri token akses AWS)
   * **Google Cloud Platform (GCP)**:
     `http://metadata.google.internal/computeMetadata/v1/` (Membutuhkan header khusus `Metadata-Flavor: Google`, namun terkadang SSRF blind atau rentan header bisa mengeksploitasinya).
   * **Microsoft Azure**:
     `http://169.254.169.254/metadata/instance?api-version=2021-02-01`

---

## 4. Contoh Payload SSRF

* **Akses Localhost**:
  ```http
  http://localhost:80/admin
  http://127.0.0.1:22
  ```
* **Akses Cloud Metadata (AWS)**:
  ```http
  http://169.254.169.254/latest/meta-data/
  ```
* **Menggunakan Skema Protokol Non-HTTP (Jika Didukung Library Curl/Parser Server)**:
  - `file:///etc/passwd` (Membaca file lokal server)
  - `gopher://127.0.0.1:6379/_SET%20test%20123` (Mengirimkan paket TCP raw ke database Redis)
  - `dict://127.0.0.1:11211/stat` (Mengakses memcached)

---

## 5. Teknik Bypass Filter & WAF SSRF

Developer sering kali memblokir akses ke string `127.0.0.1`, `localhost`, atau `169.254.169.254`. Berikut adalah teknik bypass yang sering digunakan oleh penyerang:

### 🔄 A. Alternatif Representasi IP Address
Browser dan sistem operasi memahami berbagai representasi bilangan IP address. Penyerang dapat menyamarkannya:
* **Representasi Decimal (Dword)**:
  `127.0.0.1` -> `http://2130706433`
  `169.254.169.254` -> `http://2852039166`
* **Representasi Hexadecimal**:
  `127.0.0.1` -> `http://0x7f000001`
  `169.254.169.254` -> `http://0xa9fea9fe`
* **Representasi Octal**:
  `127.0.0.1` -> `http://017700000001`
* **Representasi IP Disingkat (Shortened IP)**:
  `127.0.0.1` -> `http://127.1` atau `http://127.0.1`

### 🌐 B. Menggunakan Domain Khusus (DNS Wildcard)
Membuat domain yang mengarah kembali (*resolve*) ke IP internal:
* Menggunakan layanan publik instan:
  - `http://spoofed.127.0.0.1.nip.io` (Akan meresolve ke `127.0.0.1`)
  - `http://localtest.me` (Akan meresolve ke `127.0.0.1`)

### ⚡ C. DNS Rebinding (Sangat Efektif)
Teknik di mana penyerang memiliki domain khusus (misal `evil.com`). Saat server melakukan pemeriksaan pertama (validasi), DNS meresolve domain tersebut ke IP publik aman (lolos filter). Namun, sepersekian detik kemudian saat server melakukan *fetching* (permintaan kedua), DNS meresolve domain tersebut ke IP internal `127.0.0.1`.

### 🔗 D. HTTP Redirect Bypass
Jika aplikasi memvalidasi URL input harus berdomain aman (misal hanya boleh `http://aman.com`), penyerang dapat menaruh file skrip pada `http://aman.com` miliknya yang melakukan pengalihan status kode HTTP 302/301 ke arah target internal:
```php
<?php
// Letakkan di server luar yang diizinkan oleh target
header("Location: http://169.254.169.254/latest/meta-data/");
?>
```
Server target akan mengecek URL awal (aman.com - lolos validasi), namun saat server melakukan request HTTP, server akan mengikuti tautan pengalihan (*follow redirects*) ke IP internal metadata AWS.

---

## 6. Mitigasi (Pencegahan)

### ✔️ 1. Menerapkan Daftar Putih (Whitelisting) URL/Domain
Jangan izinkan pengguna memasukkan URL secara bebas. Hanya izinkan domain eksternal tertentu yang terpercaya yang ada di daftar putih (*whitelist*).

### ✔️ 2. Validasi IP Target Secara Ketat
Jika aplikasi memang harus menghubungi URL dinamis apa pun dari luar, implementasikan alur validasi alamat IP berikut pada kode backend:
1. Parse URL yang diinput untuk mendapatkan nama domain.
2. Lakukan resolusi DNS secara manual di sisi server untuk mendapatkan IP address domain tersebut.
3. Cek apakah IP tersebut termasuk dalam rentang IP private/local (seperti `127.0.0.0/8`, `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, `169.254.0.0/16`). Jika ya, **batalkan koneksi secara langsung**.
4. Lakukan koneksi ke IP yang sudah lolos uji (hindari melakukan resolusi DNS ulang untuk mencegah serangan DNS Rebinding).

### ✔️ 3. Isolasi Jaringan & Pembatasan Akses Metadata Cloud
* Konfigurasikan firewall jaringan lokal server (seperti iptables) agar server aplikasi web tidak dapat melakukan koneksi keluar ke arah port database internal atau instance internal lainnya secara sembarangan.
* Pada AWS, aktifkan **IMDSv2** (Instance Metadata Service Version 2) yang mengharuskan permintaan metadata menggunakan token session berbasis header POST khusus, sehingga memitigasi SSRF GET sederhana.

---

## 7. Referensi Terpercaya
* [PortSwigger Web Security Academy: Server-Side Request Forgery](https://portswigger.net/web-security/ssrf)
* [OWASP Cheat Sheet Series: Server-Side Request Forgery Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html)
* [SSRF Bypass List by PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)
