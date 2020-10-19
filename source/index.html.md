---
title: Blockchain Developer API for Bitcoin, Ethereum, Testnet, Litecoin and More | BlockCypher
seo_description: Build blockchain applications easily with our web APIs and callbacks. High throughputs, linear scaling, low-latency. Over 99.99% uptime with no single point of failure.


language_tabs:
  - shell: cURL
  - javascript: JavaScript
  - ruby: Ruby
  - python: Python
  - go: Go
  - php: PHP

toc_footers:
  - <a href='https://accounts.blockcypher.com/'>Sign Up/Login</a>

includes:
  - objects
  - blockchain
  - address
  - wallet
  - tx
  - microtx
  - conf
  - meta
  - analytics
  - asset
  - payfwd
  - events

search: true
---

# Introduction

```shell
#  _               _                  
# |_) |  _   _ |  /     ._  |_   _  ._
# |_) | (_) (_ |< \_ \/ |_) | | (/_ | 
#                    /  |             
```

```javascript
//  _               _                  
// |_) |  _   _ |  /     ._  |_   _  ._
// |_) | (_) (_ |< \_ \/ |_) | | (/_ | 
//                    /  |             
```

```ruby
#  _               _                  
# |_) |  _   _ |  /     ._  |_   _  ._
# |_) | (_) (_ |< \_ \/ |_) | | (/_ | 
#                    /  |             
```

```python
//  _               _                  
// |_) |  _   _ |  /     ._  |_   _  ._
// |_) | (_) (_ |< \_ \/ |_) | | (/_ | 
//                    /  |             
```

```go
//  _               _                  
// |_) |  _   _ |  /     ._  |_   _  ._
// |_) | (_) (_ |< \_ \/ |_) | | (/_ | 
//                    /  |             
```

```php
<?php
/**
 *  _               _
 * |_) |  _   _ |  /     ._  |_   _  ._
 * |_) | (_) (_ |< \_ \/ |_) | | (/_ |
 *                    /  |
 */
```

