# Integrasi SSO-Kota Gorontalo menggunakan PHP

Library ini menggunakan dan support PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Instalasi

Instalasi menggunakan composer :

```
composer require gorontalokota/sso-client
```

## Cara Menggunakan

### Menggunakan Authorization Code

#### Untuk Login

```php
require(__DIR__ . "/vendor/autoload.php");

use Gorontalokota\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => '{Realms}',
    'clientId'                  => '{clientID}',
    'clientSecret'              => '{clientSecret}',
    'redirectUri'               => '{http://example/oauth/authorized}',
]);

$authUrl = $provider->getAuthorizationUrl();
//Simpan status Auth ke Session untuk mencegah csrf
$_SESSION['oauth2state'] = $provider->getState();
//Redirect Url Auth
header('Location: '.$authUrl);
```

#### Untuk Get User Information

```php
require(__DIR__ . "/vendor/autoload.php");

use Gorontalokota\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => '{Realms}',
    'clientId'                  => '{clientID}',
    'clientSecret'              => '{clientSecret}',
    'redirectUri'               => '{http://example/oauth/authorized}',
]);

 //Periksa status yang diberikan terhadap status yang disimpan sebelumnya untuk mengurangi serangan CSRF
 if (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {
    unset($_SESSION['oauth2state']);
    exit('Invalid state, make sure HTTP sessions are enabled.');

} else {
try {
        $token = $provider->getAccessToken('authorization_code', [
            'code' => $_GET['code']
        ]);

         $user = $provider->getResourceOwner($token);
         $user->getUsername();
         $user->getEmail();
         $user->getName();
         
    } catch (Exception $e) {
        exit('Failed to get access token: '.$e->getMessage());
    }
}

```
#### Refresh Token
```php
require(__DIR__ . "/vendor/autoload.php");

use Gorontalokota\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => '{Realms}',
    'clientId'                  => '{clientID}',
    'clientSecret'              => '{clientSecret}',
    'redirectUri'               => '{http://example/oauth/authorized}',
]);

$token = $provider->getAccessToken('refresh_token', 
            ['refresh_token' => $token->getRefreshToken()]);

```
#### Untuk Logout
```php
require(__DIR__ . "/vendor/autoload.php");

use Gorontalokota\SSO\Client\Provider\Broker;

$provider = new Broker([
    'realm'                     => '{Realms}',
    'clientId'                  => '{clientID}',
    'clientSecret'              => '{clientSecret}',
    'redirectUri'               => '{http://example/oauth/authorized}',
]);

$authUrl = $provider->getLogoutUrl();
