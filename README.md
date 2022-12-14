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
- Tahapan Umum
- - Reconnaissance and Information Gathering
- - Network Enumeration and Scanning
- - Vulnerability┬áTesting and Exploitation
- - Reporting 
- OWASP
- Gimana cara jadi Pentester?
- Payload list

## 01. Apa sih Penetration Testing itu?

Pentesting merupakan sebuah tes yang dilakukan dengan tujuan mencari kerenatanan pada sebua sistem. Sedikit berbeda dengan Vuln Scaning, Pentest menguji keamanan komputer individu, perangkat jaringan, atau aplikasi, pengujian penetrasi menilai model keamanan jaringan secara keseluruhan. Pentesting berbeda pula dengan kegiatan Bug Hunting atau Bug Bounty.

> Tetap haus dan jangan lupa berbagi.

## 02. Emang siapa aja yang butuhin pentesting?

Mari kita lihat dari sudut pandang tujuan pentesting. Tujuan pengujian penetrasi adalah untuk membantu bisnis menemukan di mana mereka paling mungkin menghadapi serangan dan secara proaktif menopang kelemahan tersebut sebelum dieksploitasi oleh peretas. Dari tujuan tersebut, pentesting ini bisa dibutuhkan oleh siapa saja yang memiliki Sistem Elektronik yang berjalan. Khususnya sistem yang dapat diakses oleh publik.

## 03. Lah kan udah ada bug bounty, terus pentesting buat apa?
Ada perbedaan bug bounty dengan pentesting.

https://www.hackerone.com/penetration-testing/bug-bounty-vs-penetration-testing-differences-explained

## 04. Metodologi
Beberapa metodologi pentest yang bisa dipakai, antara lain:

* PTES

Standar pelaksanaan pentesting terdiri dari tujuh (7) bagian utama. Ketujuh bagian ini mencakup semua hal terkait pentest ÔÇĽ mulai dari fase komunikasi awal dan alasan mengepa pentest dibutuhkan, hingga fase pengumpulan intelijen (_intelligence gathering_) dan pemodelan ancaman (_threat modeling_).

* NIST CSF
* OWASP Testing Framework
* Lorem ipsum...
* dst....

## 05 Tahapan Umum

### 05.1 :mag: Reconnaissance dan Information Gathering
Tujuan: Untuk menemukan informasi sebanyak mungkin mengenai target (baik itu individual ataupun organisasi). Pada tahap ini, SEBISA MUNGKIN dilakukan tanpa melalukan kontak langsung dengan jaringan target.

Cara:
- Menggunakan WHOIS
- Google searching
- Website browsing

### 05.2 Network Enumeration and Scanning
Tujuan:┬á Untuk menemukan layanan atau sistem apa saja yang dimiliki target. Khususnya sistem yang sedang berjalan (live hosts dan services).

Cara: 
- Melalukan scanning ke target menggunakan Nmap
- DNS Querying
- Route analysis (traceroute) 

### 05.3 Vulnerability┬áTesting and Exploitation
Tujuan:┬á Untuk mengecek apakah host tersebut memiliki kerentanan (_vulnerabilities_) dan melihat apakah kerentanan tersebut bisa dieksploitasi atau tidak. Serta untuk mengukur tingkat keparahan (_assess the potential severity_) dari vulnerabilities tersebut. 

Cara:┬á 
- Remote vulnerability scanning (Nessus, OpenVAS)
- Active exploitation testing
- - Login checking and bruteforcing
- - Vulnerability exploitation (Metasploit, Core Impact)
- - 0day and exploit discovery (Fuzzing, program analysis)
- - Post exploitation techniques to assess severity (permission levels, backdoors, rootkits, etc)

### 05.4 :blue_book: Reporting
Tujuan:┬á Untuk mendokumentasikan ketiga fase sebelumnya.  

Cara:┬á┬á
- Documentation tools (Dradis)
- Organizes information by hosts, services, identified hazards and risks, recommendations to fix problems 
- Format dokumen (biasanya tiap instansi punya)

