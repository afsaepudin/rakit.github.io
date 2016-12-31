
<a id="configuration"></a>
## [Konfigurasi](#configuration)

Rakit framework pada dasarnya dapat berjalan tanpa konfigurasi apapun.
Namun seringkali kita ingin menyimpan beberapa pengaturan pada aplikasi,
Rakit framework menyajikan beberapa API untuk mempermudah hal tersebut. 

Berikut adalah beberapa hal yang mesti kamu ketahui tentang konfigurasi pada Rakit framework:  

<a id="config-get-set"></a>
#### [Get/Set Konfigurasi](#config-get-set)

Konfigurasi pada rakit framework tersimpan pada objek `config`
yang mengimplementasikan `ArrayAccess`. 
Untuk itu, kamu dapat menyimpan dan mengambil konfigurasi layaknya memberikan dan mengambil nilai dari array.

Berikut contohnya:

```php
// set konfigurasi 'something'
$app->config['something'] = 'value';

// mengambil konfigurasi 'something'
echo $app->config['something'];
```

<a id="config-dot-notation"></a>
#### [Memahami Dot Notation pada Konfigurasi](#config-dot-notation)

Perlu kamu ketahui, rakit framework memiliki kemampuan untuk mengambil konfigurasi dengan dot notation.
Jika pada array biasa kamu melakukan hal seperti ini:

<pre><code class="php">// set konfigurasi
$configs['database'] = [
    'host' => 'localhost',
    'username' => 'root',
    'password' => 's3cR3t',
    'name' => 'my_db'
];

...

// mengambil konfigurasi (pada file lain)
$db_host = isset($configs['database']['host']) ? $configs['database']['host'] : 'localhost';
$db_username = isset($configs['database']['username']) ? $configs['database']['username'] : 'root';
$db_password = isset($configs['database']['password']) ? $configs['database']['password'] : '';
</code></pre>

Dengan rakit framework, kode diatas akan jadi seperti ini'

```php
// set konfigurasi
$app->config['database'] = [
    'host' => 'localhost',
    'username' => 'root',
    'password' => 's3cR3t',
    'name' => 'my_db'
];

...

// mengambil konfigurasi (pada file lain)
$db_host = $app->config['database.host'] ?: 'localhost';
$db_username = $app->config['database.username'] ?: 'root';
$db_password = $app->config['database.password'] ?: '';
```

Lebih enak dipandang bukan? ya itulah kegunaan dot notation. Untuk meminimalisir isset-isset itu.

> Jika kunci (key) yang diakses dengan dot notation tidak ada, nilai yang dikembalikan adalah `null`

<a id="config-load-dir"></a>
#### [Load Konfigurasi pada Direktori](#config-load-dir)

Jika kamu pernah menggunakan framework-framework full stack seperti Laravel, Codeigniter, dsb kamu
akan menemukan kalau file konfigurasi dikumpulkan kedalam sebuah direktori yang dikelompokkan kedalam file.
Rakit framework memfasilitasi hal tersebut. Kamu dapat memuat seluruh file konfigurasi pada direktori dengan perintah `loadDir($directory)`.

Pada contoh dibawah ini, dimisalkan kita memiliki 2 buah file konfigurasi pada direktori 'configs' sebagai berikut:

```php
<?php
// file configs/app.php
return [
    'base_url' => 'http://localhost:3000',
    'debug' => true
];
```

```php
<?php
// file configs/database.php
return [
    'host' => 'localhost',
    'username' => 'root',
    'password' => 's3cR3t',
    'name' => 'my_db'
];
```              

Untuk mendaftarkan konfigurasi pada direktori 'configs' tersebut, gunakan perintah `loadDir`seperti dibawah ini:

```php
<?php
// file index.php

use Rakit\Framework\App;

require('vendor/autoload.php');

$app = new App('my-awesome-app');

// load configs
$app->config->loadDir(__DIR__.'/configs');

// then here is the results
echo $app->config['app.base_url'];        // http://localhost:3000
echo $app->config['app.debug'];           // 1 (true)
echo $app->config['database.host'];       // localhost
echo $app->config['database.username'];   // root
echo $app->config['database.password'];   // s3cR3t
echo $app->config['database.name'];       // my_db
```              

Ya, nama file akan menjadi prefix dari masing-masing konfigurasi.

<a id="config-debug"></a>
#### [Mengaktifkan Mode Debug](#config-debug)

Secara default, rakit framework menonaktifkan mode debug. 
Jadi jika terjadi error, browser hanya akan menampilkan 'Something went wrong'.

Untuk mengaktifkan mode debug, set konfigurasi `app.debug`menjadi `true`seperti dibawah ini:

```php
<?php
// file index.php

use Rakit\Framework\App;

require('vendor/autoload.php');

$app = new App('my-awesome-app');
$app->config['app.debug'] = true;
```

Dengan begitu, jika terjadi error, aplikasi akan menampilkan sesuatu seperti ini:

<img src="/assets/img/sample-error.png" alt="Sample error">
