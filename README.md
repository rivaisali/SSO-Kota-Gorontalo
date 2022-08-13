# Integrasi SSO-Kota Gorontalo menggunakan PHP

This package support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require rivaisali/oauth2-keycloak
```

## Usage

Usage is the same as The League's OAuth client, using `\Rivaisali\SSO\Client\Provider\Keycloak` as the provider.

Use `authServerUrl` to specify the Keycloak server URL. You can lookup the correct value from the Keycloak client installer JSON under `auth-server-url`, eg. `http://localhost:8080/auth`.

Use `realm` to specify the Keycloak realm name. You can lookup the correct value from the Keycloak client installer JSON under `resource`, eg. `master`.

### Authorization Code Flow

```php
$provider = new Rivaisali\SSO\Client\Provider\Keycloak([
    'authServerUrl'         => '{keycloak-server-url}',
    'realm'                 => '{keycloak-realm}',
    'clientId'              => '{keycloak-client-id}',
    'clientSecret'          => '{keycloak-client-secret}',
    'redirectUri'           => 'https://example.com/callback-url',
    'encryptionAlgorithm'   => 'RS256',                             // optional
    'encryptionKeyPath'     => '../key.pem'                         // optional
    'encryptionKey'         => 'contents_of_key_or_certificate'     // optional
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state, make sure HTTP sessions are enabled.');

} else {

    // Try to get an access token (using the authorization coe grant)
    try {
        $token = $provider->getAccessToken('authorization_code', [
            'code' => $_GET['code']
        ]);
    } catch (Exception $e) {
        exit('Failed to get access token: '.$e->getMessage());
    }

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getName());

    } catch (Exception $e) {
        exit('Failed to get resource owner: '.$e->getMessage());
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

### Refreshing a Token

```php
$provider = new Stevenmaguire\OAuth2\Client\Provider\Keycloak([
    'authServerUrl'     => '{keycloak-server-url}',
    'realm'             => '{keycloak-realm}',
    'clientId'          => '{keycloak-client-id}',
    'clientSecret'      => '{keycloak-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url',
]);

$token = $provider->getAccessToken('refresh_token', ['refresh_token' => $token->getRefreshToken()]);
```

### Handling encryption

If you've configured your Keycloak instance to use encryption, there are some advanced options available to you.

#### Configure the provider to use the same encryption algorithm

```php
$provider = new Stevenmaguire\OAuth2\Client\Provider\Keycloak([
    // ...
    'encryptionAlgorithm'   => 'RS256',
]);
```

or

```php
$provider->setEncryptionAlgorithm('RS256');
```

#### Configure the provider to use the expected decryption public key or certificate

##### By key value

```php
$key = "-----BEGIN PUBLIC KEY-----\n....\n-----END PUBLIC KEY-----";
// or
// $key = "-----BEGIN CERTIFICATE-----\n....\n-----END CERTIFICATE-----";

$provider = new Stevenmaguire\OAuth2\Client\Provider\Keycloak([
    // ...
    'encryptionKey'   => $key,
]);
```

or

```php
$provider->setEncryptionKey($key);
```

##### By key path

```php
$keyPath = '../key.pem';

$provider = new Stevenmaguire\OAuth2\Client\Provider\Keycloak([
    // ...
    'encryptionKeyPath'   => $keyPath,
]);
```