Welcome to [BlockCypher's](http://www.blockcypher.com/) API documentation! BlockCypher is a simple, mostly RESTful JSON API for interacting with blockchains, accessed over HTTP or HTTPS from the [api.blockcypher.com](https://api.blockcypher.com/v1/btc/main) domain. Currently, BlockCypher supports Bitcoin, Ethereum, Litecoin, Dogecoin, Bitcoin Testnet3, and BlockCypher's Test Chain (more about BlockCypher's Test Chain [below](#testing)).

<aside class="notice">
While <strong>our Ethereum API</strong> is very similar to the API here, it's different enough to necessitate its own documentation; you can find those docs here: <a href="https://www.blockcypher.com/dev/ethereum/">https://www.blockcypher.com/dev/ethereum/</a>.
</aside>

BlockCypher's API provides a superset of the endpoints you'd find in reference implementations, in addition to some special features that make BlockCypher uniquely powerful, like our unconfirmed transaction [Confidence Factor](#confidence-factor), dependable WebHook or WebSockets-based [Events](#events-and-hooks), [On-Chain Microtransactions](#microtransaction-api), and [Payment Forwarding](#payment-forwarding).

Consequently, if you're familiar with a blockchain's reference implementation, you'll feel at home using BlockCypher, but without worrying about scaling or implementation challenges. And if you're not familiar---with the reference implementations or blockchains in general---BlockCypher's API is a great way to dip your toes into blockchain development, without a lengthy setup process. In either case, BlockCypher has 99.99% up-time, and maintains an expressive, logical API that you'll love.

## Documentation Structure

```shell
man curl | grep -A 3 "DESCRIPTION"

DESCRIPTION
curl is a tool to transfer data from or to a server, using one of the supported protocols (DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET and TFTP). The command is designed to work without user interaction.
```

```javascript
// JavaScript examples use JQuery and can be run directly in your browser
// console (ctrl+shift+i or cmd+shift+i).
// Porting them to node.js should be trivial, replacing JQuery methods with
// request.js for example.

console.log('Welcome to BlockCypher');
```

```ruby
# Ruby examples use irb, and our official ruby sdk:
# https://github.com/blockcypher/ruby-client

# You can install it easily from rubygems.org:
# https://rubygems.org/gems/blockcypher-ruby
gem install blockcypher-ruby

# Remember to require the library after installing
> require 'blockcypher'
=> true

# Unless otherwise noted, all requests assume an initialized API 'block_cypher' API object
> block_cypher = BlockCypher::Api.new(api_token:"YOURTOKEN")
=> #<BlockCypher::Api:0x000000010e4060 @api_token="YOURTOKEN", @currency="btc", @network="main", @version="v1">
```

```python
# The official python library (https://github.com/blockcypher/blockcypher-python) works with python2/3
# Install it like this at the command line:
$ pip install blockcypher

# To access any method, first import the blockcypher module:
>>> import blockcypher

# Then call the method:
>>> blockcypher.foo()

# In your codebase, you should probably do it like this though:
>>> from blockcypher import foo
>>> foo()

# By default, all methods return results for BTC, but blockcyphers support many coins.
# You can pass coin_sybmol='foo' as an argument to any method, where foo is one of the following:
>>> blockcypher.constants.COIN_SYMBOL_LIST
[
    "btc",
    "btc-testnet",
    "ltc",
    "doge",
    "bcy",  # blockcypher's testnet
]

# Here's how to determine which version of the blockcypher SDK you're running:
>>> import pkg_resources
>>> pkg_resources.get_distribution("blockcypher")
blockcypher 1.0.39 (/usr/local/lib/python2.7/site-packages)
```

```go
// Go examples use the official SDK, which you can find here:
// https://github.com/blockcypher/gobcy

// To install, use Go Get on the command line
// go get github.com/blockcypher/gobcy

// Then import the package in your Go application
// All examples assume fmt is also imported, and
// will require additional packages as necessary
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  // For Bitcoin main:
  btc := gobcy.API{"YOURTOKEN","btc","main"}
  // For BlockCypher's internal testnet:
  bcy := gobcy.API{"YOURTOKEN","bcy","test"}
  //examples will always follow in main()
}

// You can see additional info in the GoDoc, accessible here:
// https://godoc.org/github.com/blockcypher/gobcy
```

```php
PHP Client
----------

REQUIREMENTS
- PHP 5.4+
- ext-curl
- ext-gmp
- ext-mcrypt
- Composer
- Git

INSTALL SAMPLES
git clone https://github.com/blockcypher/php-client.git
cd php-client

RUNNING SAMPLES ON CONSOLE
php -f .\sample\docs-sample\address-api\address-endpoint.php
Log file is generate in the folder when you run the command

To see detailed PHP client info, check the official PHP repository:
https://github.com/blockcypher/php-client

Less relevant/repetitive code is not shown in samples. You can get the full sample version from:
https://github.com/blockcypher/php-client/tree/master/sample

Unless noted otherwise, all samples assume this initializing code:
<?php
require __DIR__ . '/../bootstrap.php';

use BlockCypher\Auth\SimpleTokenCredential;
use BlockCypher\Rest\ApiContext;
// ... other classes

$apiContext = ApiContext::create(
    'main', 'btc', 'v1',
    new SimpleTokenCredential('$YOUR_TOKEN'),
    array('log.LogEnabled' => true, 'log.FileName' => 'BlockCypher.log', 'log.LogLevel' => 'DEBUG')
);
```

In these docs you'll find everything you need to leverage BlockCypher for your applications. For all officially supported languages, you'll see code samples, in addition to basic cURL requests/responses for every endpoint. You can switch between cURL/language samples via the selector in the upper right. We're working on supporting more languages, but if you're working on your own language library, definitely let us know: we'd love to add more community supported libraries here.

### Section Summaries

- [Objects:](#objects) An overview of all the objects in the API, alongside detailed descriptions of every field.
- [Blockchain API:](#blockchain-api) Endpoints to query general information about a blockchain and its blocks.
- [Address API:](#address-api) Query information about addresses, generate addresses, and generate multisig addresses from public keys.
- [Wallet API:](#wallet-api) Build and modify multiple-address-watching and hierarchical deterministic (HD) wallets, usable throughout the BlockCypher API.
- [Transaction API:](#transaction-api) Information about transactions, how to generate/send your own, and how to embed data into the blockchain.
- [Microtransaction API:](#microtransaction-api) Send on-chain microtransactions while BlockCypher covers your initial mining fees, and low computed fees afterwards.
- [Confidence Factor:](#confidence-factor) Get an accurate measure of the likelihood of a successful double-spend against your unconfirmed transactions.
- [Metadata API:](#metadata-api) Store both public and private key-value pairs against addresses, transactions, and blocks.
- [Analytics API:](#analytics-api) Run asynchronous analytics queries on public blockchains.
- [Asset API:](#asset-api) Create and manage your own assets---embedded on a public blockchain---via the Open Assets standard.
- [Payment Forwarding:](#payment-forwarding) Create one-time addresses that will automatically forward to an address of your choosing, while optionally adding processing fees.
- [Events and Hooks:](#events-and-hooks) Reliable notifications system for a wide variety of events on blockchains, available through WebHooks or WebSockets.

### In-Browser Code Examples

We know many learn more from code examples than specific reference documentation, which is why we have code samples in our official languages. In addition, we have a number of in-browser code examples for particular use cases, which you can see here:

- [Browse the Blockchain](/data/samples/browse-chain.html)
- [Using WebHooks](/data/samples/nodejs-webhook.html)
- [Sending a Simple Transaction](/data/samples/create-tx.html)
- [Create Multisig Transactions](/data/samples/multisig-tx.html)

### Changelog and Errors

Our documentation is powered by [GitHub Pages](https://pages.github.com/) and [Slate](https://github.com/tripit/slate), which makes viewing changes as simple as checking the [git commit history](https://github.com/blockcypher/docs/commits/master). If there's an error or you'd like to suggest a change, please consider submitting a pull request to benefit the broader BlockCypher community.

## API Versions

All API calls are versioned, and the current BlockCypher API is v1. We will never introduce any breaking changes within v1, but we may add new, non-breaking features from time to time.

## BlockCypher Supported Language SDKs

BlockCypher has client SDKs for the following languages:

- **Ruby** [https://github.com/blockcypher/ruby-client](https://github.com/blockcypher/ruby-client)
- **Python** [https://github.com/blockcypher/blockcypher-python](https://github.com/blockcypher/blockcypher-python)
- **Java** [https://github.com/blockcypher/java-client](https://github.com/blockcypher/java-client)
- **PHP** [https://github.com/blockcypher/php-client](https://github.com/blockcypher/php-client)
- **Go** [https://github.com/blockcypher/gobcy](https://github.com/blockcypher/gobcy)
- **Node.js** [https://github.com/blockcypher/node-client](https://github.com/blockcypher/node-client)

If you're using these languages, we strongly encourage you to use an official SDK. Of course, all our API calls are standard HTTP endpoints using JSON formatted responses, so any language (or cURL from the command-line) will work just fine.

### Unofficial Libraries

This client SDK was made by the community, and is not officially supported by BlockCypher. As such, BlockCypher cannot guarantee that it's fully up to date, but we hope it will provide a nice jumping-off point for developers using this language. We'll endeavor to keep this list updated, if any of these prove obsolete. If there's enough support for a particular language, we'll work with the community to turn it into an officially supported SDK.

- **.NET** [https://github.com/bscheiman/BlockCypher](https://github.com/bscheiman/BlockCypher)

## RESTful Resources

```shell
curl https://api.blockcypher.com/v1/btc/main

{
  "name": "BTC.main",
  "height": 360060,
  "hash": "000000000000000000bf56ff4a81e399374a68344a64d6681039412de78366b8",
  "time": "2015-06-08T22:57:08.260165627Z",
  "latest_url": "https://api.blockcypher.com/v1/btc/main/blocks/000000000000000000bf56ff4a81e399374a68344a64d6681039412de78366b8",
  "previous_hash": "000000000000000011c9511ae1265d34d3c16fff6e8f94380425833b3d0ae5d8",
  "previous_url": "https://api.blockcypher.com/v1/btc/main/blocks/000000000000000011c9511ae1265d34d3c16fff6e8f94380425833b3d0ae5d8",
  "peer_count": 239,
  "unconfirmed_count": 617,
  "high_fee_per_kb": 46086,
  "medium_fee_per_kb": 29422,
  "low_fee_per_kb": 12045,
  "last_fork_height": 359865,
  "last_fork_hash": "00000000000000000aa6462fd9faf94712ce1b5a944dc666f491101c996beab9"
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main').then(function(d) {console.log(d)});
> {
>   "name": "BTC.main",
>   "height": 355578,
>   "hash": "00000000000000000a0b253f20709b0c77d8a56aa8db632ecbdc7381816504cd",
>   "time": "2015-05-08T23:12:55.243311146Z",
>   "latest_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000a0b253f20709b0c77d8a56aa8db632ecbdc7381816504cd",
>   "previous_hash": "00000000000000000acef50ef89494493b4a08a8419588e1e3e20cd73bc85a6b",
>   "previous_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000acef50ef89494493b4a08a8419588e1e3e20cd73bc85a6b",
>   "peer_count": 250,
>   "unconfirmed_count": 637,
>   "high_fee_per_kb": 45768,
>   "medium_fee_per_kb": 29415,
>   "low_fee_per_kb": 12045
> }
```

```ruby
# Import Ruby SDK
> require 'blockcypher'
=> true

# Make new Api object, intialize with your token
> block_cypher = BlockCypher::Api.new(api_token:"YOURTOKEN")
=> #<BlockCypher::Api:0x000000010e4060 @api_token="YOURTOKEN", @currency="btc", @network="main", @version="v1">

# Query base resource
> block_cypher.blockchain
=> {"name"=>"BTC.main",
 "height"=>361218,
 "hash"=>"00000000000000000be2dca8e5336b01454fa032635a6f92d6adf0b98cdc6324",
 "time"=>"2015-06-16T19:53:57.157318741Z",
 "latest_url"=>"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000be2dca8e5336b01454fa032635a6f92d6adf0b98cdc6324",
 "previous_hash"=>"00000000000000000c2f3f4b87214d1791289820f9f7b696d7484987a073f567",
 "previous_url"=>"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000c2f3f4b87214d1791289820f9f7b696d7484987a073f567",
 "peer_count"=>260,
 "unconfirmed_count"=>4442,
 "high_fee_per_kb"=>45494,
 "medium_fee_per_kb"=>24444,
 "low_fee_per_kb"=>12301,
 "last_fork_height"=>360362,
 "last_fork_hash"=>"000000000000000002d5cf67bfaa92ba5b371c1590eb48d25031c669ef6233a0"}
```

```python
>>> from blockcypher import get_blockchain_overview
>>> get_blockchain_overview()
{
    "hash": "000000000000000006c488791bafc490efb365ad609ed39e6ee51d1afbc8ed83",
    "height": 379780,
    "high_fee_per_kb": 57607,
    "last_fork_hash": "00000000000000000aef777b65b5d32301c914e7d9f2c4cf1f7366e1eb217124",
    "last_fork_height": 280959,
    "latest_url": "https://api.blockcypher.com/v1/btc/main/blocks/000000000000000006c488791bafc490efb365ad609ed39e6ee51d1afbc8ed83",
    "low_fee_per_kb": 24554,
    "medium_fee_per_kb": 28847,
    "name": "BTC.main",
    "peer_count": 817,
    "previous_hash": "0000000000000000026727709d704d20de7d756427a10a86cd0b084066632e55",
    "previous_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000026727709d704d20de7d756427a10a86cd0b084066632e55",
    "time": "datetime.datetime(2015, 10, 20, 19, 35, 50, 174235, tzinfo=tzutc())",
    "unconfirmed_count": 100231
}
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  chainInfo, err := btc.GetChain()
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", chainInfo)
}

//Result from `go run`:
//{Name:BTC.main Height:648798 Hash:0000000000000000000eb01cbd9eb74c58d70ddb93f34e4e1bf71aeb8a6917fb Time:2020-09-17 20:20:20.214279195 +0000 UTC PrevHash:0000000000000000000a63f225af9678b8cd9dd4fb1dec67b6ca2e329d5efe48 PeerCount:1045 HighFee:84203 MediumFee:61367 LowFee:49335 UnconfirmedCount:17944 LastForkHeight:644543 LastForkHash:0000000000000000000ac8d61492ab76dab7451373c6eaa6803ec0244f623395}
```

```php
<?php
// Run on console:
// php -f .\sample\chain-api\ChainEndpoint.php

$blockchainClient = new BlockchainClient($apiContext);
$blockchain = $blockchainClient->get('BTC.main');

{
  "name":"BTC.main",
  "height":360602,
  "hash":"00000000000000000a1268afd1eb419817106a37c9a87852228cadb752a64f2a",
  "time":"2015-06-12T13:09:56.69966089Z",
  "latest_url":"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000a1268afd1eb419817106a37c9a87852228cadb752a64f2a",
  "previous_hash":"00000000000000000cea706491da61bc755a1250d7260becff5aafc9d26613f2",
  "previous_url":"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000cea706491da61bc755a1250d7260becff5aafc9d26613f2",
  "peer_count":250,
  "unconfirmed_count":1794,
  "high_fee_per_kb":40018,
  "medium_fee_per_kb":26652,
  "low_fee_per_kb":12299,
  "last_fork_height":360362,
  "last_fork_hash":"000000000000000002d5cf67bfaa92ba5b371c1590eb48d25031c669ef6233a0"
}
```

Almost all resources exist under a given blockchain, and follow this pattern:

`https://api.blockcypher.com/$API_VERSION/$COIN/$CHAIN/`

Currently, there's only one version of the API (v1). Thus, here's an exhaustive list of blockchains and their corresponding resources:

Coin | Chain | Resource
---- | ----- | --------
Bitcoin | Main | `api.blockcypher.com/v1/btc/main`
Bitcoin | Testnet3 | `api.blockcypher.com/v1/btc/test3`
Dogecoin | Main | `api.blockcypher.com/v1/doge/main`
Litecoin | Main | `api.blockcypher.com/v1/ltc/main`
BlockCypher | Test | `api.blockcypher.com/v1/bcy/test`

<aside class="notice">
Unless otherwise noted, all descriptions of direct HTTP requests will assume one of these base resources prepends it. But you can always see the full call in the cURL code sample.
</aside>

<aside class="success">
Our API <b>always</b> returns values in satoshis, or the lowest non-divisible unit in non-Bitcoin blockchains. As a friendly reminder, there are 10^8 satoshis in a single bitcoin (100,000,000s = 1BTC), 10^8 base units per litecoin, and 10^8 koinus per dogecoin (100,000,000k = 1DOGE).
</aside>

## Rate Limits and Tokens

```shell
# Adding your token as URL parameter
curl https://api.blockcypher.com/v1/btc/main?token=$YOURTOKEN

# Checking your token's limits
curl https://api.blockcypher.com/v1/tokens/$YOURTOKEN
{
"token": "YOURTOKEN",
"limits": {
  "api/hour": 10000,
  "api/second": 500,
  "hooks/hour": 5000,
  "confidence/hour": 1000,
  "hooks": 5000,
  "payments": 5000
},
"hits": {
  "api/hour": 280,
  "hooks/hour": 240,
  "confidence/hour": 100
},
"hits_history": {
  {
  "api/hour": 253,
  "confidence/hour": 50,
  "time": "2016-06-15T07:00:00-00:00",
  "hooks": 358,
  },
  {
  "api/hour": 2,
  "hooks/hour": 30,
  "time": "2016-06-15T06:00:00-00:00"
  "hooks": 358,
  },
  ...
}
}
# These are quite above the default limits, but if you'd like them, reach out at contact@blockcypher.com ;)
```

```javascript
// Adding your token as URL parameter
const TOKEN = 'YOUR_TOKEN';
$.get('https://api.blockcypher.com/v1/btc/main?token='+TOKEN);
```

```ruby
# Import Ruby SDK
> require 'blockcypher'
=> true

# Make new Api object, intialize with your token
> block_cypher = BlockCypher::Api.new(api_token:"YOURTOKEN")
=> #<BlockCypher::Api:0x000000010e4060 @api_token="YOURTOKEN", @currency="btc", @network="main", @version="v1">
```

```python
>>> from blockcypher import get_token_info
>>> get_token_info('YOUR_TOKEN')
{
    "hits": {
        "api/hour": 16366,
        "hooks/hour": 266,
        "confidence/hour": 101
    },
    "limits": {
        "api/hour": 500000,
        "api/second": 200,
        "hooks/hour": 30000,
        "confidence/hour": 1000,
        "hooks": 10000,
        "payments": 10000
    },
    "token": "YOUR_TOKEN"
}
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  //Adding your token is part of the API coin/chain initialization
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  fmt.Printf("%+v\n", btc)
}

//Result from `go run`:
//{Token:YOURTOKEN Coin:btc Chain:main}
```

```php
<?php
# Adding your token as client credential to API context
$apiContext = ApiContext::create(
    'main', 'btc', 'v1',
    new SimpleTokenCredential('c0afcccdde5081d6429de37d16166ead'),
    array('log.LogEnabled' => true, 'log.FileName' => 'BlockCypher.log', 'log.LogLevel' => 'DEBUG')
);

# Use $apiContext as function param or set as default ApiContext
```

We want everyone to try BlockCypher with as little friction as possible, which is why you don't need a token for any read-only **GET** calls. Please [register for a user token](http://accounts.blockcypher.com/) if you want to use **POST** and **DELETE** calls. Once you have your token, you can append it to all your requests like any other URL parameter if you're using cURL, or through the appropriate method in the language SDK you're using.

We do rate-limit our free tier, with or without a token (though tokens are required for Confidence lookups, WebHooks/Sockets, Payments, and any **POST** or **DELETE** calls):

- Classic requests, up to 3 requests/sec and 200 requests/hr
- WebHooks and WebSockets, up to 200 requests sent to your servers/hr
- WebHooks, up to 200 stored on our servers
- Confidence lookups, up to 15 requests/hour

The hourly rate limits reset on the top of the hour UTC. For example, if you're under the free tier, and you have used 200 regular requests by 03:58 UTC, you'll hit a rate limit until it resets at 04:00 UTC.

<aside class="warning">
If you exceed these limits, your requests will return an HTTP Status Code 429!
</aside>

On [the accounts page](https://accounts.blockcypher.com/), you'll find paid plans starting at $75 a month (with a 10% discount if you pay with Bitcoin). To request higher limits or SLAs beyond what's offered on the accounts page, please [email us.](mailto:contact@blockcypher.com)

You can check your current limits and usage via a **GET** on the following endpoint, outside of our normal coin/chain pattern:

`https://api.blockcypher.com/v1/tokens/$YOURTOKEN`

Within that return object, you'll also find **hits_history** array, which shows your token's last 48 hours of usage, while **hits** shows the current hour's usage.

You can even see information about your remaining limits by checking the **X-Ratelimit-Remaining** attribute in the HTTP header in normal API calls. Keep in mind the **X-Ratelimit-Remaining** attribute corresponds to the hourly rate limit associated with the endpoint you call (e.g., if it's from a WebHook, that corresponds to the Hooks/Hour; if it's a normal call, the number corresponds to the normal Requests/Hour limit).

## Batching

```shell
# Batching blocks 5, 6, and 7
curl 'https://api.blockcypher.com/v1/btc/main/blocks/5;6;7'

[{
"hash": "000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
"height": 6,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:29:49Z",
...,
},
{
"hash": "000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
"height": 5,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:23:48Z",
...,
},
{
"hash": "0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444",
"height": 7,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:39:29Z",
...,
}]
```

```javascript
// Batching blocks 5, 6, and 7

$.get('https://api.blockcypher.com/v1/btc/main/blocks/5;6;7')
  .then(function(d) {console.log(d);});
> [{
> "hash": "000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
> "height": 6,
> "chain": "BTC.main",
> "total": 0,
> "fees": 0,
> "ver": 1,
> "time": "2009-01-09T03:29:49Z",
> ...,
> },
> {
> "hash": "000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
> "height": 5,
> "chain": "BTC.main",
> "total": 0,
> "fees": 0,
> "ver": 1,
> "time": "2009-01-09T03:23:48Z",
> ...,
> },
> {
> "hash": "0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444",
> "height": 7,
> "chain": "BTC.main",
> "total": 0,
> "fees": 0,
> "ver": 1,
> "time": "2009-01-09T03:39:29Z",
> ...,
> }]
```

```ruby
# Batching blocks 5, 6, and 7
> block_cypher.blockchain_block('5;6;7')
=> [{"hash"=>"0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444",
  "height"=>7,
  "chain"=>"BTC.main",
  "total"=>0,
  "fees"=>0,
  "ver"=>1,
  "time"=>"2009-01-09T03:39:29Z",
  ...},
 {"hash"=>"000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
  "height"=>6,
  "chain"=>"BTC.main",
  "total"=>0,
  "fees"=>0,
  "ver"=>1,
  "time"=>"2009-01-09T03:29:49Z",
  ...},
 {"hash"=>"000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
  "height"=>5,
  "chain"=>"BTC.main",
  "total"=>0,
  "fees"=>0,
  "ver"=>1,
  "time"=>"2009-01-09T03:23:48Z",
  ...}]
```

```python
# Where possible, the python library will batch requests for speed
>>> from blockcypher import get_blocks_overview
# Batching blocks 5, 6, and 7
>>> get_blocks_overview([5,6,7])
[
    {
        "bits": 486604799,
        "chain": "BTC.main",
        "depth": 379775,
        "fees": 0,
        "hash": "000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
        "height": 6,
        "mrkl_root": "20251a76e64e920e58291a30d4b212939aae976baca40e70818ceaa596fb9d37",
        "n_tx": 1,
        "nonce": 2538380312,
        "prev_block": "000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
        "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
        "received_time": "datetime.datetime(2009, 1, 9, 3, 29, 49, 0, tzinfo=tzutc())",
        "relayed_by": "",
        "time": "datetime.datetime(2009, 1, 9, 3, 29, 49, 0, tzinfo=tzutc())",
        "total": 0,
        "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
        "txids": [
            "20251a76e64e920e58291a30d4b212939aae976baca40e70818ceaa596fb9d37"
        ],
        "ver": 1
    },
    {
        "bits": 486604799,
        "chain": "BTC.main",
        "depth": 379774,
        "fees": 0,
        "hash": "0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444",
        "height": 7,
        "mrkl_root": "8aa673bc752f2851fd645d6a0a92917e967083007d9c1684f9423b100540673f",
        "n_tx": 1,
        "nonce": 2258412857,
        "prev_block": "000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
        "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
        "received_time": "datetime.datetime(2009, 1, 9, 3, 39, 29, 0, tzinfo=tzutc())",
        "relayed_by": "",
        "time": "datetime.datetime(2009, 1, 9, 3, 39, 29, 0, tzinfo=tzutc())",
        "total": 0,
        "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
        "txids": [
            "8aa673bc752f2851fd645d6a0a92917e967083007d9c1684f9423b100540673f"
        ],
        "ver": 1
    },
    {
        "bits": 486604799,
        "chain": "BTC.main",
        "depth": 379776,
        "fees": 0,
        "hash": "000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
        "height": 5,
        "mrkl_root": "63522845d294ee9b0188ae5cac91bf389a0c3723f084ca1025e7d9cdfe481ce1",
        "n_tx": 1,
        "nonce": 2011431709,
        "prev_block": "000000004ebadb55ee9096c9a2f8880e09da59c0d68b1c228da88e48844a1485",
        "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/000000004ebadb55ee9096c9a2f8880e09da59c0d68b1c228da88e48844a1485",
        "received_time": "datetime.datetime(2009, 1, 9, 3, 23, 48, 0, tzinfo=tzutc())",
        "relayed_by": "",
        "time": "datetime.datetime(2009, 1, 9, 3, 23, 48, 0, tzinfo=tzutc())",
        "total": 0,
        "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
        "txids": [
            "63522845d294ee9b0188ae5cac91bf389a0c3723f084ca1025e7d9cdfe481ce1"
        ],
        "ver": 1
    }
]
```

```go
//Batching requests is currently unsupported in the Go SDK
//But you can still request things serially
package main

import (
	"fmt"

	"github.com/blockcypher/gobcy"
)

func main() {
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  blocks := make([]gobcy.Block, 3)
  heights := []int{5, 6, 7}
  for i, v := range heights {
    blk, err := btc.GetBlock(v, "", nil)
    if err != nil {
      fmt.Println(err)
    }
    blocks[i] = blk
  }
  fmt.Printf("%+v\n", blocks)
}

//Result from `go run`:
//[{Hash:000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc Height:5 Depth:648793 Chain:BTC.main Total:{neg:false abs:[]} Fees:{neg:false abs:[]} Ver:1 Time:2009-01-09 03:23:48 +0000 UTC ReceivedTime:2009-01-09 03:23:48 +0000 UTC RelayedBy:<nil> Bits:486604799 Nonce:2011431709 NumTX:1 PrevBlock:000000004ebadb55ee9096c9a2f8880e09da59c0d68b1c228da88e48844a1485 MerkleRoot:63522845d294ee9b0188ae5cac91bf389a0c3723f084ca1025e7d9cdfe481ce1 TXids:[63522845d294ee9b0188ae5cac91bf389a0c3723f084ca1025e7d9cdfe481ce1] NextTXs:} {Hash:000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d Height:6 Depth:648792 Chain:BTC.main Total:{neg:false abs:[]} Fees:{neg:false abs:[]} Ver:1 Time:2009-01-09 03:29:49 +0000 UTC ReceivedTime:2009-01-09 03:29:49 +0000 UTC RelayedBy:<nil> Bits:486604799 Nonce:2538380312 NumTX:1 PrevBlock:000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc MerkleRoot:20251a76e64e920e58291a30d4b212939aae976baca40e70818ceaa596fb9d37 TXids:[20251a76e64e920e58291a30d4b212939aae976baca40e70818ceaa596fb9d37] NextTXs:} {Hash:0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444 Height:7 Depth:648791 Chain:BTC.main Total:{neg:false abs:[]} Fees:{neg:false abs:[]} Ver:1 Time:2009-01-09 03:39:29 +0000 UTC ReceivedTime:2009-01-09 03:39:29 +0000 UTC RelayedBy:<nil> Bits:486604799 Nonce:2258412857 NumTX:1 PrevBlock:000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d MerkleRoot:8aa673bc752f2851fd645d6a0a92917e967083007d9c1684f9423b100540673f TXids:[8aa673bc752f2851fd645d6a0a92917e967083007d9c1684f9423b100540673f] NextTXs:}]
```

```php
<?php
// Run on console:
// php -f .\sample\introduction\Batching.php
// Batching blocks 5, 6, and 7

$blockClient = new BlockClient($apiContext);
$blockList = array('5', '6', '7');
$blocks = $blockClient->getMultiple($blockList);

[{
"hash": "000000003031a0e73735690c5a1ff2a4be82553b2a12b776fbd3a215dc8f778d",
"height": 6,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:29:49Z",
...,
},
{
"hash": "000000009b7262315dbf071787ad3656097b892abffd1f95a1a022f896f533fc",
"height": 5,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:23:48Z",
...,
},
{
"hash": "0000000071966c2b1d065fd446b1e485b2c9d9594acd2007ccbd5441cfc89444",
"height": 7,
"chain": "BTC.main",
"total": 0,
"fees": 0,
"ver": 1,
"time": "2009-01-09T03:39:29Z",
...,
}]

# Note, when constructing the block array (first param) programatically you can use alternative syntax like:
$blockList = array('5', '6', '7');
$blockList = explode(";", "5;6;7");
$blockList = [5,6,7]; // PHP 5.4
```

All endpoints that can retrieve a single [Object](#objects) can be batched to return multiple objects. If you're cURLing the API directly, batching simply requires appending each identifier to the previous one using a semicolon (check the code pane for an example). The results are aggregated in a JSON array. The other supported client SDKs batch differently, but each idiomatic to their respective language (check the code pane examples in each library).

<aside class="notice">
The maximum number of elements that can be batched in a single call is 100.
</aside>

<aside class="notice">
As you may have noticed from the examples, batched calls don't necessarily respect ordering, especially for bigger batches. But this shouldn't matter, as the requested identifiers are always present in the returned objects.
</aside>

When cURLing BlockCypher, batching also works when the identifiers aren't the last part of the URL; e.g., this URL will return the balances of three separate addresses:

`https://api.blockcypher.com/v1/btc/main/addrs/1J38WorKngZLJvA7qMin9g5jqUfTQUBZNE;1JP8FqoXtCMrR1sZc2McLWmHxENox1Y1PV;1ENn7XmqXNnReiQEFHhBGzfiv5gAyBj7r1/balance`

<aside class="warning">
With regard to rate limits, each individual batch call counts as a request; for example, if you request 3 addresses in a batch, you're still using 3 API calls of resources on our end. The big advantage to batching is that you avoid 3 separate round-trip calls/reduce latency. Since the default, non-registered <a href="#rate-limits-and-tokens">rate limit</a> per second is 3, larger batches require a paid API token. To use larger batches <a href="https://accounts.blockcypher.com/">please register.</a>
</aside>

## Testing

We offer two different options for testing your blockchain application: Bitcoin Testnet3, and BlockCypher's Test Chain. We offer automated faucets for both Testnet3 and BlockCypher's Test Chain, but we recommend using BlockCypher's Test Chain for a variety of reasons:

- It's nearly identical in characteristics to Bitcoin Main, with a few differences listed below.
- The prefix for standard addreses is 'B' or 'C' (0x1B). The prefix for multisig addresses is 'D' (0x1F). This is also known as the "address version byte," which you can [read more about here.](https://bitcoin.org/en/developer-reference#address-conversion)
- The chain is private (no data is broadcasted, only BlockCypher mines the transactions), making it much more predictable than the Bitcoin's testnet (which is frequently under attack).
- New blocks get built every minute, confirming the transactions that have been created using our transaction API.

In case you missed the [Resources section](#restful-resources), the BlockCypher Test Chain is accessible from this resource:

`https://api.blockcypher.com/v1/bcy/test`

<aside class="warning">
As mentioned above, Bitcoin Testnet3 is frequently under attack, sometimes from core developers stress-testing the protocol, sometimes from malevolent actors attempting new attack vectors. It can be extremely---and inconsistently---unreliable. Unless your testing environment requires Testnet3, we strongly encourage you to use BlockCypher's Test Chain.
</aside>

```shell
# Note resource change to bcy/test instead of btc/main
# Make new address; returns private key/public key/address
curl -X POST https://api.blockcypher.com/v1/bcy/test/addrs?token=$YOURTOKEN

{
"private": "26415016a2fb49f51aef161cb35bd537be07b75a6ac1e297d3b7a370cc85433b",
"public": "02c572d062fefcc8c3e1bf5016450addcedb89cd7e4507d8a323f327b4ad1018e0",
"address": "CFqoZmZ3ePwK5wnkhxJjJAQKJ82C7RJdmd"
}

# Fund prior address with faucet
curl -d '{"address": "CFqoZmZ3ePwK5wnkhxJjJAQKJ82C7RJdmd", "amount": 100000}' https://api.blockcypher.com/v1/bcy/test/faucet?token=$YOURTOKEN
{
"tx_ref": "02dbf5585d438a1cba82a9041dd815635a6b0df684225cb5271e11397a759479"
}
```

```javascript
// Make new address; returns private key/public key/address
$.post('https://api.blockcypher.com/v1/bcy/test/addrs?token=$YOUR_TOKEN')
  .then(function(d) {console.log(d)});
> {
> "private": "26415016a2fb49f51aef161cb35bd537be07b75a6ac1e297d3b7a370cc85433b",
> "public": "02c572d062fefcc8c3e1bf5016450addcedb89cd7e4507d8a323f327b4ad1018e0",
> "address": "CFqoZmZ3ePwK5wnkhxJjJAQKJ82C7RJdmd"
> }

// Fund prior address with faucet
var data = {"address": "CFqoZmZ3ePwK5wnkhxJjJAQKJ82C7RJdmd", "amount": 100000}
$.post('https://api.blockcypher.com/v1/bcy/test/faucet?token=$YOUR_TOKEN', JSON.stringify(data))
  .then(function(d) {console.log(d)});
> {
>   "tx_ref": "02dbf5585d438a1cba82a9041dd815635a6b0df684225cb5271e11397a759479"
> }
```

```ruby
# Create new api object on BlockCypher Testnet
> bc_test = BlockCypher::Api.new(currency:BlockCypher::BCY, network:BlockCypher::TEST_NET, api_token:"YOURTOKEN")
=> #<BlockCypher::Api:0x000000030169b0 @api_token="YOURTOKEN", @currency="bcy", @network="test", @version="v1">

# Generate new test address
> bc_test.address_generate
=> {"private"=>"5a3675bbc7e24e63224bff60a3850a77d5a494419735e3b94173eac5379437c9",
 "public"=>"0311ab9780760dd8c5d2d9319f1eee9c9328eddd503d0b86e3af253b1665638b8a",
 "address"=>"Bv6rJzFmSg41nhibjfEfqSeeb2Te1Hh3FG",
 "wif"=>"BrMPimGAWyUUuGduxHZi1Q9n9Nveunton8sC5ouZeJp6EhKNaAcZ"}

# Fund with faucet
> bc_test.faucet("Bv6rJzFmSg41nhibjfEfqSeeb2Te1Hh3FG", 1000000)
=> {"tx_ref"=>"59ddf03d9e292da5b3160e6ac341c9773873b693203c799a4ba8a4e05ef5a0d7"}
```

```python
# Fund existing address with faucet
>>> from blockcypher import send_faucet_coins
# bcy is the coin_symbol for the blockcypher (not bitcoin) testnet
>>> send_faucet_coins(address_to_fund='CFqoZmZ3ePwK5wnkhxJjJAQKJ82C7RJdmd', satoshis=10000, api_key='YOUR_TOKEN', coin_symbol='bcy')
{
    "tx_ref": "5d59f2ff777594fdfd964cd5ee4853e80cc3dc097a2ce67a55c16e643a99dddb"
}
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
  //Generate new address
  pair, err := bcy.GenAddrKeychain()
  //Fund it with faucet
  txhash, err := bcy.Faucet(pair, 100000)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("Address: %v, Faucet TXHash: %v\n", pair.Address, txhash)
}

//Result from `go run`:
//Address: CF2ge9wF1KERoSrk9eRR8s2EJSLHiRMLPE, Faucet TXHash: aad5399bb760307e3d8b9c4d1d1480d0baddccc7f0ce2aca8df6e54ad4b260b3
```

```php
<?php
// Make new address; returns private key/public key/address
// Run on console:
// php -f .\sample\introduction\GenerateBcyAddress.php

$addressClient = new AddressClient($apiContext);
$addressKeyChain = $addressClient->generateAddress();

{
  "private":"40e0d6e6210307b9b9d0113bceb9b8c5be6b9e010a03e07f25f75f462ed00a90",
  "public":"03bd50fc57b1cdf3badbdf0662a4bb54b4d7a0b96eb27cc216b01503b8bf29b29e",
  "address":"Bxi1GmU6xgqgyBEzugcqFZRLyJd1cpEv2S",
  "wif":"BqW9QJ24VFcEvDp8UvYvLTnDxn1SjpxsvTeysPAjBW6BX9NggYfr"
}

<?php
// Fund prior address with faucet
// Run on console:
// php -f .\sample\introduction\FundAddressWithFaucetEndpoint.php

$faucetClient = new FaucetClient($apiContext);
$faucetResponse = $faucetClient->fundAddress('Bxi1GmU6xgqgyBEzugcqFZRLyJd1cpEv2S', 100000);

{
  "tx_ref":"0fa68cf5c39dbd918a5ca49fc092be36b8bcece83cbfed919ad6c77b5f24cceb"
}
```

### Test Faucets

To help facilitate automated testing in your applications, a faucet endpoint is available on both BlockCypher's Test Chain and Bitcoin Testnet3. Calling the faucet endpoint, along with passing a valid address, will automatically create---and propagate---a new transaction funding the address with the amount you provide.

This example shows how to leverage the faucet to programmatically fund addresses, to test your applications. While the example used BlockCypher's Test Chain, the same example could have used Bitcoin Testnet3 and worked the exact same way.

<aside class="notice">
You need <a href="https://accounts.blockcypher.com/">a token</a> to use test faucets.
</aside>

<aside class="warning">
On the BlockCypher Test Chain, the faucet will refuse to fund an address with more than 500 billion BlockCypher satoshis and will not fund more than 100 million BlockCypher satoshis at a time. On Bitcoin Testnet3 those numbers are adjusted to 10 million and 500,000 testnet satoshis respectively.
</aside>
