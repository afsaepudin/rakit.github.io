---
layout: landing
lang: id
---

Rakit framework adalah PHP micro framework yang dapat membantu kamu membuat web app atau web service dengan mudah.
Rakit framework pada dasarnya terinspirasi dari Laravel dan Slim framework. 
Untuk itu beberapa penggunaan akan terlihat mirip-mirip dengan framework tersebut.

<a id="rest"></a>
## [Mendukung REST](#rest)

```php
$app->get('/get', function() {
    return 'You are access this using GET method';
});

$app->post('/post', function() {
    return 'You are access this using POST method';
});

$app->put('/put', function() {
    return 'You are access this using PUT method';
});

$app->patch('/patch', function() {
    return 'You are access this using PATCH method';
});

$app->delete('/delete', function() {
    return 'You are access this using DELETE method';
});
```

<a id="json-response"></a>
## [Menghasilkan JSON Lebih Mudah](#json-response)

Cukup return sebuah array untuk menghasilkan respon berupa json.

```php
$app->get('/example.json', function() {
    return [
        'status' => 'ok',
        'message' => 'easy right?'
    ];
});
```

<a id="middleware"></a>
## [Kami Juga Mendukung Middleware Loh](#middleware)


Mendaftarkan middleware

```php
$app->setMiddleware('auth', function($req, $res, $next) {
    if (!isset($_SESSION['user_id'])) {
        return $res->send('Unauthenticated Request', 401);          
    }

    return $next();
});
```

Lalu masukkan ke route untuk menggunakan middleware tersebut

```php
$app->get('/admin', function() {
    return 'Hola Admin';
})->auth();
```

<a id="automatic-injection"></a>
## [Automatic Injection](#automatic-injection)

Kami memiliki container yang dapat melihat kebutuhan method pada route handler dan constructor kamu.
Cukup beri hint pada argumen di route handler atau constructor kamu,
container kami akan meng-inject object yang diperlukan secara otomatis!

```php
...

use Rakit\Framework\Http\Request;

... 

$app->post('/login', function(Request $req) {
    $username = $req->get('username');
    $password = $req->get('password');
    ...
});

...
```

<a id="upload-file"></a>
## [Elegant Upload File Syntax](#upload-file)

Upload single file:

```php
$app->post('/profile', function(Request $req) {
    $avatar = $req->file('avatar');
    if ($avatar) {
        $avatar->name = 'new-name';
        $avatar->move('my/upload/dir');
    }
});
```

Multiple upload file:

```php
$app->post('/upload-images', function(Request $req) {
    $images = $req->multiFiles('images');
    foreach ($images as $i => $image) {
        $image->name = 'image-' . ($i + 1);
        $image->move('my/upload/dir');
    }
});
```

<a id="dot-notation"></a>
## [Dot Notation](#dot-notation)

Disini mengakses array konfigurasi dan request data lebih mudah dengan dot notation. Tidak perlu lagi isset-isset apalah itu.

```php
// init App with some configs
$app = new App('my-awesome-app', [
    'product' => [
        'image_path' => '/uploads/products',
    ] 
]);

...

$app->post('/something', function(App $app, Request $req) {
    $productImagePath = $app->config['product.image_path'];
    $productId = $req->get('product.id'); // will retrieve $_POST['product']['id']
    ...
});

...
```

<a id="controller"></a>
## [Menggunakan Controller Class Sebagai Route Handler](#controller)

Bisa kok, begini caranya:

Pertama, siapkan controller class dulu pastinya:

```php
<?php

use Rakit\Framework\Controller;

class MyController extends Controller
{

    public function doSomething()
    {
        // $this here refer to App instance
        $config = $this->config['any.config'];
        $file = $this->request->file('a_file');
    }

}
```

Setelah itu, kamu dapat mendaftarkan route dengan cara seperti ini:

```php
$app->post('/something', 'MyController@doSomething');
```

<a id="extensible"></a>
## [Kecil tapi Ekstensibel](#extensible)

<blockquote class="quote text-center">
  <strong>Install it when you need it</strong>
</blockquote>

Begitulah slogan rakit framework pada awal pembuatannya. 
Maksudnya adalah **cukup install apa yang kamu perlukan**.
Rakit framework sendiri dibuat berdasarkan beberapa keluhan terkait size Laravel yang dianggap berlebihan,
sementara beberapa fiturnya seringkali tidak diperlukan.

Framework ini pada dasarnya hanyalah router, http request-response, dan konfigurator.
Ukurannya sendiri hanya sekitar 200-300KB.
Namun seperti namanya 'rakit', kamu dapat merakit framework ini sesuai keperluan kamu.
Kamu dapat menambahkan kemampuan/layanan lain kedalam aplikasi
dengan fitur `ServiceProvider` layaknya Laravel.

Berikut beberapa paket yang juga sedang kami kembangkan:

<ul>
  <li>
    <a target="_blank" href="https://github.com/rakit/validation">Rakit/Validation</a>
    <br>
    Library validasi ala laravel dengan sizenya yang 10 kali lebih kecil dari `illuminate/validation`.
  </li>
  <li>
    <a target="_blank" href="https://github.com/rakit/blade">Rakit/Blade</a>
    <br>
    Jika kamu ingin menggunakan blade pada rakit.
  </li>
  <li>
    <a target="_blank" href="https://github.com/rakit/session">Rakit/Session</a>
    <br>
    Session manager dengan fitur Flash message. Mendukung driver PDO, Cookie, File, ataupun custom sesukamu.
  </li>
  <li>
    <a target="_blank" href="https://github.com/emsifa/Block">Emsifa/Block</a>
    <br>
    PHP native template system (bukan engine) yang terinspirasi dari Blade. 
    Hanya saja versi nativenya, jadi tidak butuh proses kompilasi dan caching, jadi performa lebih cepat.
  </li>
  <li>
    Dan akan hadir lebih banyak lagi. Apakah kamu salah satu kreatornya?
  </li>
</ul>
