# 🔑 Insecure Direct Object Reference (IDOR)

## 1. Definisi
**Insecure Direct Object Reference (IDOR)** adalah jenis kerentanan kontrol akses (*access control*) yang terjadi ketika aplikasi web menyediakan akses langsung ke objek data internal (seperti baris database, file lokal, atau kunci sistem) berdasarkan input parameter yang disediakan oleh pengguna, tanpa melakukan validasi otorisasi apakah pengguna tersebut memang berhak mengakses objek data tersebut.

IDOR umumnya memungkinkan penyerang untuk:
* **Horizontal Privilege Escalation**: Mengakses atau memodifikasi data milik pengguna lain yang memiliki tingkat hak akses yang sama.
* **Vertical Privilege Escalation**: Mengakses atau memodifikasi data milik pengguna yang memiliki tingkat hak akses lebih tinggi (misalnya administrator).

---

## 2. Cara Kerja & Contoh Skenario IDOR

IDOR terjadi ketika backend aplikasi web berasumsi bahwa pengguna hanya akan meminta data yang memang milik mereka, dan langsung mengeksekusi query database tanpa memeriksa kepemilikan sesi (*session ownership*).

### 🔍 Skenario Klasik:
1. Pengguna login ke akun mereka dan membuka menu "Profil Saya".
2. Aplikasi memanggil URL: `https://target.com/profile?user_id=125`
3. Pengguna mengubah parameter `user_id` menjadi `126`: `https://target.com/profile?user_id=126`
4. Backend menerima request dan langsung menampilkan data pribadi milik pengguna dengan ID `126` (seperti nama lengkap, alamat, nomor telepon) di layar penyerang tanpa memverifikasi apakah akun login penyerang memiliki izin atas ID `126` tersebut.

---

## 3. Cara Mendeteksi IDOR

IDOR biasanya diuji secara manual dengan mengubah nilai parameter yang mengarah pada objek tertentu.

1. **Identifikasi Parameter Sensitif**: Cari parameter pada URL, request body (JSON/XML), atau header yang merepresentasikan objek, seperti:
   - `id=...`
   - `user_id=...`
   - `account_number=...`
   - `order_id=...`
   - `file_name=...`
2. **Siapkan Dua Akun Uji**:
   - Akun A (Penyerang)
   - Akun B (Korban)
3. **Lakukan Manipulasi (Tukar Parameter)**:
   - Tangkap request dari Akun A saat mengakses datanya sendiri (misal: order #5001).
   - Ubah parameter order ID menjadi ID milik Akun B (misal: order #5002) dengan tetap menggunakan cookie sesi Akun A.
   - Amati respon: Jika server mengembalikan detail pesanan Akun B, maka fitur tersebut rentan terhadap IDOR.

---

## 4. Teknik Lanjutan & Bypass Uji IDOR

Sering kali developer menyembunyikan IDOR dengan beberapa proteksi sederhana. Berikut adalah teknik bypass yang sering dicoba pentester:

### A. Menggunakan HTTP Parameter Pollution (HPP)
Mengirimkan parameter ganda untuk membingungkan parsing di sisi backend:
```http
GET /profile?user_id=AKUN_KORBAN&user_id=AKUN_SAYA
GET /profile?user_id=AKUN_SAYA&user_id=AKUN_KORBAN
```
Terkadang framework backend memvalidasi parameter pertama tetapi mengambil nilai parameter kedua saat mengeksekusi query database.

### B. Mengubah Metode Request (HTTP Methods)
Mencoba mengubah metode pengiriman jika aksi GET atau POST dilindungi:
* Coba ubah `GET` menjadi `POST`, `PUT`, atau `DELETE` (misalnya untuk menghapus akun orang lain).
* Coba kirim data sebagai JSON (`Content-Type: application/json`) alih-alih form URL-encoded.

### C. Mengubah Tipe Data Input (Ganti ID dengan Array)
Beberapa framework web (seperti PHP/Rails) rentan terhadap manipulasi tipe data parameter:
* `user_id=126` -> `user_id[]=126` atau `user_id[]=125&user_id[]=126`

### D. Bypass UUID (Predictable & Leaked UUIDs)
Jika aplikasi menggunakan UUID (misalnya `f81d4fae-7dec-11d0-a765-00a0c91e6bf6`) yang sulit ditebak:
* Cek apakah UUID pengguna lain bocor di halaman publik (seperti di kolom komentar, pencarian user, atau metadata publik).
* Cek apakah UUID tersebut dapat diprediksi (misalnya menggunakan algoritma UUID v1 yang berbasis waktu dan alamat MAC).

---

## 5. Mitigasi (Pencegahan)

Menggunakan UUID atau mengenkripsi ID parameter hanyalah perlindungan permukaan (*security through obscurity*). Satu-satunya cara mutlak mencegah IDOR adalah **melakukan verifikasi otorisasi di sisi backend secara ketat pada setiap request**.

### ✔️ 1. Otorisasi Berbasis Peran & Kepemilikan (Access Control Checks)
Setiap kali menerima parameter objek, server harus memverifikasi apakah identitas pengguna yang aktif (yang ada di dalam session/JWT token) memiliki hak atas objek tersebut.

* **Contoh PHP yang Rentan (Salah)**:
  ```php
  // Mengambil data langsung berdasarkan input tanpa cek kepemilikan
  $invoice_id = $_GET['invoice_id'];
  $invoice = $db->query("SELECT * FROM invoices WHERE id = $invoice_id");
  ```

* **Contoh PHP yang Aman (Benar)**:
  ```php
  $invoice_id = $_GET['invoice_id'];
  $user_id = $_SESSION['user_id']; // ID User dari sesi login aman

  // Query memverifikasi bahwa invoice tersebut adalah milik user yang sedang aktif
  $stmt = $db->prepare("SELECT * FROM invoices WHERE id = :invoice_id AND owner_id = :user_id");
  $stmt->execute(['invoice_id' => $invoice_id, 'user_id' => $user_id]);
  $invoice = $stmt->fetch();

  if (!$invoice) {
      // Jika tidak ditemukan atau bukan pemiliknya, tolak akses!
      http_response_code(403);
      die("Akses Ditolak.");
  }
  ```

### ✔️ 2. Menggunakan UUID v4 (Non-predictable ID)
Gunakan UUID versi 4 yang acak secara kriptografis untuk ID objek publik. Ini mempersulit penyerang untuk melakukan pemindaian masif (*mass enumeration*) terhadap data pengguna lain.

### ✔️ 3. Indirect Reference Map
Jika Anda ingin menyembunyikan ID database asli, gunakan peta referensi tidak langsung. Misalnya, alih-alih mengirimkan `/download?file=1203`, server memetakan file tersebut ke dalam sesi pengguna sebagai kunci sementara: `/download?file=file_key_1`. Kunci tersebut hanya valid selama sesi pengguna aktif dan tidak bisa ditebak oleh pengguna lain.

---

## 6. Referensi Terpercaya
* [PortSwigger Web Security Academy: Access Control & IDOR](https://portswigger.net/web-security/access-control/idor)
* [OWASP Cheat Sheet Series: Authorization Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
* [IDOR Methodology & Tips by HowToHunt](https://github.com/KathanP19/HowToHunt/blob/master/IDOR/IDOR.md)
