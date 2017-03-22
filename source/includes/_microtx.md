# Microtransaction API

<aside class="warning">
Due to congestion on the Bitcoin network and the drastic increase in miner fees, microtransactions have become uneconomical. As a result, our microtransaction API isn't supported on bitcoin anymore.
</aside>

In addition to our normal [Transaction API](#transaction-api), we offer a unique, on-chain microtransaction endpoint that makes it easy to propagate smaller, nearly-instantly guaranteed, more frequent transactions that are still publicly auditable and trusted through their existence on the blockchain. How can we do what others claim require off-chain, centralized services?

Simple. We leverage our own infrastructure; in particular, our [Confidence Factor](#confidence-factor). When we reach 98% confidence that a microtransaction will reach the next block (usually ~8 seconds), we guarantee its value and return the completed microtransaction. As of this writing, we haven't had a single failed mictrotransaction, and we're improving our confidence data all the time.

We also pay for the mining fees for your first 8,000 microtransactions. After which, we automatically optimize fee structure to achieve a balance between guaranteeing confidence it will be in the next block and minimizing cost. If there isn't enough value in your microtransaction for the fee, we'll return an error.

<aside class="notice">
In order for us to keep track of mining fees, we require that all calls to the Microtransactions API use a token. You can <a href="http://accounts.blockcypher.com/">register for one here.</a>
</aside>

<aside class="warning">
The Microtransaction API will accept values between <b>7,000 satoshis (~$0.04)</b> and <b>4,000,000 satoshis (~$20)</b>. Anything below or above these thresholds will return an error. If you need to transfer larger values, use the <a href="#creating-transactions">standard Transaction process.</a> If you need to transfer lower values, you may want to reevaluate your application.
</aside>

<aside class="warning">
As with creating <a href="#creating-transactions">normal Transactions</a>, always use HTTPS with the Microtransaction API.
</aside>

## Microtransaction Endpoint

The endpoint is simple to employ, and uses a slightly pared down transaction object, appropriately called a [MicroTX](#microtx). For low value source addresses and testing, we support directly sending private keys---we'll construct, sign, and send the transaction for you. **We never store the private key, and remove it from server memory as soon as its used.** That said, where your application model supports it and/or when dealing with higher-value source addresses, we encourage you to send public keys and sign the data yourself---a two-step process similar to using `/tx/new` and `/tx/send` with [normal Transactions](#creating-transactions).

In either case, you use the same endpoint.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/micro | POST | [MicroTX](#microtx) | [MicroTX](#microtx)

The request object is a partially filled out [MicroTX](#microtx), whose format differs depending on whether you're sending a private key or public key for the source address. In either case, there are two options you can set:

- If not set, **change_address** defaults to the original source address computed by the private or public key sent. You can set it manually in the request object, useful if your source address is high-value, or you want to mitigate security risk after sending private keys.
- If not set, **wait_guarantee** defaults to *true*, which means the API will wait for BlockCypher to guarantee the transaction, using our [Confidence Factor](#confidence-factor). The guarantee usually takes around 8 seconds. If manually set to *false*, the Microtransaction endpoint will return as soon as the transaction is broadcast.

```shell
# Note resource change to bcy/test instead of btc/main, makes it easier to share private keys :)
curl -d '{ "from_private": "97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90", "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", "value_satoshis": 10000 }' https://api.blockcypher.com/v1/bcy/test/txs/micro?token=YOURTOKEN

{
"from_private": "97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90",
"to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
"value_satoshis": 10000,
"hash": "892fd7b36c1c3a2e5edb9b4a5d4ffd9ba74d78d3acf3b249991bd8d10a287dbd"
}
```

```javascript
var microtx = {
  from_private: "97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90",
  to_address: "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
  value_satoshis: 10000
}
var url = 'https://api.blockcypher.com/v1/bcy/test/txs/micro?token='+TOKEN;
$.post(url, JSON.stringify(microtx))
  .then(function(d) {console.log(d)});
> {
> "from_private": "97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90",
> "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
> "value_satoshis": 10000,
> "hash": "892fd7b36c1c3a2e5edb9b4a5d4ffd9ba74d78d3acf3b249991bd8d10a287dbd"
> }
```

```ruby
> bc_test.microtx_from_priv("97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90", "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", 20000)
=> {"from_private"=>"97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90",
 "to_address"=>"C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
 "value_satoshis"=>20000,
 "hash"=>"5d618233bccbeaa570072d719ff4718ae02eebffb748210166d9b6180ea8da59"}
```

```python
# The python library includes a method that will sign and verify a transaction client-side, so your private key never leaves your computer. It should be used instead for security:
>>> from blockcypher import simple_spend
>>> simple_spend(from_privkey='97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90', to_address='C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn', to_satoshis=10000, coin_symbol='bcy')
'7981c7849294648c1e79dd16077a388b808fcf8c20035aec7cc5315b37dacfee'

# If you prefer to use the MicroTX endpoint (say for the wait_guarantee), here's how to do that:
>>> import requests, json
>>> data = {'from_private': '97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90', 'to_address': 'C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn', 'value_satoshis': 10000}
>>> params = {'token': 'YOUR_TOKEN'}
>>> r = requests.post('https://api.blockcypher.com/v1/bcy/test/txs/micro', data=data, params=params)
>>> r.json()
{
    "from_private": "97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90", 
    "hash": "fadfc54f69297e12c17d18ad519e636d96b3c7f0638ca2b011baedaff7cac378", 
    "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", 
    "value_satoshis": 10000
}
```

```go
package main

import (
	"fmt"

	"github.com/blockcypher/gobcy"
)

func main() {
	//note the change to BlockCypher Testnet
	bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
	micro, err := bcy.SendMicro(gobcy.MicroTX{Priv: "5e4e8495f90a7d2e7091ec60a6586fdfb57b3108823ecfd955732aab1e3c18d7", ToAddr: "CBXcmktjHSwqtHsC8cgRPYT7Mpq9VT7ASp", Value: 200000})
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", micro)
}

//Result from `go run`:
//{Pubkey: Priv:5e4e8495f90a7d2e7091ec60a6586fdfb57b3108823ecfd955732aab1e3c18d7 Wif: ToAddr:CBXcmktjHSwqtHsC8cgRPYT7Mpq9VT7ASp Value:200000 ChangeAddr: Wait:false ToSign:[] Signatures:[] Hash:f3ef51a0d595e5c42a7599335adbb88c03b2fc104376c942dd2201139fe968f7 Inputs:[] Outputs:[] Fees:0}
```

```php
<?php
// Run on console:
// php -f .\sample\microtransaction-api\CreateMicroTXFromPrivateDocsSample.php

$microTXClient = new MicroTXClient($apiContext);
$microTX = $microTXClient->sendWithPrivateKey(
    "2c2cc015519b79782bd9c5af66f442e808f573714e3c4dc6df7d79c183963cff", // private key
    "C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi", // to address
    10000 // value (satoshis)
);

{
  "from_private":"2c2cc015519b79782bd9c5af66f442e808f573714e3c4dc6df7d79c183963cff",
  "to_address":"C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi",
  "value_satoshis":10000,
  "hash":"6886c3b2f48e410d83eb0ea4ef18396a699aba453a3210bc39bc7af99f061854"
}
```

### Via Private Keys

The simplest way to send a Microtransaction is by using a private key. Within the request object you must include **from_private** (or the equivalent **from_wif**), **to_address**, and **value_satoshis**. You can read more descriptions about these fields within [MicroTX object description](#microtx), although they should be self-explanatory.

The call will hold until the Confidence Factor reaches 98% (usually about 8 seconds). If successful thereafter, a completed [MicroTX object](#microtx) will be returned (which will include the transaction's **hash** for future queries), along with an HTTP Status Code 201.

<aside class="warning">
The private key method doesn't work with <b>uncompressed public addresses</b> as sources. You can still use uncompressed public addresses via the public key method listed below, but if you want to use the private key method, please use compressed public addresses (which has been the predominant standard since Bitcoin 0.6.0). You can read more about the distinction between <a href="https://bitcoin.org/en/developer-guide#public-key-formats">uncompressed and compressed keys here.</a>
</aside>

```shell
# Note resource change to bcy/test instead of btc/main
curl -d '{ "from_pubkey": "02152e2bb5b273561ece7bbe8b1df...", "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", "value_satoshis": 20000 }' https://api.blockcypher.com/v1/bcy/test/txs/micro?token=YOURTOKEN

{
  "from_pubkey": "02152e2bb5b273561ece7bbe...",
  "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
  "value_satoshis": 20000,
  "tosign": [
    "0f804bef755e623ba3f2c89795814ffa3a0dc53a376ad3deb3c7d3e91957cf12"
  ],
  "token": "YOURTOKEN",
  "inputs": [
		...
  ],
  "outputs": [
	...
  ],
  "fees": 735
}

# Using our CLI signer tool...
./signer 0f804bef755e623ba3f2c89795814ffa3a0dc53a376ad3deb3c7d3e91957cf12 $YOURPRIVATEKEY

30450221009050c3c966d1f895b6d6f6ab544113b88b3ce6908083a58170ef1e32da415e2f022037cb5dae353165f505d18a12c50b318c826b2c3552e41f70f7ade186e0434388

#request object is same as return object above, but + signature
curl -d '{ ..., "signatures": ["30450221009050c3c966d1f895b6d6f6ab544113b88b3ce6908083a58170ef1e32da415e2f022037cb5dae353165f505d18a12c50b318c826b2c3552e41f70f7ade186e0434388"],...}' https://api.blockcypher.com/v1/bcy/test/txs/micro?token=YOURTOKEN

{
  "from_pubkey": "02152e2bb5b273561ece7bbe8b1...",
  "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
  "value_satoshis": 20000,
  "hash": "ce8b3a2d5bb1f552c9d526559dd892b4ee...",
  "inputs": [
	...
  ],
  "outputs": [
	...
  ],
  "fees": 735
}
```

```javascript
// here we're using bitcoinjs built to expose bigi, buffer, and require
// you can use this library here:
// http://bitcoinjs.org/

var bitcoin = require("bitcoinjs-lib");
var bigi    = require("bigi");
var buffer  = require('buffer');
var key     = new bitcoin.ECPair(bigi.fromHex(my_hex_private_key), true);

var microtx = {
  from_pubkey: "02152e2bb5b273561ece7bbe...",
  to_address: "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
  value_satoshis: 20000
}
var url = 'https://api.blockcypher.com/v1/bcy/test/txs/micro?token='+TOKEN;

$.post(url, JSON.stringify(microtx)).then(function(tmptx) {
  // signing each of the hex-encoded string required to finalize the transaction
  tmptx.signatures = tmptx.tosign.map(function(tosign) {
    return key.sign(new buffer.Buffer(tosign, "hex")).toDER().toString("hex");
  });
  // sending back the transaction with all the signatures to broadcast
  $.post(url, tmptx).then(function(finaltx) {
    console.log(finaltx);
  })
});

> {
>   "from_pubkey": "02152e2bb5b273561ece7bbe8b1...",
>   "to_address": "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
>   "value_satoshis": 20000,
>   "hash": "ce8b3a2d5bb1f552c9d526559dd892b4ee...",
>   "inputs": [
> 	...
>   ],
>   "outputs": [
> 	...
>   ],
>   "fees": 735
> }
```

```ruby
# Our official Ruby SDK can generate the public key from your private key
# But this function signs locally instead of sending to the server
> bc_test.microtx_from_pub("97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90", "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", 20000)
=> {"from_pubkey"=>"02152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
 "to_address"=>"C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn",
 "value_satoshis"=>20000,
 "hash"=>"52330171c634e9094bb919e7e5bdaf73ca615d317fb5243224bec30b0f125eb4",
 "inputs"=>
  [{"prev_hash"=>"5d618233bccbeaa570072d719ff4718ae02eebffb748210166d9b6180ea8da59", "output_index"=>2},
   {"prev_hash"=>"ce8b3a2d5bb1f552c9d526559dd892b4eeee9a694fe885bf03c660fb304cd828", "output_index"=>1}],
 "outputs"=>
  [{"address"=>"C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", "value"=>20000},
   {"address"=>"CEyQhiXPtWk8q7hMkv2Kg728i4PYDM9qxf", "value"=>9997795},
   {"address"=>"CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9", "value"=>3716000}],
 "fees"=>735}
```

```go
package main

import (
	"fmt"

	"github.com/blockcypher/gobcy"
)

func main() {
	//note the change to BlockCypher Testnet
	bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
	keychain := gobcy.AddrKeychain{Private: "5e4e8495f90a7d2e7091ec60a6586fdfb57b3108823ecfd955732aab1e3c18d7", Public: "03daabc0502d41f52358690a65a7001a2a22df432706cce52a3c98da2c47229a51", Address: "CBXcmktjHSwqtHsC8cgRPYT7Mpq9VT7ASp"}
	//Send MicroTX with Public Key instead of Private Key
	micro, err := bcy.SendMicro(gobcy.MicroTX{Pubkey: keychain.Public, ToAddr: "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", Value: 300000})
	//Sign result locally with associated Private Key
	err = micro.Sign(keychain.Private)
	//Send signed MicroTX
	micro, err = bcy.SendMicro(micro)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", micro)
}

//Result from `go run`:
//{Pubkey:03daabc0502d41f52358690a65a7001a2a22df432706cce52a3c98da2c47229a51 Priv: Wif: ToAddr:C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn Value:300000 ChangeAddr: Wait:false ToSign:[] Signatures:[] Hash:934dfdaae9269ed7cf33f2484b3b3e8a7e5c1be12b9ea010e4bf95a2d11e59c7 Inputs:[{PrevHash:f3ef51a0d595e5c42a7599335adbb88c03b2fc104376c942dd2201139fe968f7 OutputIndex:0} {PrevHash:f3ef51a0d595e5c42a7599335adbb88c03b2fc104376c942dd2201139fe968f7 OutputIndex:2} {PrevHash:3332e1b220d0584f54a3cc8f8d1c6eafe2767b4f0f017379e8ca514ca4823c32 OutputIndex:1}] Outputs:[{Value:300000 Address:C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn} {Value:9958400 Address:BxeGtkPZM6qUsfkZ4pzaq4TF1RHNoqdZHT} {Value:700000 Address:CBXcmktjHSwqtHsC8cgRPYT7Mpq9VT7ASp}] Fees:3400}
```

```php
<?php
// Run on console:
// php -f .\sample\microtransaction-api\CreateMicroTXFromPubkeyDocsSample.php

$microTXClient = new MicroTXClient($apiContext);
$microTX = $microTXClient->sendSigned(
    "2c2cc015519b79782bd9c5af66f442e808f573714e3c4dc6df7d79c183963cff", // private key
    "C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi", // to address
    10000 // value (satoshis)
);

{
  "from_pubkey":"02d4e3404e175923adf89c932fab96758716f6a0a896890f2494c5d9141eb3f543",
  "to_address":"C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi",
  "value_satoshis":10000,
  "tosign":[
    "488e6defc3f3d26a20505730a1c64b8a3499e225f1bbd8a0e1b51b38251eed0a"
  ],
  "token":"c0afcccdde5081d6429de37d16166ead",
  "inputs":[
    ...
  ],
  "outputs":[
    ...
  ],
  "fees":735,
  "signatures":[
    "3045022100f99bef2add7d1fd9c...d6d64d8ae19ec090373"
  ],
  "hash":"7fc9e41e6ef29486bc4478ff063934ea9a1ecd08bbfb0a207b39ad92fe55bebf"
}
```

### Via Public Keys and Client-side Signing

If your security/application model allows it, we strongly recommend using public keys instead. Much like [creating normal transactions](#creating-transactions) the process requires two endpoint calls; the first is similar to the private key method, but with public keys. E.g., your required object must contain **from_pubkey** (instead of private keys), **to_address**, and **value_satoshis**.

After POSTing this to `/txs/micro` you'll receive another partially filled out [MicroTX](#microtx) object, but one containing a **tosign** array. You must then sign the data in this array with your locally-stored private key; signing can be a tricky process, but you can use our [signer tool](https://github.com/blockcypher/btcutils/tree/master/signer) as a baseline. Once that data is signed, it must be inserted to a **signatures** within the previously returned [MicroTX](#microtx) object. Then, you send this completed [MicroTX](#microtx) to `/txs/micro`.

As with the private key method, the call will hold until the Confidence Factor reaches 98% (usually about 8 seconds). If successful thereafter, a completed [MicroTX object](#microtx) will be returned (which will include the transaction's **hash** for future queries), along with an HTTP Status Code 201.

## Security vs Convenience

In general, sending any private key to an outside service is bad security practice. However, security and convenience are often in opposition. In terms of deciding whether passing private keys for microtransactions is a reasonable tradeoff, consider the following:

- The private key data is sent in a JSON document over SSL, protected against eavesdroppers.
- We never log or store any private key. They're only present on our systems during the time of signing.
- The use of a different change addresses is highly encouraged; in this way, leftover value from the source address gets transferred to another private key.
- Regardless of the above, never pass a private key for addresses with high value. The Microtransaction API is meant for low-value transactions and low-value addresses.

Finally, we only offer the option of passing private keys because we understand that signing on the client side can be difficult, depending on the environment. We hope that by fully understanding the tradeoffs and risks, you'll make the right choice for your situation.
