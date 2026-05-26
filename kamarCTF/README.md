# 🚩 Kamar CTF — Pojok Pembelajaran & Latihan

Sub-direktori **kamarCTF** ini didedikasikan lebih khusus lagi bagi para penghuni **Kamar CTF** di Kamar Kamsib yang bahkan di malam minggu masih asyik memecahkan tantangan (*solving challenges*) Capture The Flag!

Di sini kami mengumpulkan daftar platform latihan, pembagian kategori tantangan CTF, serta pustaka alat bantu (*tools*) yang wajib dimiliki oleh setiap pemain CTF.

---

## 🎮 1. Platform Latihan CTF Terbaik

Bagi pemula maupun pemain tingkat lanjut, berikut adalah platform web terbaik di dunia untuk melatih keahlian CTF Anda:

| Platform | Keunggulan Utama | Tingkat Kesulitan |
| :--- | :--- | :--- |
| **[PicoCTF](https://picoctf.org/)** | Dibuat oleh Carnegie Mellon University, sangat ramah untuk pemula total dengan panduan lengkap. | 🟢 Pemula |
| **[CTFtime](https://ctftime.org/)** | Kalender global kompetisi CTF internasional. Wajib diikuti untuk memantau turnamen aktif mingguan. | 🟡 - 🔴 Semua Tingkat |
| **[Root-Me](https://www.root-me.org/)** | Lab interaktif gratis dengan ratusan tantangan yang mencakup berbagai kategori keamanan. | 🟡 Menengah |
| **[Hack The Box (HTB)](https://www.hackthebox.com/)** | Lab penetrasi mesin virtual (*boxes*) dan tantangan CTF kustom terpopuler di dunia. | 🔴 Menengah - Ahli |
| **[PortSwigger Web Academy](https://portswigger.net/web-security)** | Lab gratis berfokus 100% pada kerentanan aplikasi web modern. | 🟡 - 🔴 Semua Tingkat |
| **[CryptoHack](https://cryptohack.org/)** | Platform belajar kriptografi modern secara menyenangkan menggunakan Python. | 🔴 Menengah - Ahli |

---

## 🛠️ 2. Pembagian Kategori CTF & Alat Bantu (Tools)

Kompetisi CTF umumnya dibagi menjadi beberapa divisi utama. Berikut adalah daftar tools andalan untuk masing-masing divisi:

### 🎨 A. Web Exploitation (Kerentanan Aplikasi Web)
* **[Burp Suite Community/Pro](https://portswigger.net/burp)**: Proxy lokal interseptor HTTP.
* **[Dirsearch](https://github.com/maurosoria/dirsearch)** / **[Gobuster](https://github.com/OJ/gobuster)**: Brute-force direktori web tersembunyi.
* **[CyberChef](https://gchq.github.io/CyberChef/)**: Alat serbaguna berbasis web untuk melakukan berbagai macam encoding, decoding, enkripsi, dan manipulasi data.
* **[SQLmap](https://github.com/sqlmapproject/sqlmap)**: Deteksi & eksploitasi SQL Injection otomatis.

### 🕵️ B. Forensics & Steganography (Analisis Data & Pesan Tersembunyi)
* **[Wireshark](https://www.wireshark.org/)**: Analisis file rekaman paket jaringan (`.pcap`).
* **[Autopsy](https://www.autopsy.com/)**: Platform investigasi forensik digital pada image disk harddisk/USB.
* **[ExifTool](https://exiftool.org/)**: Membaca metadata gambar, audio, dan video.
* **[Stegsolve](https://github.com/zardus/ctf-tools/blob/master/stegsolve/install)**: Menganalisis layer gambar warna untuk mendeteksi steganografi visual.
* **[Binwalk](https://github.com/ReFirmLabs/binwalk)**: Menganalisis dan mengekstrak file tersembunyi di dalam berkas binary lain.

### ⚙️ C. Reverse Engineering (Rekayasa Balik Aplikasi)
* **[Ghidra](https://ghidra-sre.org/)**: Alat dekompiler dan disassembler open-source kelas dunia buatan NSA.
* **[IDA Pro / IDA Free](https://hex-rays.com/ida-free/)**: Decompiler biner paling populer.
* **[gdb / GEF](https://github.com/hugsy/gef)**: Debugger Linux GDB dengan ekstensi ramah pemain CTF.
* **[cutter](https://cutter.re/)**: GUI decompiler berbasis framework Rizin.

### 💥 D. Binary Exploitation / PWN (Eksploitasi Memori)
* **[Pwntools](https://github.com/Gallopsled/pwntools)**: Pustaka Python wajib untuk menulis exploit biner lokal maupun remote.
* **[checksec](https://github.com/slimm609/checksec.sh)**: Mengecek proteksi biner (NX, ASLR, Canary, PIE).
* **[one_gadget](https://github.com/david942j/one_gadget)**: Mencari alamat eksekusi shell sekali panggil pada pustaka libc.

### 🔑 E. Cryptography (Kriptografi & Pemecahan Sandi)
* **[RsaCtfTool](https://github.com/project-alchemist/RsaCtfTool)**: Pemecah kunci privat RSA jika parameter publiknya lemah.
* **[dcode.fr](https://www.dcode.fr/)**: Mesin pemecah cipher klasik terlengkap online.
* **[Hashcat](https://hashcat.net/hashcat/)** / **[John the Ripper](https://www.openwall.com/john/)**: Alat bruteforce hash offline berkemampuan GPU.

---

## 📈 3. Komunitas & Tips
1. **Belajar Menulis Write-Up (WU)**: Menulis write-up setelah kompetisi selesai sangat mempercepat proses pemahaman Anda. Anda juga bisa membaca write-up orang lain di repositori publik atau blog.
2. **Main Bersama Tim**: CTF paling asyik dimainkan bersama tim. Diskusikan temuan dan berbagilah tugas di grup Kamar CTF.
3. **Keep Calm and Solve Challenges!**
