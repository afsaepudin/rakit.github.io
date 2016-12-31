---
layout: landing
lang: en
---

Rakit framework is another yet PHP micro framework to help you build web app or web service with ease.

<a id="rest"></a>
## [REST, of Course](#rest)

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
## [JSON? Easy](#json-response)
Just return an array to response JSON. We will <code>json_encode()</code> it for you.
```php
$app->get('/example.json', function() {
    return [
        'status' => 'ok',
        'message' => 'easy right?'
    ];
});
```

<a id="middleware"></a>
## [Yes, We Also Have Middleware](#middleware)

Register middleware

```php
$app->setMiddleware('auth', function($req, $res, $next) {
    if (!isset($_SESSION['user_id'])) {
        return $res->send('Unauthenticated Request', 401);          
    }

    return $next();
});
```

Use it

```php
$app->get('/admin', function() {
    return 'Hola Admin';
})->auth();
```

<a id="automatic-injection"></a>
## [Automatic Injection](#automatic-injection)

Hint a class you need in arguments. We will inject its instance automatically.

```php
...

use Rakit\Framework\Http\Request;

... 

$app->post('/login', function(Request $req) {
    $username = $req->get('username');
    $password = $req->get('password');
    ...
});
```

<a id="upload-file"></a>
## [Elegant Upload File Syntax](#upload-file)

Single upload file:

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

Access configuration or request data with ease using dot notation:

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
## [How About Using Controller Class?](#controller)

Ups, I almost forgot it. Here you go:

First, create your controller class:

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

Then you just need to put this in route:

```php
$app->post('/something', 'MyController@doSomething');
```