## 06. OWASP
> Dalam menyusun tulisan ini, @kamarkamsib menggunakan OWASP sebagai kiblat.
> situs : [OWASP](https://owasp.org/www-project-top-ten/)

Open Web Application Security Project┬« (OWASP) adalah yayasan nirlaba yang bekerja untuk meningkatkan keamanan perangkat lunak. Melalui proyek perangkat lunak sumber terbuka yang dipimpin komunitas, ratusan cabang lokal di seluruh dunia, puluhan ribu anggota, dan konferensi pendidikan dan pelatihan terkemuka. 

OWASP Foundation adalah sumber bagi pengembang dan ahli teknologi untuk mengamankan web. Salah satu project populer yang dihadirkan oleh yayasan ini adalah OWASP TOP 10. 

### 06.1 OWASP TOP 10

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
- Tetap up to date pada perkembangan terbaru dalam keamanan komputer, membaca buletin dan laporan keamanan adalah cara yang baik untuk melakukannya.
- Menguasai bahasa perograman XXXX-X
- Microsoft, Cisco, and Novell certifications
- Penetration Testing Certifications
- Certified Ethical Hacker (CEH)
- GIAC Certified Penetration Tester (GPEN)

## 99. Payload List
* XSS https://github.com/payloadbox/xss-payload-list
* SQL Injection https://github.com/payloadbox/sql-injection-payload-list
* xxx
* yyy
* zzz

## 100. :desktop_computer: Persyaratan Pekerjaan (Umumnya)
S
* Modern penetration testing tools and methods
* Konsep Network security
* Konsep Web-based application security
* Windows/Linux/UNIX internals
* Teknik dan taktik Social engineering techniques
* Memiliki pengalaman di program bug bounty yang diselenggarakan oleh provider lokal atau internasional
* Memiliki sertifikasi di bidang security assessment seperti CEH, OSCP, LTP, dsb.
* Memiliki pengalaman dalam pengunaan multiple interpreted languages (Ruby, Python, PHP, etc.)
* Memiliki pengalaman dalam penggunaan multiple compiled languages (Java, C, C++, Assembly, etc.)
* Memiliki keahlian dalam penulisan laporan dan presetasi.
* Memiliki pengetahuan terhadap peraturan yang diselenggarakan regulator dan IT Governance.
* Mampu secara efektif memimpin tim pentester ketika proyek berjalan.
* Mampu menjelaskan temuan pada pentest berikut rekomendasinya kepada user teknis maun non-teknis.

J
* Pengalaman dalam penilaian kerentanan dan pengujian penetrasi minimal 1 tahun
* Mampu bekerja dalam tim, cepat belajar, sikap yang baik
* Keterampilan komunikasi yang baik, mampu menulis atau berbicara dalam bahasa Inggris
* Akrab dengan pengujian penetrasi dan laporan
* Familiar dengan program CTF dan bug bounty hunter minimal 1 tahun

### 100.1 Daftar persyaratan kerja diolah dari lowongan Junior/Senior Penetration Tester di: PT Juke Solusi Teknologi, PT X, PT Z, ...

### Cyber Security Engineer
ANOTECH Singapore
SGD5.000 - 13.000/Bulan
Software Engineering
Full-Time
5 - 10 tahun pengalaman
**Responsibilities**
* Determining and defining cybersecurity requirements from clients in relation to the sales and account management team
* Recommending cybersecurity solutions/configurations, with regards to the clientsÔÇÖ requirements (proposals, bill of material, etc.)
* Conduct and perform security monitoring, investigations, and analysis
* Provide Security in Depth visibility into potential threats that may pose risk
* Support detection, incident analysis, containment, remediation and reporting of events/incidents
* Coordinating, anticipating, and balancing emerging and actual threats to ensure the integrity of assets
* Analyse and respond to security threats from Firewall (FW), Intrusion Detection Systems (IDS), Intrusion Prevention Systems (IPS), Antivirus (AV), Endpoint Detection and Response (EDR) and other security threat data sources
* Respond in a timely manner (SLA)
* Conduct Operations surrounding cyber security incident response technologies including network logging and forensics, security information, security analytics platforms, etc.
* Act as an internal information security consultant to the business and technology units, advising on risks, threats, and control practices
* Utilise analytics to identify potential threats to the environment
* Collaborate with technical leads and respective stakeholders
* Adhere to policies, procedures, and security practices
* Resolve problems independently and understand escalation procedures
**Requirements**
* At least 5 years of IT experience
* Excellent communication skills and team player
* Strong knowledge regarding the current and emerging threats (security)
* Industry related certifications will be an advantage but not mandatory (CISSP, Security+, C|EH, GSEC, etc.)
* Strong knowledge in:
* TCP/IP networking
* Linux, Windows, iOS, and Network Operating Systems
* EDR and SIEM technologies
* Working knowledge of Routing and Access Control Devices
* Cisco networking solution (campus LAN & DC switches, routers, wireless)
* Next generation firewall solution (Palo Alto, Checkpoint, Fortigate, etc.)
* Cisco Security (Identity Service Engine, etc.)
* Secure Access Service Edge (SASE) solution

# Latihan Menggunakan Santa THM 
Task 8  [Day 3] OSINT Nothing escapes detective McRed
Task 9  [Day 4] Scanning Scanning through the snow

