# Integrasi SSO-Kota Gorontalo menggunakan PHP

Library ini menggunakan dan support PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Instalasi

Instalasi menggunakan composer :

```
composer require rivaisali/sso-client
```

## Cara Menggunakan

### Menggunakan Authorization Code

#### Untuk Login

```php
require(__DIR__ . "/vendor/autoload.php");

use Rivaisali\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => 'Realm',
    'clientId'                  => 'clientID',
    'clientSecret'              => 'secret',
    'redirectUri'               => 'http://localhost/',
]);

$authUrl = $provider->getAuthorizationUrl();
```

#### Untuk Get User Information

```php
require(__DIR__ . "/vendor/autoload.php");

use Rivaisali\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => 'Realm',
    'clientId'                  => 'clientID',
    'clientSecret'              => 'secret',
    'redirectUri'               => 'http://localhost/',
]);

try {
        $token = $provider->getAccessToken('authorization_code', [
            'code' => $_GET['code']
        ]);
        
         $user = $provider->getResourceOwner($token);
         $user->getName();
         $user->getEmail();
         
    } catch (Exception $e) {
        exit('Failed to get access token: '.$e->getMessage());
    }
```
#### Untuk Logout
```php
require(__DIR__ . "/vendor/autoload.php");

use Rivaisali\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => 'Realm',
    'clientId'                  => 'clientID',
    'clientSecret'              => 'secret',
    'redirectUri'               => 'http://localhost/',
]);

$authUrl = $provider->getLogoutUrl();
