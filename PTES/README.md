# 📋 PTES (Penetration Testing Execution Standard)

**Penetration Testing Execution Standard (PTES)** adalah standar baku pelaksanaan pentesting yang dirancang oleh para ahli industri keamanan siber untuk menetapkan basis acuan minimum dalam pelaksanaan penetration testing berkualitas tinggi. 

Tujuan dari standar ini adalah untuk memberikan pemahaman teknis dan bisnis yang selaras bagi penyedia jasa pentest (*tester*) maupun organisasi yang diuji (*klien*), sehingga hasil pengujian memiliki nilai nyata dalam meningkatkan postur keamanan.

---

## 🗺️ 7 Bagian Utama Standar PTES

Berikut adalah penjelasan mengenai 7 tahapan utama yang didefinisikan oleh PTES sebagai siklus hidup pelaksanaan pentest profesional:

### 1. Pre-engagement Interactions (Interaksi Pra-Pengujian)
* **Tujuan**: Tahap negosiasi dan penyusunan kesepakatan hukum sebelum pengujian dimulai.
* **Aktivitas**:
  - Menentukan ruang lingkup pengujian (*Scope* - apa saja IP, domain, atau aplikasi yang boleh/tidak boleh diserang).
  - Menetapkan aturan keterlibatan (*Rules of Engagement* - waktu pengujian, pelarangan serangan DoS, pengujian sosial engineering).
  - Menyusun dan menandatangani dokumen hukum (seperti NDA - *Non-Disclosure Agreement*).

### 2. Intelligence Gathering (Pengumpulan Intelijen / Reconnaissance)
* **Tujuan**: Mengumpulkan informasi sebanyak mungkin mengenai target untuk merancang skenario serangan.
* **Aktivitas**:
  - Melakukan OSINT (Open Source Intelligence) pada data publik organisasi.
  - Melakukan pencarian footprint jaringan, email karyawan, subdomain, dan infrastruktur cloud.
  - Menganalisis metadata dari file publik yang dibagikan target.

### 3. Threat Modeling (Pemodelan Ancaman)
* **Tujuan**: Mengidentifikasi dan memetakan potensi ancaman berdasarkan informasi intelijen yang terkumpul.
* **Aktivitas**:
  - Menganalisis aset organisasi apa saja yang paling bernilai (seperti database pelanggan, kode sumber).
  - Mengidentifikasi profil penyerang yang mungkin menargetkan organisasi tersebut (misal: kompetitor, aktor negara, peretas oportunistik).
  - Menentukan vektor serangan taktis yang paling efektif untuk menembus pertahanan target.

### 4. Vulnerability Analysis (Analisis Kerentanan)
* **Tujuan**: Menemukan kelemahan atau celah keamanan pada sistem dan aplikasi yang berada dalam ruang lingkup.
* **Aktivitas**:
  - Melakukan pemindaian otomatis (menggunakan *vulnerability scanners*).
  - Melakukan pengujian manual untuk mengidentifikasi kesalahan konfigurasi logic aplikasi, kesalahan otorisasi, atau versi komponen usang.
  - Mengonfirmasi temuan celah keamanan untuk memisahkan antara kerentanan nyata dan *false positive*.

### 5. Exploitation (Eksploitasi)
* **Tujuan**: Menembus pertahanan sistem dengan cara mengeksploitasi kerentanan yang telah ditemukan sebelumnya secara terkontrol.
* **Aktivitas**:
  - Menjalankan payload exploit (seperti SQLi, RCE, Buffer Overflow) untuk mendapatkan akses awal (*initial access*).
  - Menghindari deteksi sistem pertahanan (seperti WAF atau Antivirus) jika diperlukan sesuai kesepakatan rules.
  - Membuktikan dampak nyata dari kerentanan tanpa merusak integritas operasional sistem klien.

### 6. Post Exploitation (Pasca Eksploitasi)
* **Tujuan**: Mengukur tingkat kerusakan atau dampak bisnis setelah sistem berhasil ditembus.
* **Aktivitas**:
  - Melakukan eskalasi hak akses (*privilege escalation*) dari user biasa menjadi administrator/root.
  - Melakukan pemindahan lateral (*pivoting*) untuk menjelajahi jaringan internal target.
  - Mengidentifikasi data sensitif yang dapat diakses (ekstrasi informasi bernilai tinggi).
  - Memasang mekanisme persisten (*backdoor* legal) untuk membuktikan kemampuan penyerang mempertahankan akses.

### 7. Reporting (Pelaporan)
* **Tujuan**: Menyusun dokumen resmi hasil pengujian yang berisi temuan teknis beserta rekomendasi perbaikannya.
* **Aktivitas**:
  - Menulis laporan eksekutif ringkas untuk tingkat direksi/manajemen (berisi tingkat risiko bisnis dan konklusi umum).
  - Menulis detail temuan teknis untuk tim IT/developer (langkah reproduksi celah, skor CVSS, bukti PoC, dan langkah mitigasi konkret).
  - Melakukan sesi presentasi temuan (*debriefing*) dengan klien.

---

## 🛠️ Panduan Teknis & FAQ Resmi
Karena dokumen standar ini fokus pada *alur proses kerja* dan bukan panduan perintah teknis, komite PTES juga menyediakan modul panduan teknis pelengkap yang dapat diakses secara online:

* [PTES Technical Guidelines (Panduan Teknis Resmi)](http://www.pentest-standard.org/index.php/PTES_Technical_Guidelines)
* [FAQ Resmi Standar PTES](http://www.pentest-standard.org/index.php/FAQ)
