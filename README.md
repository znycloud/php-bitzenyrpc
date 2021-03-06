# Simple BitZeny JSON-RPC client based on GuzzleHttp


## Installation
Run ```php composer.phar require znycloud/php-bitzenyrpc``` in your project directory or add following lines to composer.json
```javascript
"require": {
    "znycloud/php-bitzenyrpc": "^2.0"
}
```
and run ```php composer.phar update```.

## Requirements
PHP 7.0 or higher (should also work on 5.6, but this is unsupported)

## Usage
Create new object with url as parameter
```php
use znycloud\BitZeny\Client as BitZenyClient;

$bitzenyd = new BitZenyClient('http://rpcuser:rpcpassword@localhost:9253/');
```
or use array to define your bitzenyd settings
```php
use znycloud\BitZeny\Client as BitZenyClient;

$bitzenyd = new BitZenyClient([
    'scheme' => 'http',                 // optional, default http
    'host'   => 'localhost',            // optional, default localhost
    'port'   => 9253,                   // optional, default 9253
    'user'   => 'rpcuser',              // required
    'pass'   => 'rpcpassword',          // required
    'ca'     => '/etc/ssl/ca-cert.pem'  // optional, for use with https scheme
]);
```
Then call methods defined in [BitZeny Core API Documentation](https://BitZeny.org/en/developer-reference#BitZeny-core-apis) with magic:
```php
/**
 * Get block info.
 */
$block = $bitzenyd->getBlock('000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f');

$block('hash')->get();     // 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
$block['height'];          // 0 (array access)
$block->get('tx.0');       // 4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b
$block->count('tx');       // 1
$block->has('version');    // key must exist and CAN NOT be null
$block->exists('version'); // key must exist and CAN be null
$block->contains(0);       // check if response contains value
$block->values();          // array of values
$block->keys();            // array of keys
$block->random(1, 'tx');   // random block txid
$block('tx')->random(2);   // two random block txid's
$block('tx')->first();     // txid of first transaction
$block('tx')->last();      // txid of last transaction

/**
 * Send transaction.
 */
$result = $bitzenyd->sendToAddress('mmXgiR6KAhZCyQ8ndr2BCfEq1wNG2UnyG6', 0.1);
$txid = $result->get();

/**
 * Get transaction amount.
 */
$result = $bitzenyd->listSinceBlock();
$totalAmount = $result->sum('transactions.*.amount');
$totalSatoshi = BitZenyClient::toSatoshi($totalAmount);
```
To send asynchronous request, add Async to method name:
```php
use znyCloud\BitZeny\BitZenydResponse;

$promise = $bitzenyd->getBlockAsync(
    '000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f',
    function (BitZenydResponse $success) {
        //
    },
    function (\Exception $exception) {
        //
    }
);

$promise->wait();
```

You can also send requests using request method:
```php
/**
 * Get block info.
 */
$block = $bitzenyd->request('getBlock', '000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f');

$block('hash');            // 000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f
$block['height'];          // 0 (array access)
$block->get('tx.0');       // 4a5e1e4baab89f3a32518a88c31bc87f618f76673e2cc77ab2127b7afdeda33b
$block->count('tx');       // 1
$block->has('version');    // key must exist and CAN NOT be null
$block->exists('version'); // key must exist and CAN be null
$block->contains(0);       // check if response contains value
$block->values();          // get response values
$block->keys();            // get response keys
$block->random(1, 'tx');   // get random txid

/**
 * Send transaction.
 */
$result = $ZNY->request('sendtoaddress', ['mmXgiR6KAhZCyQ8ndr2BCfEq1wNG2UnyG6', 0.06]);
$txid = $result->get();

```
or requestAsync method for asynchronous calls:
```php
use znycloud\BitZeny\BitZenydResponse;

$promise = $bitzenyd->requestAsync(
    'getBlock',
    '000000000019d6689c085ae165831e934ff763ae46a2a6c172b3f1b60a8ce26f',
    function (BitZenydResponse $success) {
        //
    },
    function (\Exception $exception) {
        //
    }
);

$promise->wait();
```

## License

This product is distributed under MIT license.

## Donations

If you like this project,
you can donate BitZeny to ZdYq9qjbj6D9e6d4UfjpAr1AxpFewAtNcU.

Thanks for your support!❤
