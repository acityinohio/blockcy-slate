# Events and Hooks

Blockchains are highly transactional systems. Many usage patterns require knowing when an event occurs: i.e., when a transaction is included into a block, or when an unconfirmed transaction is relayed through the network. Instead of requiring you to continuously poll resources, we provide push APIs to facilitate those use cases, and support both WebSockets and WebHooks.

### Which Should I Use?

**[WebSockets](https://developer.mozilla.org/en-US/docs/WebSockets)** are typically used in client applications when a server is not already running: e.g., a web page displaying the most recent transactions or a wallet app updating its UI when a transaction has been confirmed. Websockets are less reliable in longer interactions (> 1 hour) because they require a connection to stay open.

**[WebHooks](https://webhooks.pbworks.com/w/page/13385124/FrontPage)** are the most reliable way to get event notifications but requires running a server to receive the callbacks. We automatically retry HTTP requests 5 times.

## Types of Events

We support a number of different event types, and you can filter your notification requests depending on how you structure your [Event](#event) request object. 

Event | Description
----- | -----------
*unconfirmed-tx* | Triggered for every new transaction BlockCypher receives before it's confirmed in a block; basically, for every unconfirmed transaction. The payload is an unconfirmed [TX](#tx).
*new-block* | Triggered for every new block added to the blockchain you've selected as your base resource. The payload is a [Block](#block).
*confirmed-tx* | Triggered for every new transaction making it into a new block; in other words, for every first transaction confirmation. This is equivalent to listening to the *new-block* event and fetching each transaction in the new [Block](#block). The payload is a confirmed [TX](#tx).
*tx-confirmation* | Simplifies listening to confirmations on all transactions for a given address up to a provided threshold. Sends first the unconfirmed transaction and then the transaction for each confirmation. Use the **confirmations** property within the [Event](#event) to manually specify the number of confirmations desired (maximum 10, defaults to 6). The payload is a [TX](#tx).
*double-spend-tx* | Triggered any time a double spend is detected by BlockCypher. The payload is the [TX](#tx) that triggered the event; the hash of the transaction that it's trying to double spend is included in its **double_spend_tx** property.
*tx-confidence* | Triggered any time an **address** has an unconfirmed transaction above the **confidence** property specified in the [Event](#event), based on our [Confidence Factor.](#confidence-factor) The payload is the [TX](#tx) that triggered the event. If **confidence** is not set, defaults to 0.99. To ensure transactions are not missed, even if your confidence threshold is not reached, a transaction is still sent after a minute timeout; please remember to double-check the **confidence** attribute in the [TX](#tx) payload.

<aside class="notice">
Events like <i>unconfirmed-tx</i> can produce a lot of requests. To avoid rate-limiting, please <a href="http://accounts.blockcypher.com/">register for a token.</a>
</aside>

## Using WebSockets

```shell
# no websockets via cURL, check the Javascript example
```

```javascript
// Get latest unconfirmed transactions live
var ws = new WebSocket("wss://socket.blockcypher.com/v1/btc/main?token=$TOKEN");
var count = 0;
ws.onmessage = function (event) {
  var tx = JSON.parse(event.data);
  var shortHash = tx.hash.substring(0, 6) + "...";
  var total = tx.total / 100000000;
  var addrs = tx.addresses.join(", ");
  $('#browser-websocket').before("<div>Unconfirmed transaction " + shortHash + " totalling " + total + "BTC involving addresses " + addrs + "</div>");
  count++;
  if (count > 10) ws.close();
}
ws.onopen = function(event) {
  ws.send(JSON.stringify({event: "unconfirmed-tx"}));
}
```

```php
# no websockets via PHP, check the Javascript example
```

Opening a WebSocket to listen to our feeds is easy, like so in Javascript:

`new WebSocket("wss://socket.blockcypher.com/v1/btc/main?token=$TOKEN");`

The code may differ if you're not programming in Javascript (check relevant code examples for our standard libraries) but the URL will be identical. Once the socket is opened, the JSON document representing the [Event](#event) of interest should be sent.

In addition to standard events, WebSockets accept a "ping" event. If you send the following, you will receive the same message back with "ping" replaced by "pong":

`{ "event": "ping" }`

A regular ping (i.e. every 20 seconds) allows the WebSocket to stay connected for a longer period of time.

## Using WebHooks

WebHooks leverage similar objects and interactions but with two key differences:

- The JSON [Event](#event) should be sent using a POST request to the "create webhook endpoint" and include a **url** property to denote where payloads should be delivered.
- The [TX](#tx) or [Block](#block) associated with the [Event](#event) will be POSTed to the provided **url**. The POSTed payload will also include *X-EventType* and *X-EventId* metadata in the HTTP header specifying the **event** type and **id** of the WebHook which triggered the payload.

We retry individual payloads to your **url** five times; if one fails, we wait exponentially between retries: 1 second, 2s, 4s, 8s, 16s. In order to protect against stale callback **url**s, your [Event](#event) will be deleted if it reaches 50 aggregate **callback_errors** from failed payloads.

<aside class="warning">
To prevent eavesdropping, we recommend securing your callback <b>url</b> by using SSL and providing a <i>secret</i> parameter appended to the <a href="#event">Event</a> request. We POST the payload to the unaltered <b>url</b>, which allows you to check on your server that the parameter was not modified. For more advanced setups see <a href="webhook-signing"></a>
</aside>

<aside class="notice">
Testing WebHooks can be tricky; we recommend using <a href="http://requestb.in/">requestb.in</a> to set up a temporary server to test your events.
</aside>

### Create WebHook Endpoint

```shell
curl -d '{"event": "unconfirmed-tx", "address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh", "url": "https://my.domain.com/callbacks/new-tx"}' https://api.blockcypher.com/v1/btc/main/hooks?token=YOURTOKEN

{
"id": "399d0923-e920-48ee-8928-2051cbfbc369"
"event": "unconfirmed-tx",
"address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
"token": "YOURTOKEN",
"url": "https://my.domain.com/callbacks/new-tx",
"callback_errors": 0
}
```

```javascript
var webhook = {
  "event": "unconfirmed-tx",
  "address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "url": "https://my.domain.com/callbacks/new-tx"
}
var url = 'https://api.blockcypher.com/v1/btc/main/hooks?token='+TOKEN;
$.post(url, JSON.stringify(webhook))
  .then(function(d) {console.log(d)});
{
"id": "399d0923-e920-48ee-8928-2051cbfbc369"
"event": "unconfirmed-tx",
"address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
"token": "YOURTOKEN",
"url": "https://my.domain.com/callbacks/new-tx",
"callback_errors": 0
}
```

```ruby
> block_cypher.event_webhook_subscribe("https://my.domain.com/callbacks/new-tx", "unconfirmed-tx", address:"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh")
=> {"id"=>"85883de4-2b01-4f27-a7d4-4400856e124a",
 "token"=>"YOURTOKEN",
 "url"=>"https://my.domain.com/callbacks/new-tx",
 "callback_errors"=>0,
 "address"=>"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
 "event"=>"unconfirmed-tx",
 "filter"=>"addr=15qx9ug952GWGTNn7Uiv6vode4RcGrRemh&event=unconfirmed-tx"}
```

```python
>>> from blockcypher import subscribe_to_address_webhook
>>> subscribe_to_address_webhook(callback_url='https://my.domain.com/callbacks/new-tx', subscription_address='15qx9ug952GWGTNn7Uiv6vode4RcGrRemh', event='unconfirmed-tx', api_key='YOUR_TOKEN')
'bcaf7c39-9a7f-4e8b-8ba4-23b3c1806039'

# returns the webhook ID
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  hook, err := btc.CreateHook(gobcy.Hook{Event: "tx-confirmation", Address: "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh", URL: "https://my.domain.com/callbacks/payments"})
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", hook)
}

//Result from `go run`:
//{ID:86a85527-7c36-435b-bab8-5638d033ead4 Event:tx-confirmation Hash: WalletName: Address:15qx9ug952GWGTNn7Uiv6vode4RcGrRemh Confirmations:6 Confidence:0 Script: URL:https://my.domain.com/callbacks/payments CallbackErrs:0}
```

```php
<?php
// Run on console:
// php -f .\sample\hook-api\CreateWebHookEndpoint.php

$webHook = new WebHook();
$webHook->setUrl("http://requestb.in/rwp6jirw?uniqid=" . uniqid());
$webHook->setEvent('unconfirmed-tx');
$webHook->setHash('2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b');

$webHookClient = new \BlockCypher\Client\WebHookClient($apiContext);
$webHook = $webHookClient->create($webHook);

{
  "url":"http://requestb.in/rwp6jirw?uniqid=5581998e19114",
  "event":"unconfirmed-tx",
  "hash":"2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b",
  "id":"d5ca3bd3-5dfb-477d-9fb4-ac3510af258d",
  "token":"c0afcccdde5081d6429de37d16166ead",
  "callback_errors":0,
  "filter":"event=unconfirmed-tx\u0026hash=2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b"
}
```

Using a partially filled out [Event](#event), you can create a WebHook using this resource. Check the [Event object description](#event) and [types of events](#types-of-events) to understand the options available for your events.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/hooks | POST | [Event](#event) | [Event](#event)

If successful, it will return the [Event](#event) with a newly generated **id**.

### List WebHooks Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/hooks?token=YOURTOKEN

[
  {
  "id": "399d0923-e920-48ee-8928-2051cbfbc369"
  "event": "unconfirmed-tx",
  "address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "token": "YOURTOKEN",
  "url": "https://my.domain.com/callbacks/new-tx",
  "callback_errors": 0
  }
]
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/hooks?token='+TOKEN)
  .then(function(d) {console.log(d)});
[
  {
  "id": "399d0923-e920-48ee-8928-2051cbfbc369"
  "event": "unconfirmed-tx",
  "address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "token": "YOURTOKEN",
  "url": "https://my.domain.com/callbacks/new-tx",
  "callback_errors": 0
  }
]
```

```ruby
> block_cypher.event_webhook_listall
=> [{"id"=>"85883de4-2b01-4f27-a7d4-4400856e124a",
  "token"=>"YOURTOKEN",
  "url"=>"https://my.domain.com/callbacks/new-tx",
  "callback_errors"=>0,
  "address"=>"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "event"=>"unconfirmed-tx",
  "filter"=>"addr=15qx9ug952GWGTNn7Uiv6vode4RcGrRemh&event=unconfirmed-tx"}]
```

```python
>>> from blockcypher import list_webhooks
>>> list_webhooks('YOUR_TOKEN')
[
    {
        "address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
        "callback_errors": 0,
        "confirmations": 6,
        "event": "unconfirmed-tx",
        "filter": "event=unconfirmed-tx&addr=15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
        "id": "bcaf7c39-9a7f-4e8b-8ba4-23b3c1806039",
        "token": "YOUR_TOKEN",
        "url": "https://my.domain.com/callbacks/new-tx"
    },
]
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  hooks, err := btc.ListHooks()
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", hooks)
}

//Result from `go run`:
//[{ID:86a85527-7c36-435b-bab8-5638d033ead4 Event:tx-confirmation Hash: WalletName: Address:15qx9ug952GWGTNn7Uiv6vode4RcGrRemh Confirmations:6 Confidence:0 Script: URL:https://my.domain.com/callbacks/payments CallbackErrs:0}]
```

```php
<?php
// Run on console:
// php -f .\sample\hook-api\ListWebHooksEndpoint.php

$webHookClient = new \BlockCypher\Client\WebHookClient($apiContext);
$webHooks = $webHookClient->getAll();

[
  {
    "id":"d5ca3bd3-5dfb-477d-9fb4-ac3510af258d",
    "token":"c0afcccdde5081d6429de37d16166ead",
    "url":"http://requestb.in/rwp6jirw?uniqid=5581998e19114",
    "callback_errors":0,
    "event":"unconfirmed-tx",
    "hash":"2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b",
    "filter":"event=unconfirmed-tx\u0026hash=2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b"
  }
]
```

This resource lists your currently active events, according to the base resource and $YOURTOKEN.

Resource | Method | Return Object
-------- | ------ | -------------
/hooks?token=$YOURTOKEN | GET | Array[[Event](#event)]

### WebHook ID Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/hooks/399d0923-e920-48ee-8928-2051cbfbc369

{
"id": "399d0923-e920-48ee-8928-2051cbfbc369"
"event": "unconfirmed-tx",
"address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
"token": "YOURTOKEN",
"url": "https://my.domain.com/callbacks/new-tx",
"callback_errors": 0
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/hooks/399d0923-e920-48ee-8928-2051cbfbc369')
  .then(function(d) {console.log(d)});
{
"id": "399d0923-e920-48ee-8928-2051cbfbc369"
"event": "unconfirmed-tx",
"address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
"token": "YOURTOKEN",
"url": "https://my.domain.com/callbacks/new-tx",
"callback_errors": 0
}
```

```ruby
> block_cypher.event_webhook_get("85883de4-2b01-4f27-a7d4-4400856e124a")
=> {"id"=>"85883de4-2b01-4f27-a7d4-4400856e124a",
 "token"=>"YOURTOKEN",
 "url"=>"https://my.domain.com/callbacks/new-tx",
 "callback_errors"=>0,
 "address"=>"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
 "event"=>"unconfirmed-tx",
 "filter"=>"addr=15qx9ug952GWGTNn7Uiv6vode4RcGrRemh&event=unconfirmed-tx"}
```

```python
>>> from blockcypher import get_webhook_info
>>> get_webhook_info('bcaf7c39-9a7f-4e8b-8ba4-23b3c1806039')
{
    "address": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
    "callback_errors": 0,
    "event": "unconfirmed-tx",
    "filter": "addr=15qx9ug952GWGTNn7Uiv6vode4RcGrRemh&event=unconfirmed-tx",
    "id": "bcaf7c39-9a7f-4e8b-8ba4-23b3c1806039",
    "token": "YOUR_TOKEN",
    "url": "https://my.domain.com/callbacks/new-tx"
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
  hook, err := btc.GetHook("86a85527-7c36-435b-bab8-5638d033ead4")
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", hook)
}

//Result from `go run`:
//{ID:86a85527-7c36-435b-bab8-5638d033ead4 Event:unconfirmed-tx Hash: WalletName: Address: Confirmations:0 Confidence:0 Script: URL:https://my.domain.com/callbacks/new-tx CallbackErrs:0}
```

```php
<?php
// Run on console:
// php -f .\sample\hook-api\WebHookIdEndpoint.php

$webHookClient = new \BlockCypher\Client\WebHookClient($apiContext);
$webHook = $webHookClient->get('d5ca3bd3-5dfb-477d-9fb4-ac3510af258d');

{
  "id":"d5ca3bd3-5dfb-477d-9fb4-ac3510af258d",
  "token":"c0afcccdde5081d6429de37d16166ead",
  "url":"http://requestb.in/rwp6jirw?uniqid=5581998e19114",
  "callback_errors":0,
  "event":"unconfirmed-tx",
  "hash":"2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b",
  "filter":"event=unconfirmed-tx\u0026hash=2b17f5589528f97436b5d563635b4b27ca8980aa20c300abdc538f2a8bfa871b"
}
```

This resource returns an [Event](#event) based on its generated *id*.

Resource | Method | Return Object
-------- | ------ | -------------
/hooks/$WEBHOOKID | GET | [Event](#event)

WEBHOOKID is a string representing the event's generated *id*, for example:

`399d0923-e920-48ee-8928-2051cbfbc369`

### Delete WebHook Endpoint

```shell
# Piping into grep to get status code
curl -X DELETE -Is https://api.blockcypher.com/v1/btc/main/hooks/399d0923-e920-48ee-8928-2051cbfbc369?token=YOURTOKEN | grep "HTTP/1.1"

HTTP/1.1 204 OK
```

```javascript
$.ajax({
  url: "https://api.blockcypher.com/v1/btc/main/hooks/399d0923-e920-48ee-8928-2051cbfbc369?token="+TOKEN,
  method: "DELETE"
});
```

```ruby
> block_cypher.event_webhook_delete("85883de4-2b01-4f27-a7d4-4400856e124a")
=> nil
```

```python
>>> from blockcypher import unsubscribe_from_webhook
>>> unsubscribe_from_webhook('bcaf7c39-9a7f-4e8b-8ba4-23b3c1806039')
True
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  err := btc.DeleteHook("86a85527-7c36-435b-bab8-5638d033ead4")
  if err != nil {
    fmt.Println(err)
  } else {
    fmt.Println("Hook deleted")
  }
}

//Result from `go run`:
//Hook deleted
```

```php
<?php
// Run on console:
// php -f .\sample\hook-api\DeleteWebHookEndpoint.php

$webHookClient = new \BlockCypher\Client\WebHookClient($apiContext);
$webHookClient->delete('d5ca3bd3-5dfb-477d-9fb4-ac3510af258d');

HTTP/1.1 204 OK
```

This resource deletes an active [Event](#event) based on its *id*. Remember to include your token, or the request will fail.

Resource | Method | Return Object
-------- | ------ | -------------
/hooks/$WEBHOOKID | DELETE | *nil*

WEBHOOKID is a string representing the event's generated *id*, for example:

`399d0923-e920-48ee-8928-2051cbfbc369`

If successful, it won't return any objects, but will respond with an HTTP Status Code 204.

### WebHook Signing

To guarantee the origin and integrity of the webhook data, webhooks can optionally be signed by our servers. An optional JSON attribute called "signkey" can be provided with the webhook when created to either:

1. A PEM-encoded ECDSA private key that you created.
2. The string "preset", in which case the BlockCypher signing key will be used. The associated x509 PKIX encoded public key for our webhook signing key is `MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEflgGqpIAC9k65JicOPBgXZUExen4rWLq05KwYmZHphTU/fmi3Oe/ckyxo2w3Ayo/SCO/rU2NB90jtCJfz9i1ow==`

Once the "signkey" attribute is set on a webhook, all webhook requests will be signed following the sha256-ecdsa scheme of the [HTTP signatures](https://tools.ietf.org/html/draft-cavage-http-signatures-12) specification.
