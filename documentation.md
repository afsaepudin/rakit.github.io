---
layout: documentation
title: Documentation
lang: en
---

<a id="getting-started"></a>
# Getting Started

<a id="requirements"></a>
## [Requirements](#requirements)

* PHP 5.5 or higher.
* Composer for installation

<a id="installation"></a>
## [Installation](#installation)

First, create your project directory.

```bash
cd /var/www
mkdir my-project
```

Then install rakit framework using composer command below:      

```bash
composer require "rakit/framework"
```

<a id="hello-world"></a>
## [Hello World](#hello-world)

Create index.php, and write scripts below:

```php
<?php
        
use Rakit\Framework\App;

require("vendor/autoload.php");

// initialize your app
$app = new App('my-awesome-app');

// register routes
$app->get('/', function() {
    return "Hello World!";
});

// run app
$app->run();
```
 
Start your server (in this case we are using PHP built-in server):

```bash
php -S localhost:8000
```

Open 'http://localhost:8000' in your browser, and voila!

<a id="guide"></a>
# Guide

<a id="routing"></a>
## [Routing](#routing)

TODO: write this
<a if="middleware"></a>
## [Middleware](#middleware)

TODO: write this

<a if="request"></a>
## [Request](#request)

TODO: write this

<a if="response"></a>
## [Response](#response)

TODO: write this

<a if="container"></a>
## [Container](#container)

TODO: write this
          