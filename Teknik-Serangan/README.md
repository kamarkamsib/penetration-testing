# ⚔️ Teknik Serangan (Attack Techniques)

Berikut adalah panduan praktis dan modul pembelajaran mengenai berbagai teknik serangan (*attack techniques*) yang sering diuji coba selama proses Penetration Testing. Setiap modul dilengkapi dengan konsep dasar, metode pengujian, payload, teknik bypass, serta langkah mitigasi bagi pengembang aplikasi.

---

## 📂 Daftar Modul Teknik Serangan

Silakan klik tautan di bawah ini untuk mempelajari masing-masing kerentanan secara detail:

| Modul Serangan | Deskripsi Singkat | Status Materi |
| :--- | :--- | :--- |
| **[💉 SQL Injection (SQLi)](SQLi/README.md)** | Memanipulasi query database SQL melalui input pengguna. | 🟢 Lengkap (Baru) |
| **[🎨 Cross-Site Scripting (XSS)](XSS/README.md)** | Menyisipkan skrip klien berbahaya (JS) ke halaman web yang diakses pengguna lain. | 🟢 Lengkap (Baru) |
| **[📡 Server-Side Request Forgery (SSRF)](SSRF/README.md)** | Memaksa server backend melakukan HTTP request ke server internal atau cloud metadata. | 🟢 Lengkap (Baru) |
| **[📑 XML External Entity (XXE)](XXE/README.md)** | Menyalahgunakan parser XML untuk membaca file lokal server atau memicu SSRF. | 🟢 Lengkap (Baru) |
| **[🔑 Insecure Direct Object Reference (IDOR)](IDOR/README.md)** | Mengakses objek data pengguna lain secara ilegal akibat kontrol akses objek yang lemah. | 🟢 Lengkap (Baru) |
| **[📤 File Upload Vulnerability](File-Upload-Inject/README.md)** | Mengunggah berkas web shell berbahaya ke server untuk mendapatkan akses RCE. | 🟢 Lengkap (Baru) |
| **[🔄 Cross-Site Request Forgery (CSRF)](CSRF/README.md)** | Menjebak pengguna terautentikasi untuk melakukan tindakan tanpa disadari. | 🟡 Diperbarui (Polesan) |
| **[🌐 CORS Misconfiguration](CORS/README.md)** | Miskonfigurasi kebijakan lintas-asal yang memungkinkan pencurian data sensitif pengguna. | 🟡 Diperbarui (Polesan) |
| **[🧩 Server-Side Template Injection (SSTI)](SSTI/README.md)** | Menyisipkan sintaks template untuk dieksekusi di sisi server (RCE). | 🟡 Diperbarui (Polesan) |
| **[⚙️ CMS Vulnerabilities (WordPress/AEM/Drupal)](CMS/README.md)** | Eksploitasi kesalahan konfigurasi dan celah keamanan pada CMS populer. | 🟡 Diperbarui (Polesan) |

---

## 🛠️ Tips Umum Pengujian
1. **Selalu Intercept Request**: Gunakan proxy lokal seperti **Burp Suite** atau **OWASP ZAP** untuk melihat, memodifikasi, dan mengulang kembali (*replay*) setiap permintaan HTTP.
2. **Pahami Sisi Server**: Mengetahui bahasa pemrograman backend (PHP, Node.js, Python, Java) sangat membantu dalam merancang payload bypass yang sukses.
3. **Lakukan Uji Mitigasi**: Setelah menemukan kerentanan, pastikan Anda juga menguji apakah metode mitigasi yang disarankan benar-benar menutup celah tersebut.
