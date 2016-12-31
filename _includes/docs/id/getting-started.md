<a id="getting-started"></a>
# Memulai

<a id="requirements"></a>
## [Kebutuhan](#requirements)

* PHP 5.5 keatas.
* Composer untuk instalasi.

<a id="installation"></a>
## [Instalasi](#installation)

Pertama-tama, siapkan direktori proyekmu:

```bash
cd /var/www
mkdir my-project
```

Setelah itu, install rakit framework menggunakan perintah composer dibawah ini:      

```bash
composer require "rakit/framework"
```

<a id="hello-world"></a>
## [Hello World](#hello-world)

Buat file index.php, lalu ketikkan kode berikut:

```php
<?php
        
use Rakit\Framework\App;

require("vendor/autoload.php");

// inisialisasi App
$app = new App('my-awesome-app');

// mendaftarkan route
$app->get('/', function() {
    return "Hello World!";
});

// menjalankan app
$app->run();
```

Jalankan server (disini kami menggunakan PHP built-in server):

```bash
php -S localhost:8000
```

Kemudian buka 'http://localhost:8000' pada web browser, dan voila!
