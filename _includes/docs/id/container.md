<a id="container"></a>

## [Container](#container)

Container adalah tempat menampung dan mengelola layanan atau objek pada aplikasi. 
Setiap instance rakit framework sendiri seperti App, Router, Http Request, Http Response, 
Configurator, dsb tersimpan di dalam sebuah Container sehingga kamu dapat mengaksesnya dengan mudah pada bagian lain dari aplikasi.

Objek container pada rakit framework sendiri berada di `$app->container`.

Container pada rakit framework mengimplementasikan `ArrayAccess`, jadi kamu dapat mengakses container layaknya mengakses sebuah array. 

<a id="container-basic"></a>

#### [Contoh Sederhana](#container-basic)

Berikut ini adalah beberapa cara mendaftarkan objek sederhana kedalam container.

```php
// cara 1
$app->container->register(PDO::class, new PDO($dsn));

// cara 2 
$app->container[PDO::class] = new PDO($dsn);

// cara 3
$app[PDO::class] = new PDO($dsn);
```

<blockquote>
    <small>
        Buat kamu yang belum tahu, `PDO::class` adalah string nama class dari PDO. 
        Konstanta `class` ini diperkenalkan sejak PHP versi 5.5.
    </small>
</blockquote>

Setelah mendaftarkan nilai kedalam container, kamu dapat mengambil nilai tersebut dengan beberapa cara dibawah ini:

```php
// cara 1
$cara1 = $app->container->get(PDO::class);

// cara 2 
$cara2 = $app->container[PDO::class];

// cara 3
$cara3 = $app[PDO::class];
```

Sebenarnya ada satu cara lagi yang lebih singkat dan mungkin lebih nyaman. Yaitu:

```php
// mendaftarkan
$app->pdo = new PDO($dsn);

// mengambil
$pdo = $app->pdo;
```

Hanya saja, cara tersebut tidak dapat kamu gunakan untuk automatic injection 
karena `key` container ('pdo') tidak sama dengan nama classnya ('PDO').
Cara ini lebih cocok jika kamu ingin nilai hanya dapat diakses langsung melalui instance dari app.

Jika kamu ingin mendaftarkan sebuah nilai menggunakan beberapa key sekaligus, kamu dapat gunakan cara no.1, yaitu `register`.
Contoh:


```php
$app->container->register(['pdo', PDO::class], new PDO($dsn));

// mengambil dan membandingkan nilai
var_dump($app->pdo === $app[PDO::class]); // true
```

> Pada perbandingan diatas hasilnya `true` karena instance bersifat singleton didalam container.

<a id="lazy-loading"></a>

#### [Lazy Loading](#lazy-loading)

Pada container, lazy loading adalah teknik dimana instance dibuat hanya jika dibutuhkan.
Pada contoh diatas kita mendaftarkan objek PDO, padahal belum tentu setiap route menggunakan objek PDO tersebut.
Hal ini dapat menambah beban memori ke aplikasi kamu. Untuk itulah lazy loading seringkali diperlukan.

Untuk menerapkan lazy loading, kamu perlu membungkus instantiator (pembentuk instance) kedalam sebuah Closure seperti dibawah ini:

```php
$app[PDO::class] = function() use ($app) {
    $dsn = $app->config['database.dsn'];
    return new PDO($dsn);
};
```

Dengan begitu, objek PDO hanya dibuat saat aplikasi mengambil objek PDO tersebut.

**CATATAN PENTING**: 
saat mendaftarkan layanan dengan Closure seperti kode diatas, objek akan 
dibuat instancenya setiap kali kamu mengakses objek tersebut. 

Contoh pada PDO diatas:

```php
$pdo1 = $app[PDO::class];
$pdo2 = $app[PDO::class];

var_dump($pdo1 === $pdo2); // hasilnya false
var_dump($app[PDO::class] === $app[PDO::class]); // hasilnya false
```

Untuk itu, jika kamu menginginkan instance yang sama sekaligus lazy loading, kamu dapat mencoba method `singleton`
yang akan dijelaskan pada bagian selanjutnya.

<a id="singleton"></a>

#### [Singleton](#singleton)

Singleton adalah design pattern dimana sebuah class hanya dapat memiliki 1 instance saja.
Jika kamu pernah mempelajari pattern ini, singleton yang dimaksud pada bagian ini 
bukanlah singleton yang ditangani oleh class itu sendiri dimana contructor pada class dibuat protected atau private.
Tapi singleton disini adalah bagaimana container aplikasi hanya memberikan sebuah instance yang sama pada setiap kali pengambilan.

Untuk mendaftarkan objek supaya menjadi singleton, ada 2 cara, yaitu:

