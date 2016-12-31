
<a id="routing"></a>
## [Routing](#routing)

Berikut ini method yang dapat kamu gunakan untuk mendaftarkan route:

```php
$app->get('/get', function() {
    return 'Hanya dapat menerima metode GET';
});

$app->post('/post', function() {
    return 'Hanya dapat menerima metode POST';
});

$app->put('/put', function() {
    return 'Hanya dapat menerima metode PUT';
});

$app->patch('/patch', function() {
    return 'Hanya dapat menerima metode PATCH';
});

$app->delete('/delete', function() {
    return 'Hanya dapat menerima metode DELETE';
});

// mendaftarkan beberapa method sekaligus pada sebuah route
$app->router->map(['GET', 'POST', 'PUT'], '/some-methods', function() {
    return 'Route ini dapat menerima metode GET, POST, dan PUT';
});
```             

> Catatan: Setiap route pada rakit framework harus diawali dengan '/'

Untuk mendaftarkan parameter pada route, gunakan `:nama_parameter`seperti kode berikut:

```php
$app->get('/user/:username', function($username) {
    return "Halo $username";
});
```

Kode diatas memungkinkan kamu untuk mengakses '/user/foo', '/user/123', '/user/foo123', dsb.

Gunakan tanda kurung pada path dan parameter yang ingin dijadikan optional, lalu set nilai default pada argumen parameter tersebut.

Contoh:

```php
$app->get('/user(/:username)', function($username = 'Anonymous') {
    return "Halo $username";
});
```

> Jika tanda '/' pada `(/:username)`ditaruh diluar menjadi seperti `/user/(:username)`
  maka route tersebut tidak menerima '/user', melainkan harus '/user/'. 

Secara default, parameter akan menerima karakter apapun selain '/'. 
Jika kamu mengiginkan parameter hanya dapat menerima karakter tertentu, kamu bisa menambahkan `->where('param', 'regex')`
setelah mendefinisikan route. 

Contoh:

```php
$app->get('/user/:username', function() {
    return "Halo $username";
})->where('username', '[a-zA-Z0-9]+');
```

Dengan begitu route hanya akan dianggap cocok jika parameter 'username' terdiri dari rangkaian huruf dan angka (a-z, 0-9).
Bila tidak, aplikasi akan menampilkan halaman 404.

Route group digunakan untuk mengelompokan route kamu sehingga lebih mudah dibaca dan dimaintain jika sewaktu-waktu ada perubahan pada path atau middleware group tersebut.

Contoh menggunakan route group:

```php
$app->group('/admin', function($admin) {
    $admin->get('/dashboard', function() {
        return 'Halaman dashboard';
    });

    $admin->get('/settings', function() {
        return 'Halaman pengaturan';
    });

    $admin->post('/settings', function() {
        return 'Menyimpan pengaturan';
    });
});
```

Pada kode diatas, kita mendaftarkan 3 buah route, yaitu:

* GET '/admin/dashboard'
* GET '/admin/settings'
* POST '/admin/settings'
