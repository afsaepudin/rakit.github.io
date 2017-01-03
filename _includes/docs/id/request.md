<a id="request"></a>

## [Request](#request)

Objek request adalah objek yang menyimpan request data seperti `$_GET`, `$_POST`, `$_FILES`, request method, path, uri, dll.

#### [Mengambil Data GET atau POST](#mengambil-data-get-atau-post)

Pada rakit framework, `$_GET` dan `$_POST` disatukan, dimana jika terdapat key yang sama, maka key `$_POST` yang akan diambil.

Untuk mengambil data `$_GET` atau `$_POST`, gunakan method `get($key)`. 

Contoh:

```php
$app->post('/login', function() {
    // mengambil per key
    $username = $this->request->get('username');
    $password = $this->request->get('password');

    // mengambil hanya 'username' dan 'password'
    $data = $this->request->only(['username', 'password']);

    // mengmabil semua data, kecuali 'A', 'B', 'C' 
    $data = $this->request->except(['A', 'B', 'C']);
});
```

Perlu kamu ketahui, method `get` ini dapat menggunakan dot notation. Jadi untuk mengambil array kamu dapat melakukan seperti ini:

```php
$app->get('/products', function() {
    // mengambil $_GET['filter']['category']
    $filterCategory = $this->request->get('filter.category');
});
```

#### [Upload Single File](#upload-single-file)

Gunakan method `file($key)` untuk mengambil objek `UploadedFile`, kemudian kamu dapat mengupload file ke direktori proyek menggunakan method `move`.

Contoh:

```php
$app->post('/register', function() {
    $avatar = $this->request->file('avatar');
    // memastikan $avatar tidak kosong (NULL)
    if ($avatar) {
        $avatar->name = 'new-name'; // set nama file output
        $avatar->move('my/upload/dir');
    }
});
```

Jika kamu mengupload file `test.png`, maka file akan tersimpan di `my/upload/dir/new-name.png`.

#### [Upload Multiple File](#upload-multiple-file)

Mirip-mirip dengan single file. Hanya saja untuk mengambil multiple upload file method yang digunakan adalah `multiFiles($key)`. Nilai yang dikembalikan adalah array `UploadedFile`, jadi kamu perlu loop untuk menguploadnya satu per satu. 

```php
$app->post('/register', function() {
    $documents = $this->request->multipleFiles('documents');
    foreach ($documents as $document) {
        $document->move('my/upload/dir');
    }
});
```


#### [Mengambil Route yang Aktif](#mengambil-route-yang-aktif)

Untuk mengambil objek route yang sedang diakses, gunakan method `route()`.

```php
$app->get('/foo/bar/baz', function() {
    $currentRoute = $this->request->route();
    return $currentRoute->getPath(); // => '/foo/bar/baz'
});
```

#### [Cek Apakah Request Berupa AJAX](#cek-apakah-request-berupa-ajax)

Untuk mengetahui apakah request berupa AJAX, gunakan method `isAjax()`.

```php
$app->get('/foo/bar/baz', function() {
    if ($this->request->isAjax()) {
        return "request berupa ajax";
    } else {
        return "request bukan berupa ajax";
    }
});
```

#### [Mengecek atau Mendapatkan Request Method](#mengecek-atau-mendapatkan-request-method)

Untuk mengetahui atau mengecek request method yang digunakan, kamu dapat gunakan beberapa method ini:

* `isMethodPost()`: untuk mengetahui apakah request berupa POST?
* `isMethodGet()`: untuk mengetahui apakah request berupa GET?
* `isMethodPatch()`: untuk mengetahui apakah request berupa PATCH?
* `isMethodPut()`: untuk mengetahui apakah request berupa PUT?
* `isMethodDelete()`: untuk mengetahui apakah request berupa DELETE?
* `method()`: untuk mengambil method yang digunakan, sama seperti `$_SERVER['REQUEST_METHOD']`.

#### [Mengambil Path URL](#mengambil-path-url)

Untuk mengambil path dari url kamu dapat menggunakan methog `path()`. Contoh:

```php
// misal url = http://localhost:3000/foo/bar/baz?x=12&y=10
$app->get('/foo/bar/baz', function() {
    return $this->request->path(); // => '/foo/bar/baz'
});
```

#### [Mengambil URI Segment](#mengambil-uri-segment)

Untuk mengambil URI segment, gunakan method `segment($n)` dimana `$n` adalah urutan segment. Contoh:

```php
// misal url = http://localhost:3000/foo/bar/baz?x=12&y=10
$app->get('/foo/bar/baz', function() {
    return $this->request->segment(2); // => 'bar'
});
```

