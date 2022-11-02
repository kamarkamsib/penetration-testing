# CORS (Cross-Origin Resource Sharing)
## Definisi
Cross-Origin Resource Sharing atau bisa disebut CORS adalah sebuah mekanisme browser yang memungkinkan akses terkontrol ke *resource* yang berlokasi di luar domain tertentu. CORS juga bisa memberikan potensi serangan *cross-domain* jika tidak dikonfigurasi dan diterapkan dengan baik. Gambar dibawah ini merupakan 
![Skema Serangan CORS](SeranganCors.svg)
<p align="center">Sumber : https://portswigger.net/web-security/cors</p>

## Teknik Serangan
Berikut merupakan beberapa metode yang bisa dilakukan untuk mendeteksi adanya miskonfigurasi pada CORS.
* Metode pertama (satu target):
```
Tahap 1. Pasang target website yang ingin diuji dan lakukan crawl atau spider menggunakan Burpsuite. 
Tahap 2. Gunakan burpsuite untuk mencari apakah ada "Access-Control" pada website tersebut.
Tahap 3. Coba untuk menambahkan Origin Header, contohnya: Origin:penyerang.com atau Origin:null atau Origin:target.penyerang.com
Tahap 4. Jika Origin Header tersebut nantinya muncul pada response, menandakan bahwa target tersebut rentan terhadap CORS
```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
* Metode kedua (banyak target termasuk subdomain):
```
Tahap 1. cari subdomain dengan subfinder, contoh subfinder -d target.com -o domains.txt
Tahap 2. cek domain yang hidup/aktif dengan httpx: cat domains.txt | httpx | tee -a alive.txt
Tahap 3. kirim setiap domain yang aktif ke Burpsuite, misalnya dengan cara seperti ini, cat alive.txt | parallel -j 10 curl --proxy "http://127.0.0.1:8080" -sk 2>/dev/null
Tahap 4. Kemudian ulangi metode pertama dari setiap target yang didapat
(Metode ini membutuhkan tool subfinder dan httpx)
```
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
* Metode Ketiga (Otomatisasi), Berikut merupakan tool yang dibutuhkan :

   * [meg](https://github.com/tomnomnom/meg)
   * [gf](https://github.com/tomnomnom/gf)
   * [subfinder](https://github.com/projectdiscovery/subfinder)
   * [assetfinder](https://github.com/tomnomnom/assetfinder)
   * [findomain](https://github.com/Edu4rdSHL/findomain)
   * [httpx](https://github.com/projectdiscovery/httpx)

```
Tahap 1. Cari domain dengan subfinder, assetfinder, dan findomain. cth: subfinder -d target.com | tee -a hosts1 , findomain -t target.com | tee -a hosts1, assetfinder --subs-only target.com | tee -a hosts1
Tahap 2. Kemudian lakukan command berikut untuk melakukan sort: hosts1 | sort -u | tee -a hosts2 
Tahap 3. Kemudian lakukan command berikut untuk pengecekan domain aktif: cat hosts2 | httpx | tee -a hosts
Tahap 4. Pada terminal, pindah ke direktori dimana file 'hosts' berada/dibuat.
Tahap 5. kemudian ketik command berikut: meg -v
Tahap 6. Setelah selesai, ketik command berikut: gf cors.
Tahap 7. Semua URL yang mengandung "Access-Control-Allow" akan terlihat, yang berarti memiliki kerentanan CORS

```

