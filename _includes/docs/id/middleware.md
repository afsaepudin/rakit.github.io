
<a id="middleware"></a>

## [Middleware](#middleware)

Middleware adalah sebuah fungsi yang melapisi controller, atau middleware pada lapisan dibawahnya.
Middleware dapat digunakan untuk filter request, manipulasi response, setup beberapa hal, dsb.

Middleware pada rakit framework sendiri hanya dapat didaftarkan pada route atau route group.

Berikut contoh-contoh penggunaan middleware:

<a id="middleware-filtering"></a>

#### [Middleware untuk Filter Request](#middleware-filtering)

Ini adalah fungsi yang paling umum dari middleware. Yaitu untuk autentikasi dan autorisasi.

Pada contoh dibawah ini kita akan membuat middleware 'auth' yang berfungsi untuk mengecek
apakah user sudah login atau belum berdasarkan session. 
Jika belum, maka tampilkan respon 401 (Unauthenticated request).

Pertama kamu perlu mendaftarkan middleware 'auth' tersebut:

```php
$app->setMiddleware('auth', function($req, $res, $next) {
    if (!isset($_SESSION['user_id'])) {
        return $res->send('Unauthenticated request', 401);
    }

    return $next();
});
```

Setelah itu, middleware 'auth' tersebut dapat kamu gunakan 
pada route atau route group seperti ini:

```php
// mendaftarkan middleware pada route 
$app->get('/article/edit', 'ArticleController@formEdit')->auth();

// mendaftarkan middleware pada route group
$app->group('/admin', function($admin) {
    $admin->get('/article', 'ArticleController@pageIndex');
    $admin->get('/article/create', 'ArticleController@formCreate');
    $admin->post('/article/create', 'ArticleController@postCreate');
})->auth();
```

Pada contoh diatas, middleware 'auth' akan melapisi controller didalamnya, dan dijalankan terlebih dahulu
sebelum controller. Middleware tersebut akan mengecek apakah terdapat session 'user_id' atau tidak.
Jika tidak ada, aplikasi akan menampilkan 'Unauthenticated Request' dengan status 401.
Jika ada, middleware akan meneruskan request ke controller (lapisan dibawahnya) dengan perintah `$next()`.

> Perlu diingat kalau middleware dan controller pada rakit framework selalu menggunakan `return`untuk
  mengembalikan hasil.

<a id="middleware-manipulate"></a>

#### [Middleware untuk Manipulasi Response](#middleware-manipulate)

Terkadang kamu mungkin mau mengubah respon atau hasil yang dikirimkan oleh controller seperti menghapus karakter ilegal,
replace sesuatu, menginsert sesuatu ke JSON yang dihasilkan, dsb.

Pada contoh berikut ini kita akan membuat middleware 'api' yang berguna untuk menambahkan 'status' ke dalam JSON.

1) Mendaftarkan middleware:

```php
$app->setMiddleware('api', function($req, $res, $next) {
    $result = $next();
    return array_merge([
      'status' => 'ok'
    ], $result);
});
```

2) Mendaftarkan middleware ke route.

```php
$app->get('/api/something', function() {
    return [
        'message' => 'Hola!'
    ];
})->api();
```

Pada contoh diatas, jika kamu mengakses '/api/something' maka hasilnya adalah JSON sebagai berikut:

```javascript
{
    "status": "ok",
    "message": "Hola!"
}
```

<a id="middleware-parameter"></a>

#### [Middleware dengan Parameter](#middleware-parameter)

Seringkali kita mendapatkan kasus dimana aplikasi yang kita kembangkan memiliki berbagai macam kategori user, seperti 'admin', 'guru', 'murid'.
Jika kamu ingin membuat middleware untuk autentikasi 3 kategori user tersebut, terdapat 2 cara, yaitu: 

1. Buat 3 buah middleware untuk masing-masing kategori. 
2. Cukup buat sebuah middleware yang menerima parameter.

Berikut contoh penggunaan middleware dengan parameter untuk kasus tersebut:

1) Mendaftarkan middleware. Tambahkan parameter `$category`pada akhir `Closure`.

```php
$app->setMiddleware('auth', function($req, $res, $next, $category) {
    // cek apakah user sudah login
    if (!isset($_SESSION['user']) OR !is_array($_SESSION['user'])) {
        return $res->send('Anda belum login', 401);
    }

    // cek apakah kategori user sesuai $category
    if ($_SESSION['user']['category'] != $category) {
        return $res->send('Anda tidak memiliki hak akses ke halaman ini', 403);
    }

    return $next();
});
```

2) Mendaftarkan middleware ke route. Masukkan parameter ke middleware `auth`yang telah dibuat.

```php
$app->get('/admin/dashboard', function() {
    return "Halo admin";
})->auth('admin');

$app->get('/guru/dashboard', function() {
    return "Halo guru";
})->auth('guru');

$app->get('/murid/dashboard', function() {
    return "Halo Murid";
})->auth('murid');
```