1. Mendaftarkan instance secara langsung seperti [contoh sederhana](#container-basic) diatas. 
   Dengan cara ini, objek tidak bersifat lazy loading.
2. Menggunakan `$app->container->singleton`. 
   Dengan cara ini, objek menjadi bersifat lazy loading.

Berikut contoh menggunakan cara kedua masih pada kasus PDO seperti sebelumnya:

```php
$app[PDO::class] = $app->container->singleton(function() use ($app) {
    $dsn = $app->config['database.dsn'];
    return new PDO($dsn);
});

// contoh untuk memastikan instance sama
$pdo1 = $app[PDO::class];
$pdo2 = $app[PDO::class];
var_dump($pdo1 === $pdo2); // true
```

<a id="automatic-injection"></a>

#### [Automatic Injection](#automatic-injection)

Container pada rakit framework memiliki kemampuan untuk membuat instance suatu class
secara otomatis dengan memperhatikan dependency dari constructor class tersebut (constructor injection).
Selain itu, container juga dapat memanggil method atau function dengan memperhatikan 
dependency dari method atau function tersebut (callable injection).

Rakit framework akan melakukan injeksi otomatis ke route handler dan constructor controller kamu. 
Namun kamu dapat melakukan constructor injection dengan `$app->container->make` 
dan callable injection dengan `$app->container->call`.

Sebagai contoh, pertama kamu daftarkan PDO kedalam aplikasi seperti pada contoh-contoh sebelumnya:

```php
$app[PDO::class] = $app->container->singleton(function() {
    return new PDO('mysql:host-localhost;dbname=mydb', 'username', 'password');
});
```

Setelah itu, berikut beberapa contoh penerapan automatic injectionnya:

1) Pada route handler

```php
$app->get('/product/detail/:id', function($id, PDO $pdo) {
    // lakukan sesuatu dengan PDO disini
})->where('id', '\d+');
```

2) Pada constructor class controller

```php
<?php

use Rakit\Framework\Controller;
use Rakit\Framework\App;

class ProductController extends Controller
{
    public function __construct(App $app, PDO $pdo)
    {
        parent::__construct($app);
        $this->pdo = $pdo;
    }

    public function detail($id)
    {
        // lakukan sesuatu dengan $this->pdo
    }

    public function delete($id)
    {
        // lakukan sesuatu dengan $this->pdo
    }    
}

```

> Pemanggilan `parent::__construct` digunakan supaya controller dapat mengakses instance dari App dengan `$this`.

3) Menggunakan constructor injection. Disini dicontohkan kita ingin injeksi PDO kedalam class `ProductRepository`. 

Pertama buat dulu `ProductRepository.php`-nya:

```php
<?php

// file: repositories/ProductRepository.php

class ProductRepository
{

    public function __construct(PDO $pdo, $arg1, $arg2)
    {
        $this->pdo = $pdo;
        $this->arg1 = $arg1;
        $this->arg2 = $arg2;
    }

    public function findById($id)
    {
        // lakukan sesuatu dengan $this->pdo
    }    

}
```

Kemudian kamu dapat membentuk instance dari `ProductRepository` dengan cara berikut:

```php
$productRepository = $app->container->make(ProductRepository::class, [$arg1, $arg2]);
$product = $productRepository->findById(5);
```

4) Menggunakan callable injection.

Dimisalkan kita mempunyai function seperti ini:

```php
function do_something(PDO $pdo) {
    // do something with PDO
}
```

Kamu dapat inject PDO secara otomatis dengan cara dibawah ini:

```php
$result = $app->container->call('do_something');
```

Callable injection tidak hanya berlaku pada function saja, tapi juga berlaku pada jenis callable lainnya.

<a id="binding-interface"></a>

#### [Binding Interface](#binding-interface)

Katakanlah kamu memiliki class `MysqlBuilder` yang berisi query builder sederhana untuk CRUD dengan database mysql, 
dan class tersebut sudah kamu inject ke 20 class yang membutuhkannya.
Tapi ternyata suatu saat kamu diharuskan pindah ke SQL Server, dengan begitu kamu harus membuat `SqlServerBuilder`, 
kemudian mengubah 20 class yang sebelumnya menggunakan `MysqlBuilder`. 

Cara seperti itu tidaklah efisien.

Solusinya adalah kamu membuat interface `QueryBuilderInterface`, 
dimana `MysqlBuilder` mengimplementasikan interface tersebut. 
Kemudian pada 20 class yang akan di-inject, gunakan `QueryBuilderInterface` sebagai type hint-nya.

Jadi jika sewaktu-waktu ada perubahan dari mysql ke sql server, postgresql, oracle atau apapun, 
kamu cukup membuat class yang mengimplementasikan `QueryBuilderInterface` tersebut.
Container rakit framework akan membantu kamu melakukan adaptasi secara otomatis dengan binding interface.

Tidak ada method spesial untuk melakukan binding interface, karena pada dasarnya `key` yang 
didaftarkan ke container dapat berupa apapun, tidak harus sama dengan classnya.
Jadi kamu bisa saja menggunakan interface sebagai `key`nya seperti dibawah ini:

```php
$app[QueryBuilderInterface::class] = function () {
    return new MysqlBuilder;
};
```

Dan sewaktu-waktu kamu ingin merubah mysql ke sql server, kamu cukup mengubah kode tersebut menjadi:

```php
$app[QueryBuilderInterface::class] = function () {
    return new SqlServerBuilder;
};
```

Dengan begitu, semua constructor ataupun callable yang di-inject menggunakan `container->make` ataupun `container->call`
akan menerima instance dari `SqlServerBuilder` (bukan lagi `MysqlBuilder`).
