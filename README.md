# Penetration Testing
###### Tulisan ini didedikasikan untuk para anggota grup [**Pentesting Indonesia**](https://t.me/PentestingIndonesia) di [Telegram](https://t.me/KamarKamsib). Github ini ada berisikan materi, konsep, best practice, contoh dokumen, hingga tools yang sering digunakan. Cekidot!

## Kontributor:
- [rad1zly](https://github.com/rad1zly)
- [anton](https://github.com/antonlepari)
- in progres ...
- in progres ...

_Penetration testing, also called pen testing, goes a step ahead of vulnerability scanning in security assessment. Unlike vulnerability scanning, which examines the security of individual computers, network devices, or applications, penetration testing assesses the security model of the network as a whole. Penetration testing can reveal the potential consequences of a real attacker breaking into the accounts of network-to-network administrators, IT managers, and executives. It also sheds light on the security weaknesses missed in typical vulnerability scanning._ [EC-Council]

Pentesting sebuah tes yang dilakukan dengan tujuan mencari kerenatanan pada sebua sistem. Sedikit berbeda dengan Vuln Scaning, Pentest menguji keamanan komputer individu, perangkat jaringan, atau aplikasi, pengujian penetrasi menilai model keamanan jaringan secara keseluruhan. Pentesting berbeda pula dengan kegiatan Bug Hunting atau Bug Bounty.

> Tetap haus dan jangan lupa berbagi.

## Metodologi
Beberapa metodologi pentest yang bisa dipakai, antara lain:

* PTES
* Lorem ipsum...
* dst....

## OWASP
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
moves up from the fifth position to the category with the most serious web application security risk; the contributed data indicates that on average, 3.81% of applications tested had one or more Common Weakness Enumerations (CWEs) with more than 318k occurrences of CWEs in this risk category. The 34 CWEs mapped to Broken Access Control had more occurrences in applications than any other category.

#### A02 Cryptographic Failures 
shifts up one position to #2, previously known as A3:2017-Sensitive Data Exposure, which was broad symptom rather than a root cause. The renewed name focuses on failures related to cryptography as it has been implicitly before. This category often leads to sensitive data exposure or system compromise.

#### A03 Injection 
slides down to the third position. 94% of the applications were tested for some form of injection with a max incidence rate of 19%, an average incidence rate of 3.37%, and the 33 CWEs mapped into this category have the second most occurrences in applications with 274k occurrences. Cross-site Scripting is now part of this category in this edition.

#### A04 Insecure Design 
is a new category for 2021, with a focus on risks related to design flaws. If we genuinely want to "move left" as an industry, we need more threat modeling, secure design patterns and principles, and reference architectures. An insecure design cannot be fixed by a perfect implementation as by definition, needed security controls were never created to defend against specific attacks.

#### A05 Security Misconfiguration 
moves up from #6 in the previous edition; 90% of applications were tested for some form of misconfiguration, with an average incidence rate of 4.5%, and over 208k occurrences of CWEs mapped to this risk category. With more shifts into highly configurable software, it's not surprising to see this category move up. The former category for A4:2017-XML External Entities (XXE) is now part of this risk category.

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


#### XSS
https://github.com/payloadbox/xss-payload-list

#### SQL Injection
https://github.com/payloadbox/sql-injection-payload-list
