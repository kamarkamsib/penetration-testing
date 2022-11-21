# Penetration Testing
###### Tulisan ini didedikasikan untuk para anggota grup [**Pentesting Indonesia**](https://t.me/PentestingIndonesia) di [Telegram](https://t.me/KamarKamsib). Github ini ada berisikan materi, konsep, best practice, contoh dokumen, hingga tools yang sering digunakan. Cekidot!

## Kontributor:
- [rad1zly](https://github.com/rad1zly)
- [anton](https://github.com/antonlepari)
- maybe it's you ...
- maybe it's you ...

## Outline

- Apa sih [Penetration Testing](https://github.com/kamarkamsib/penetration-testing/) itu?
- Emang siapa aja yang butuhin pentesting?
- Lah kan udah ada bug bounty, terus pentesting buat apa?
- Metodologi yang digunakan.
- - Reconnaissance and Information Gathering
- - Network Enumeration and Scanning
- - Vulnerability Testing and Exploitation
- Reporting 
- OWASP
- Gimana cara jadi Pentester?

## 01. Apa sih Penetration Testing itu?

Pentesting merupakan sebuah tes yang dilakukan dengan tujuan mencari kerenatanan pada sebua sistem. Sedikit berbeda dengan Vuln Scaning, Pentest menguji keamanan komputer individu, perangkat jaringan, atau aplikasi, pengujian penetrasi menilai model keamanan jaringan secara keseluruhan. Pentesting berbeda pula dengan kegiatan Bug Hunting atau Bug Bounty.

> Tetap haus dan jangan lupa berbagi.

## 02. Emang siapa aja yang butuhin pentesting?

## 03. Lah kan udah ada bug bounty, terus pentesting buat apa?
Ada perbedaan bug bounty dengan pentesting.

## 04. Metodologi
Beberapa metodologi pentest yang bisa dipakai, antara lain:

* PTES

Standar pelaksanaan pentesting terdiri dari tujuh (7) bagian utama. Ketujuh bagian ini mencakup semua hal terkait pentest ― mulai dari fase komunikasi awal dan alasan mengepa pentest dibutuhkan, hingga fase pengumpulan intelijen (_intelligence gathering_) dan pemodelan ancaman (_threat modeling_).

* NIST CSF
* OWASP Testing Framework
* Lorem ipsum...
* dst....

### Reconnaissance and Information Gathering
### Network Enumeration and Scanning
### Vulnerability Testing and Exploitation

## 05. Reporting

## 06. OWASP
> Dalam menyusun tulisan ini, @kamarkamsib menggunakan OWASP sebagai kiblat.
> situs : [OWASP](https://owasp.org/www-project-top-ten/)

Open Web Application Security Project® (OWASP) adalah yayasan nirlaba yang bekerja untuk meningkatkan keamanan perangkat lunak. Melalui proyek perangkat lunak sumber terbuka yang dipimpin komunitas, ratusan cabang lokal di seluruh dunia, puluhan ribu anggota, dan konferensi pendidikan dan pelatihan terkemuka. 

OWASP Foundation adalah sumber bagi pengembang dan ahli teknologi untuk mengamankan web. Salah satu project populer yang dihadirkan oleh yayasan ini adalah OWASP TOP 10. 

### OWASP TOP 10

![mapping](https://owasp.org/www-project-top-ten/assets/images/mapping.png)

OWASP Top 10 adalah dokumen kesadaran standar untuk pengembang dan keamanan aplikasi web. Ini mewakili konsensus luas tentang risiko keamanan paling kritis untuk aplikasi web. OWASP Top 10 adalah buku/dokumen referensi yang menguraikan 10 masalah keamanan paling kritis (critical security concerns) untuk keamanan aplikasi web. Laporan tersebut disusun oleh tim pakar keamanan dari seluruh dunia dan datanya berasal dari sejumlah organisasi dan kemudian dianalisis.

### Belajar Serangan
Salah satu kiblat dari Kamar Kamsib dalam memahami serangan yang populer adalah OWASP Top 10. Mulai dari OWASP Top Ten tahun 2013, 2017, dan tahun 2021. Bagian ini akan membahas mengenai OWASP TOP 10 2021.

#### A01 Broken Access Control 
Kontrol Akses (_access control_) memberlakukan kebijakan sedemikian rupa sehingga pengguna tidak dapat bertindak di luar izin yang dimaksudkan. Kegagalan (_failure_) biasanya mengarah pada pengungkapan informasi yang tidak sah, modifikasi, atau penghancuran semua data atau menjalankan fungsi bisnis di luar batas pengguna. Kerentanan kontrol akses umum meliputi:

* Hak akses yang seharusnya hanya untuk top level akun, malah tersedia untuk siapa saja.
* Melewati pemeriksaan kontrol akses dengan memodifikasi URL (pengrusakan parameter atau penelusuran paksa), status aplikasi internal, atau halaman HTML, atau dengan menggunakan alat penyerang yang mengubah permintaan API.
* Mengizinkan melihat atau mengedit akun orang lain, dengan memberikan pengenal uniknya (referensi objek langsung yang tidak aman).
* Mengakses API tanpa kontrol akses untuk POST, PUT, dan DELETE.
* Peningkatan keistimewaan. Bertindak sebagai pengguna tanpa masuk atau bertindak sebagai admin saat masuk sebagai pengguna.
* Manipulasi metadata, seperti memutar ulang atau merusak token kontrol akses JSON Web Token (JWT), atau cookie atau bidang tersembunyi yang dimanipulasi untuk meningkatkan hak istimewa atau menyalahgunakan pembatalan JWT.
* Kesalahan konfigurasi CORS memungkinkan akses API dari sumber yang tidak sah/tidak tepercaya.
* _Force browsing_ ke halaman yang diautentikasi sebagai pengguna yang tidak diautentikasi atau ke halaman dengan hak istimewa sebagai pengguna standar.

#### A02 Cryptographic Failures 
Menentukan terlebih dulu kebutuhan **Data Protection** antara **Data In transit** dan **Data At Rest**. Misalnya, kata sandi, nomor kartu kredit, catatan kesehatan, informasi pribadi, dan rahasia bisnis memerlukan perlindungan ekstra, terutama jika data tersebut termasuk dalam undang-undang privasi, misalnya Peraturan Perlindungan Data Umum (GDPR) UE, atau peraturan, misalnya, perlindungan data keuangan seperti Standar Keamanan Data PCI (PCI DSS).

#### A03 Injection 
Injek.

#### A04 Insecure Design 
Desain yang tidak aman adalah kategori luas yang mewakili berbagai kelemahan, dinyatakan sebagai "desain kontrol yang hilang atau tidak efektif". Rancangan yang tidak aman bukanlah sumber dari semua kategori 10 risiko teratas lainnya. Ada perbedaan antara desain yang tidak aman dan implementasi yang tidak aman. 

Kami membedakan antara cacat desain dan cacat implementasi karena suatu alasan, mereka memiliki akar penyebab dan perbaikan yang berbeda. Desain yang aman masih dapat memiliki cacat implementasi yang menyebabkan kerentanan yang dapat dieksploitasi. Desain yang tidak aman tidak dapat diperbaiki dengan implementasi yang sempurna karena menurut definisi, kontrol keamanan yang diperlukan tidak pernah dibuat untuk bertahan dari serangan tertentu. 

Salah satu faktor yang berkontribusi terhadap desain yang tidak aman adalah kurangnya profil risiko bisnis yang melekat pada perangkat lunak atau sistem yang sedang dikembangkan, dan dengan demikian kegagalan untuk menentukan tingkat desain keamanan apa yang diperlukan.

#### A05 Security Misconfiguration 
Jangan salah konfigurasi.

#### A06 Vulnerable and Outdated Components 
was previously titled Using Components with Known Vulnerabilities and is #2 in the Top 10 community survey, but also had enough data to make the Top 10 via data analysis. This category moves up from #9 in 2017 and is a known issue that we struggle to test and assess risk. It is the only category not to have any Common Vulnerability and Exposures (CVEs) mapped to the included CWEs, so a default exploit and impact weights of 5.0 are factored into their scores.

#### A07 Identification and Authentication Failures 
was previously Broken Authentication and is sliding down from the second position, and now includes CWEs that are more related to identification failures. This category is still an integral part of the Top 10, but the increased availability of standardized frameworks seems to be helping.

#### A08 Software and Data Integrity Failures 
is a new category for 2021, focusing on making assumptions related to software updates, critical data, and CI/CD pipelines without verifying integrity. One of the highest weighted impacts from Common Vulnerability and Exposures/Common Vulnerability Scoring System (CVE/CVSS) data mapped to the 10 CWEs in this category. A8:2017-Insecure Deserialization is now a part of this larger category.

#### A09 Security Logging and Monitoring Failures 
was previously A10:2017-Insufficient Logging & Monitoring and is added from the Top 10 community survey (#3), moving up from #10 previously. This category is expanded to include more types of failures, is challenging to test for, and isn't well represented in the CVE/CVSS data. However, failures in this category can directly impact visibility, incident alerting, and forensics.

#### A10 Server-Side Request Forgery 
is added from the Top 10 community survey (#1). The data shows a relatively low incidence rate with above average testing coverage, along with above-average ratings for Exploit and Impact potential. This category represents the scenario where the security community members are telling us this is important, even though it's not illustrated in the data at this time.


## 07. Gimana cara jadi Pentester?

#### XSS
https://github.com/payloadbox/xss-payload-list

#### SQL Injection
https://github.com/payloadbox/sql-injection-payload-list
