# Address Forwarding

One of the well-known benefits of cryptocurrency is the ability to allow users to partake in online commerce without necessarily requiring extensive setup barriers, like registering new accounts. In that spirit, our Address Forwarding API is the easiest way to accept---and consolidate---payments securely without forcing your users to create accounts and jump through unnecessary loops. It's also a generic way to automatically transfer value from one address to another. While there are many possible use cases, the two we hear most about are:

- A way to generate payment-specific addresses for which funds will automatically transfer to a main merchant address. Great for automatic merchandise (whether physical or virtual) processing.
- A method to easily fund a multisignature address from any wallet by providing a classic address that will automatically transfer to the multisignature/*pay-to-script-hash* address.

We do not take a fee on address forwarding, other than the required miner fee which depends on network conditions; forwards are free. However, as part of your own services, you can include a fee (either fixed or a percentage) that will also be automatically transfered to your own address in the same transaction. Fee-based business models are thus easily achieved, and moreover, easily auditable via the blockchain.

<aside class="notice">
Address forwarding is only available for paid plans with an active token, you can't create a address forwarding request without one. You can <a href="http://accounts.blockcypher.com">register for a token and pay for a plan here.</a>
</aside>

<aside class="notice">
Note that <b>mining fees</b> are deducted from any user-set <b>processing fees</b> first. This can often lead to lower processing fees than expected.
</aside>

<aside class="warning">
By default, all forwards will be debited with a <b>mining fee</b> that depends on current network fee levels. The amount of the fee is configurable, and if a <b>processing fee</b> is set, it will be deducted from that fee first. But for very small forwards, if the amount sent is even lower than the mining and user-set processing fees (or if the processing fees cannot cover the mining fee), the forward will fail.
</aside>

## Create Forward Endpoint

```shell
curl -d '{"destination":"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh","callback_url": "https://my.domain.com/callbacks/new-pay"}' https://api.blockcypher.com/v1/btc/main/forwards?token=YOURTOKEN

{
"input_address": "16uKw7GsQSzfMaVTcT7tpFQkd7Rh9qcXWX",
"destination": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
"callback_url": "https://my.domain.com/callbacks/forwards",
"id": "399d0923-e920-48ee-8928-2051cbfbc369",
"token": "YOURTOKEN"
}
```

```javascript
var payment = {
  "destination":"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "callback_url": "https://my.domain.com/callbacks/new-pay"
}
var url = 'https://api.blockcypher.com/v1/btc/main/forwards?token='+TOKEN;
$.post(url, JSON.stringify(payment))
  .then(function(d) {console.log(d)});
{
"input_address": "16uKw7GsQSzfMaVTcT7tpFQkd7Rh9qcXWX",
"destination": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
"callback_url": "https://my.domain.com/callbacks/forwards",
"id": "399d0923-e920-48ee-8928-2051cbfbc369",
"token": "YOURTOKEN"
}
```

```ruby
> block_cypher.create_forwarding_address("15qx9ug952GWGTNn7Uiv6vode4RcGrRemh", callback_url: "https://my.domain.com/callbacks/new-pay")
=> {"id"=>"72811fff-7826-4b04-8f99-4f492e84aeaa",
 "token"=>"YOURTOKEN",
 "destination"=>"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
 "input_address"=>"1Php2ZGrEtm5gUaEZNmTtUabmBei6zj9nP",
 "callback_url"=>"https://my.domain.com/callbacks/new-pay"}
```

```python
>>> from blockcypher import create_forwarding_address_with_details
>>> create_forwarding_address_with_details(destination_address='15qx9ug952GWGTNn7Uiv6vode4RcGrRemh', api_key='YOUR_TOKEN', callback_url='https://my.domain.com/callbacks/new-pay')
{
    "callback_url": "https://my.domain.com/callbacks/new-pay",
    "destination": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
    "id": "f35c80c2-3347-410d-b4ac-d049910289ec",
    "input_address": "1CUYiFY3LzEd9dXgR6ubRaYPTq2SMxFFCJ",
    "token": "YOUR_TOKEN"
}
# You can use create_forwarding_address to just return the input_address and not the rest of the JSON
```

```go
package main

import (
    "fmt"

    "github.com/blockcypher/gobcy"
)

func main() {
    btc := gobcy.API{"YOURTOKEN", "btc", "main"}
    payfwd, err := btc.CreatePayFwd(gobcy.PayFwd{Destination: "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh", CallbackURL: "https://my.domain.com/callbacks/forwards"})
    if err != nil {
        fmt.Println(err)
    }
    fmt.Printf("%+v\n", payfwd)
}

//Result from `go run`:
//{ID:6fbe2b48-fe79-44a2-9cdc-8114bc4d5fcc Destination:15qx9ug952GWGTNn7Uiv6vode4RcGrRemh InputAddr:17Ri1Shdzo5G8kiUys1qoeM8w1PNj1eFAK ProcessAddr: ProcessPercent:0 ProcessValue:0 CallbackURL:https://my.domain.com/callbacks/forwards EnableConfirm:false MiningFees:0 TXHistory:[]}
```

```php
<?php
// Run on console:
// php -f .\sample\payment-api\CreatePaymentEndpoint.php

$paymentForwardClient = new PaymentForwardClient($apiContext);
$options = array(
    'callback_url' => 'http://requestb.in/rwp6jirw?uniqid=' . uniqid()
);
$paymentForward = $paymentForwardClient->createForwardingAddress('15qx9ug952GWGTNn7Uiv6vode4RcGrRemh', $options);


{
  "destination":"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "callback_url":"http://requestb.in/rwp6jirw?uniqid=5583de7d87288",
  "id":"ec2b4b4f-eeb2-4824-b528-7d78a6f52492",
  "token":"c0afcccdde5081d6429de37d16166ead",
  "input_address":"17h2S1KtX7AqS9DJexqqSTNFCgwCoqqxhU"
}
```

First, to create an address forwarding address, you need to POST a partially filled [AddressForward](#addressforward) object to the payment creation endpoint. You need to include at least a **destination** address and your **token**; optionally, you can add a **callback_url**, processing fees (either percent or fixed) and a **process_fee_address**, and a few other options. You can see more details about these options in the [AddressForward](#addressforward) object details.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/forwards | POST | [AddressForward](#addressforward) | [addressForward](#addressforward)

In return, you'll get a more complete [AddressForward](#AddressForward) object, including an **input_address** and **id**.

<aside class="notice">
If you decide to have a <b>callback_url</b>, you'll receive a payload at that url whenever a payment is made to the <b>input_address</b>. The payload will come in the form of a <a href="#addressforwardcallback">AddressForwardCallback</a> object.
</aside>

## List Payments Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/forwards?token=YOURTOKEN

[
    {
    "input_address": "16uKw7GsQSzfMaVTcT7tpFQkd7Rh9qcXWX",
    "destination": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
    "callback_url": "https://my.domain.com/callbacks/forwards",
    "id": "399d0923-e920-48ee-8928-2051cbfbc369",
    "token": "YOURTOKEN"
    }
]
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/forwards?token='+TOKEN)
  .then(function(d) {console.log(d)});
[
    {
    "input_address": "16uKw7GsQSzfMaVTcT7tpFQkd7Rh9qcXWX",
    "destination": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
    "callback_url": "https://my.domain.com/callbacks/forwards",
    "id": "399d0923-e920-48ee-8928-2051cbfbc369",
    "token": "YOURTOKEN"
    }
]
```

```ruby
> block_cypher.list_forwarding_addresses
=> [{"id"=>"72811fff-7826-4b04-8f99-4f492e84aeaa",
  "token"=>"YOURTOKEN",
  "destination"=>"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
  "input_address"=>"1Php2ZGrEtm5gUaEZNmTtUabmBei6zj9nP",
  "callback_url"=>"https://my.domain.com/callbacks/new-pay"}]
```

```python
>>> from blockcypher import list_forwarding_addresses
>>> list_forwarding_addresses(api_key='YOUR_TOKEN')
[
    {
        "callback_url": "https://my.domain.com/callbacks/forwards",
        "destination": "15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
        "id": "f35c80c2-3347-410d-b4ac-d049910289ec",
        "input_address": "1CUYiFY3LzEd9dXgR6ubRaYPTq2SMxFFCJ",
        "token": "YOUR_TOKEN"
    }
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
    payfwds, err := btc.ListPayFwds()
    if err != nil {
        fmt.Println(err)
    }
    fmt.Printf("%+v\n", payfwds)
}

//Result from `go run`:
//[{ID:6fbe2b48-fe79-44a2-9cdc-8114bc4d5fcc Destination:15qx9ug952GWGTNn7Uiv6vode4RcGrRemh InputAddr:17Ri1Shdzo5G8kiUys1qoeM8w1PNj1eFAK ProcessAddr: ProcessPercent:0 ProcessValue:0 CallbackURL:https://my.domain.com/callbacks/forwards EnableConfirm:false MiningFees:0 TXHistory:[]}]
```

```php
<?php
// Run on console:
// php -f .\sample\payment-api\ListPaymentsEndpoint.php

$paymentForwardClient = new PaymentForwardClient($apiContext);
$paymentForwardArray = $paymentForwardClient->listForwardingAddresses();

[
  {
    "id":"ec2b4b4f-eeb2-4824-b528-7d78a6f52492",
    "token":"c0afcccdde5081d6429de37d16166ead",
    "destination":"15qx9ug952GWGTNn7Uiv6vode4RcGrRemh",
    "input_address":"17h2S1KtX7AqS9DJexqqSTNFCgwCoqqxhU",
    "callback_url":"http://requestb.in/rwp6jirw?uniqid=5583de7d87288"
  }
]
```

To list your currently active address forwarding addresses, you can use this endpoint.

Resource | Method | Return Object
-------- | ------ | -------------
/forwards | GET | Array[[AddressForward](#addressforward)]

Flag | Type | Effect
---- | ---- | ------
**start** | *integer* | Returns list of address forwards starting at the **start** index; useful for paging beyond the limit of 200 address forwards.

This returns the full array of your currently active address forwarding addresses, based on your token. By default, this endpoint only returns the first 200 address forwards. If you have more, you can page through them using the optional **start** parameter.

## Delete Payment Endpoint

```shell
# Piping to grep to just show status code
curl -X DELETE -Is https://api.blockcypher.com/v1/btc/main/forwards/399d0923-e920-48ee-8928-2051cbfbc369?token=YOURTOKEN | grep "HTTP/1.1"

HTTP/1.1 204 No Content
```

```javascript
var url = "https://api.blockcypher.com/v1/btc/main/forwards/399d0923-e920-48ee-8928-2051cbfbc369?token="+TOKEN
$.ajax({
  url: url,
  method: "DELETE"
});
```

```ruby
> block_cypher.delete_forwarding_address("72811fff-7826-4b04-8f99-4f492e84aeaa")
=> nil
```

```python
>>> from blockcypher import delete_forwarding_address
>>> delete_forwarding_address('f35c80c2-3347-410d-b4ac-d049910289ec')
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
    err := btc.DeletePayFwd("6fbe2b48-fe79-44a2-9cdc-8114bc4d5fcc ")
    if err != nil {
        fmt.Println(err)
    } else {
        fmt.Println("PayFwd Deleted")
    }
}

//Result from `go run`:
//PayFwd Deleted
```

```php
<?php
// Run on console:
// php -f .\sample\payment-api\DeletePaymentEndpoint.php

$paymentForwardClient = new PaymentForwardClient($apiContext);
$paymentForwardClient->deleteForwardingAddress('1fdf8f9b-cc37-4955-882b-8cbcd670a433');

HTTP/1.1 204 No Content
```

When you're done with an address forwarding address, you can delete it via its id.

Resource | Method | Return Object
-------- | ------ | -------------
/forwards/$PAYID | DELETE |  *nil*

PAYID is a string representing the address forwarding request you want to delete, for example:

`399d0923-e920-48ee-8928-2051cbfbc369`

This will return no object, but will return an HTTP Status Code 204 if the request was successfully deleted.
