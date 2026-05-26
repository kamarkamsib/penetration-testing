# 📝 Pentest Quick Checklist & Cheat Sheet

Berkas ini adalah panduan taktis lapangan (*field checklist*) yang disusun secara ringkas untuk memandu pentester selama siklus hidup proyek pentesting berlangsung.

---

## 🏁 1. Fase Pra-Pengujian (Pre-engagement Checklist)
Sebelum memulai pemindaian apapun, pastikan poin-poin berikut telah selesai:
- [ ] **Scope Terdefinisi**: IP, subnet, domain, sub-domain, dan URL target sudah tertulis jelas dalam dokumen kesepakatan.
- [ ] **Dokumen Hukum**: Kontrak kerja, NDA (*Non-Disclosure Agreement*), dan Surat Izin Kerja Pentest (*Authorization Letter*) sudah ditandatangani oleh manajemen puncak kedua belah pihak.
- [ ] **Kontak Darurat**: Mengetahui siapa kontak teknis target (*Point of Contact* - POC) jika terjadi kendala/sistem down saat pengujian.
- [ ] **Whitelist IP Pentester**: IP public yang digunakan pentester sudah di-whitelist oleh tim keamanan target (jika pengujian berjenis *Gray/White Box*).

---

## 🔍 2. Pengumpulan Informasi & Pemindaian (Recon & Scanning)
Langkah-langkah taktis dalam pengumpulan data awal:

### A. Reconnaissance (OSINT)
- [ ] Lakukan pencarian domain WHOIS target.
- [ ] Lakukan Google Dorking untuk mencari file publik sensitif (PDF, DOCX, XLSX, SQL) yang tidak sengaja terindeks.
  - *Dork*: `site:target.com filetype:xls OR filetype:conf OR filetype:sql`
- [ ] Cari subdomain aktif menggunakan `subfinder`, `amass`, atau `assetfinder`.
- [ ] Lakukan reverse DNS lookups dan enumerasi rekaman DNS (MX, TXT, NS, A).

### B. Network Port Scanning & Service Fingerprinting
- [ ] Lakukan pemindaian host aktif (*Ping Sweep*).
- [ ] Jalankan Nmap scan dasar untuk mendeteksi port terbuka dan layanannya:
  ```bash
  nmap -sV -sC -Pn -T4 -oN nmap_result.txt <TARGET_IP>
  ```
- [ ] Lakukan identifikasi sistem operasi (*OS fingerprinting*).
- [ ] Lakukan pemindaian direktori web sensitif menggunakan `gobuster` atau `ffuf`:
  ```bash
  ffuf -w wordlist.txt -u http://target.com/FUZZ -mc 200,301,302
  ```

---

## ⚡ 3. Deteksi & Eksploitasi Kerentanan (Vulnerability & Exploitation)
Fokus pada pengujian manual dan otomatis terhadap target utama:

- [ ] **OWASP Top 10 Web Security Checks**:
  - [ ] **SQL Injection**: Tes pada form input, login, filter pencarian.
  - [ ] **Cross-Site Scripting (XSS)**: Cari kolom input yang memantulkan data kembali ke halaman web.
  - [ ] **IDOR / Access Control**: Tukar parameter ID akun untuk mengakses data akun lain.
  - [ ] **CSRF**: Cek keberadaan token anti-csrf pada request POST penting.
  - [ ] **SSRF**: Cek fitur yang memproses URL eksternal (impor gambar, webhook).
  - [ ] **File Upload**: Uji unggah berkas dengan mengganti ekstensi atau memanipulasi Content-Type.
- [ ] **Vulnerability Assessment (Otomatis)**:
  - [ ] Jalankan Nessus atau OpenVAS.
  - [ ] Cek temuan CVE pada service/port yang usang menggunakan database Exploit-DB atau `searchsploit`.

---

## 💥 4. Pasca-Eksploitasi (Post-Exploitation Checklist)
Setelah berhasil menembus salah satu sistem:
- [ ] **Eskalasi Hak Akses (Privilege Escalation)**:
  - [ ] Linux: Cek SUID binary, cronjobs misconfiguration, atau kernel exploit.
  - [ ] Windows: Cek privileges user saat ini (`whoami /priv`), unquoted service paths, atau bypass UAC.
- [ ] **Pivoting & Lateral Movement**:
  - [ ] Identifikasi jaringan internal server korban (menggunakan `ifconfig`, `ip route`, `arp -a`).
  - [ ] Gunakan tool seperti `Chisel` atau `proxychains` untuk mengakses host internal lain dari server korban.
- [ ] **Pembersihan Log Awal (Bila Perlu)**:
  - Catat seluruh aksi dan direktori berkas backdoor yang ditaruh untuk memudahkan proses *clean-up*.

---

## 🧼 5. Pembersihan Sistem (Clean-up Checklist)
Sangat penting untuk menjaga integritas infrastruktur klien:
- [ ] Hapus seluruh berkas web shell, backdoor, script tester, dan database payload yang diunggah ke server target.
- [ ] Kembalikan konfigurasi sistem yang diubah (seperti akun user tester yang dibuat sementara).
- [ ] Hapus entri log atau file temporary yang dibuat selama pengujian (jika disepakati).

---

## 📝 6. Penyusunan Laporan (Reporting Checklist)
Hasil akhir yang menjadi deliverable utama klien:
- [ ] **Laporan Eksekutif**: Tulis ringkasan singkat setinggi 1 halaman yang berfokus pada dampak bisnis, risiko, dan kesimpulan umum keamanan (cocok untuk jajaran manajemen non-teknis).
- [ ] **Tabel Daftar Temuan**: Urutkan temuan berdasarkan tingkat keparahan (Critical, High, Medium, Low).
- [ ] **Detail Teknis Temuan**:
  - Deskripsi dan dampak dari setiap celah.
  - Langkah-langkah detail untuk mereproduksi celah (*Step to Reproduce*).
  - Bukti gambar PoC (*Proof of Concept*).
  - Rekomendasi mitigasi teknis (kode perbaikan/patching).
- [ ] **Kesimpulan & Saran**: Berikan panduan strategis jangka pendek dan panjang untuk tim IT klien.
