---
description: Integrating the moltin PHP SDK into your existing application
---

# PHP SDK

[Sign up](https://dashboard.moltin.com/signup) or [login](https://dashboard.moltin.com/login) to the moltin dashboard to obtain your API keys. If you already have an existing moltin store, you can create additional stores inside the [accounts dashboard](https://accounts.moltin.com/).

#### Get Composer

The recommended way to install the SDK is to use [Composer](https://getcomposer.org/download/) to manage installation and creation of the autoload script.

```php
composer.json

{
  "require" : {
    "moltin/php-sdk" : "^2.0.0"
  }
}  
```

### Install the SDK

Add to, or create, `composer.json` in your project folder. Execute composer with the install parameter \(i.e. `composer install`\). This will download the Moltin PHP SDK and create `autoload.php` in the `vendor directory`.

```php
index.php

require 'vendor/autoload.php';
$moltin = new Moltin\Client([
  'client_id'     => 'XXXX',
  'client_secret' => 'XXXX'
]);
```

### Instantiate

Require the `autoload.php` file, and instantiate a Moltin client. You will need your store API keys to authenticate with our API. You can find your store keys in your [store dashboard](https://dashboard.moltin.com/).

