# Address API

BlockCypher's Address API allows you to look up information about public addresses on the blockchain, generate single-use, low-value key pairs with corresponding addresses, help generate multisig addresses, and collect multiple addresses into a single shortcut for address viewing, all [based on the coin/chain resource](#restful-resources) you've selected for your endpoints.

If you're new to blockchains, you can think of public addresses as similar to bank account numbers in a traditional ledger. The biggest differences:

- Anyone can generate a public address themselves (through [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) in Bitcoin). No single authority is needed to generate new addresses; it's just public-private key cryptography.
- Public addresses are significantly more lightweight. Consequently, and unlike traditional bank accounts, you can (and should!) generate new addresses for every transaction.
- Addresses can also leverage [*pay-to-script-hash*](https://en.bitcoin.it/wiki/Pay_to_script_hash), which means they can represent exotic things beyond a single private-public key pair; the most prominent example being multi-signature addresses that require n-of-m signatures to spend.

## Address Balance Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/addrs/1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD/balance

{
"address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
"total_received": 4433416,
"total_sent": 0,
"balance": 4433416,
"unconfirmed_balance": 0,
"final_balance": 4433416,
"n_tx": 7,
"unconfirmed_n_tx": 0,
"final_n_tx": 7
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/addrs/1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD/balance')
  .then(function(d) {console.log(d)});
> {
>   "address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
>   "total_received": 4433416,
>   "total_sent": 0,
>   "balance": 4433416,
>   "unconfirmed_balance": 0,
>   "final_balance": 4433416,
>   "n_tx": 7,
>   "unconfirmed_n_tx": 0,
>   "final_n_tx": 7
> }
```

```ruby
# Full balance
> block_cypher.address_balance("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD")
=> {"address"=>"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
 "total_received"=>4433416,
 "total_sent"=>0,
 "balance"=>4433416,
 "unconfirmed_balance"=>0,
 "final_balance"=>4433416,
 "n_tx"=>7,
 "unconfirmed_n_tx"=>0,
 "final_n_tx"=>7}

# Just final balance
> block_cypher.address_final_balance("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD")
=> 4433416
```

```python
>>> from blockcypher import get_address_overview
>>> get_address_overview('1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD')
{
    "address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", 
    "balance": 4433416, 
    "final_balance": 4433416, 
    "final_n_tx": 7, 
    "n_tx": 7, 
    "total_received": 4433416, 
    "total_sent": 0, 
    "unconfirmed_balance": 0, 
    "unconfirmed_n_tx": 0
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
	addr, err := btc.GetAddrBal("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", nil)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", addr)
}

//Result from `go run`:
//{Address:1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD Wallet:{Name: Addresses:[]} HDWallet:{Name: ExtPubKey: SubchainIndexes:[] Chains:[]} TotalReceived:4443378 TotalSent:0 Balance:4443378 UnconfirmedBalance:0 FinalBalance:4443378 NumTX:8 UnconfirmedNumTX:0 FinalNumTX:8 TXs:[] TXRefs:[] UnconfirmedTXRefs:[] HasMore:false}
```

```php
<?php
// Run on console:
// php -f .\sample\address-api\AddressBalanceEndpoint.php

$addressClient = new AddressClient($apiContext);
$addressBalance = $addressClient->getBalance('1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD');

{
  "address":"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
  "total_received":4433416,
  "total_sent":0,
  "balance":4433416,
  "unconfirmed_balance":0,
  "final_balance":4433416,
  "n_tx":7,
  "unconfirmed_n_tx":0,
  "final_n_tx":7
}
```

The Address Balance Endpoint is the simplest---and fastest---method to get a subset of information on a public address.

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS/balance | GET | [Address](#address)

Flag | Type | Effect
---- | ---- | ------
**omitWalletAddresses** | *bool* | If **omitWalletAddresses** is *true* and you're querying a [Wallet](#wallet) or [HDWallet](#hdwallet), the response will omit address information (useful to speed up the API call for larger wallets).

ADDRESS is a *string* representing the public address (or wallet/HD wallet name) you're interested in querying, for example:

`1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD`

The returned object contains information about the address, including its balance in satoshis and the number of transactions associated with it. The endpoint omits any detailed transaction information, but if that isn't required by your application, then it's the fastest and preferred way to get public address information.

## Address Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/addrs/1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD

{
"address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
"total_received": 4433416,
"total_sent": 0,
"balance": 4433416,
"unconfirmed_balance": 0,
"final_balance": 4433416,
"n_tx": 7,
"unconfirmed_n_tx": 0,
"final_n_tx": 7,
"txrefs": [
{
  "tx_hash": "14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e",
  "block_height": 302013,
  "tx_input_n": -1,
  "tx_output_n": 0,
  "value": 20213,
  "ref_balance": 4433416,
  "spent": false,
  "confirmations": 63066,
  "confirmed": "2014-05-22T03:46:25Z",
  "double_spend": false
},
{
  "tx_hash": "4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869",
  "block_height": 302002,
  "tx_input_n": -1,
  "tx_output_n": 0,
  "value": 40596,
  "ref_balance": 4413203,
  "spent": false,
  "confirmations": 63077,
  "confirmed": "2014-05-22T02:56:08Z",
  "double_spend": false
},
...
],
"tx_url": "https://api.blockcypher.com/v1/btc/main/txs/"
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/addrs/1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD')
  .then(function(d) {console.log(d)});
> {
> "address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
> "total_received": 4433416,
> "total_sent": 0,
> "balance": 4433416,
> "unconfirmed_balance": 0,
> "final_balance": 4433416,
> "n_tx": 7,
> "unconfirmed_n_tx": 0,
> "final_n_tx": 7,
> "txrefs": [
> 	{
> 	"tx_hash": "14b1052855bbf6561bc4db8aa501762...",
> 	"block_height": 302013,
> 	"tx_input_n": -1,
> 	"tx_output_n": 0,
> 	"value": 20213,
>   "ref_balance": 4433416,
> 	"spent": false,
> 	"confirmations": 55061,
> 	"confirmed": "2014-05-22T03:46:25Z",
> 	"double_spend": false
> 	},
> 	{
> 	"tx_hash": "4cff011ec53022f2ae47197d1a2fd4a6...",
> 	"block_height": 302002,
> 	"tx_input_n": -1,
> 	"tx_output_n": 0,
> 	"value": 40596,
>   "ref_balance": 4413203,
> 	"spent": false,
> 	"confirmations": 55072,
> 	"confirmed": "2014-05-22T02:56:08Z",
> 	"double_spend": false
> 	},
> ...
> ],
> "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/"
> }
```

```ruby
> block_cypher.address_details("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD")
=> {"address"=>"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
 "total_received"=>4433416,
 "total_sent"=>0,
 "balance"=>4433416,
 "unconfirmed_balance"=>0,
 "final_balance"=>4433416,
 "n_tx"=>7,
 "unconfirmed_n_tx"=>0,
 "final_n_tx"=>7,
 "txrefs"=> [
  {"tx_hash"=>"14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e",
	 "block_height"=>302013,
	 "tx_input_n"=>-1,
	 "tx_output_n"=>0,
	 "value"=>20213,
   "ref_balance"=>4433416,
	 "spent"=>false,
	 "confirmations"=>59220,
	 "confirmed"=>"2014-05-22T03:46:25Z",
	 "double_spend"=>false},
	{"tx_hash"=>"4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869",
	 "block_height"=>302002,
	 "tx_input_n"=>-1,
	 "tx_output_n"=>0,
	 "value"=>40596,
   "ref_balance"=>4413203,
	 "spent"=>false,
	 "confirmations"=>59231,
	 "confirmed"=>"2014-05-22T02:56:08Z",
	 "double_spend"=>false},
	...],
 "tx_url"=>"https://api.blockcypher.com/v1/btc/main/txs/"}
```

```python
>>> from blockcypher import get_address_details
>>> get_address_details('1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD')
{
    "address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", 
    "balance": 4433416, 
    "final_balance": 4433416, 
    "final_n_tx": 7, 
    "n_tx": 7, 
    "total_received": 4433416, 
    "total_sent": 0, 
    "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/", 
    "txrefs": [
        {
            "block_height": 302013, 
            "confirmations": 77809, 
            "confirmed": "datetime.datetime(2014, 5, 22, 3, 46, 25, 0, tzinfo=tzutc())", 
            "double_spend": False, 
            "ref_balance": 4433416, 
            "spent": False, 
            "tx_hash": "14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e", 
            "tx_input_n": -1, 
            "tx_output_n": 0, 
            "value": 20213
        }, 
        {
            "block_height": 302002, 
            "confirmations": 77820, 
            "confirmed": "datetime.datetime(2014, 5, 22, 2, 56, 8, 0, tzinfo=tzutc())", 
            "double_spend": False, 
            "ref_balance": 4413203, 
            "spent": False, 
            "tx_hash": "4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869", 
            "tx_input_n": -1, 
            "tx_output_n": 0, 
            "value": 40596
        }, 
        ...

    ], 
    "unconfirmed_balance": 0, 
    "unconfirmed_n_tx": 0, 
    "unconfirmed_txrefs": []
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
	addr, err := btc.GetAddr("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", nil)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", addr)
}

//Result from `go run`:
//{Address:1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD Wallet:{Name: Addresses:[]} HDWallet:{Name: ExtPubKey: SubchainIndexes:[] Chains:[]} TotalReceived:4443378 TotalSent:0 Balance:4443378 UnconfirmedBalance:0 FinalBalance:4443378 NumTX:8 UnconfirmedNumTX:0 FinalNumTX:8 TXs:[] TXRefs:[{Address: BlockHeight:382021 TXHash:ce53a6ed8b8f6b39cf86d4f300c12a33b69816014cc7261f4ced8b0e23bd4aeb TXInputN:-1 TXOutputN:2 Value:9962 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:39103 Script: RefBalance:4443378 Confidence:0 Confirmed:2015-11-04 14:28:14 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:302013 TXHash:14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e TXInputN:-1 TXOutputN:0 Value:20213 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:119111 Script: RefBalance:4433416 Confidence:0 Confirmed:2014-05-22 03:46:25 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:302002 TXHash:4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869 TXInputN:-1 TXOutputN:0 Value:40596 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:119122 Script: RefBalance:4413203 Confidence:0 Confirmed:2014-05-22 02:56:08 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:302002 TXHash:ea1cbb874ed4e40add51b4d65b877dc4e8d69bb63f5b2220a79d673c413b778a TXInputN:-1 TXOutputN:0 Value:101491 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:119122 Script: RefBalance:4372607 Confidence:0 Confirmed:2014-05-22 02:56:08 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:292586 TXHash:b4735a0690dab16b8789fceaf81c511f3be484e319f684cc214380eaa2851030 TXInputN:-1 TXOutputN:0 Value:3500000 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:128538 Script: RefBalance:4271116 Confidence:0 Confirmed:2014-03-26 17:08:04 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:292505 TXHash:0416b8db5db4fa088437008aea7889e966e326f11c52c1da95161cd2ded95185 TXInputN:-1 TXOutputN:0 Value:100000 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:128619 Script: RefBalance:771116 Confidence:0 Confirmed:2014-03-26 04:18:38 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:292455 TXHash:995a50e05d197be88d4da74160b4bcd2c363ebb1a49f95e572667d580bc70aba TXInputN:-1 TXOutputN:0 Value:500000 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:128669 Script: RefBalance:671116 Confidence:0 Confirmed:2014-03-25 21:32:08 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0} {Address: BlockHeight:292325 TXHash:0c83c8321537a7c79dc6214788944ba6cd5ea76f0594453b6251fcf1856f2e4b TXInputN:-1 TXOutputN:0 Value:171116 Pref: Spent:false DoubleSpend:false DoubleOf: Confirmations:128799 Script: RefBalance:171116 Confidence:0 Confirmed:2014-03-25 00:07:31 +0000 UTC SpentBy: Received:0001-01-01 00:00:00 +0000 UTC ReceivedCount:0}] UnconfirmedTXRefs:[] HasMore:false}
```

```php
<?php

// Run on console:
// php -f .\sample\address-api\AddressEndpoint.php

$addressClient = new AddressClient($apiContext);
$address = $addressClient->get('1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD');

{
  "address":"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
  "total_received":4433416,
  "total_sent":0,
  "balance":4433416,
  "unconfirmed_balance":0,
  "final_balance":4433416,
  "n_tx":7,
  "unconfirmed_n_tx":0,
  "final_n_tx":7,
  "txrefs":[
    {
      "tx_hash":"14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e",
      "block_height":302013,
      "tx_input_n":-1,
      "tx_output_n":0,
      "value":20213,
      "ref_balance": 4433416,
      "spent":false,
      "confirmations":58584,
      "confirmed":"2014-05-22T03:46:25Z",
      "double_spend":false
    },
    ...
  ],
  "tx_url":"https://api.blockcypher.com/v1/btc/main/txs/"
}
```

The default Address Endpoint strikes a balance between speed of response and data on Addresses. It returns more information about an address' transactions than the [Address Balance Endpoint](#address-balance-endpoint) but doesn't return full transaction information (like the [Address Full Endpoint](#address-full-endpoint)).

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS | GET | [Address](#address)

Flag | Type | Effect
---- | ---- | ------
**unspentOnly** | *bool* | If **unspentOnly** is *true*, filters response to only include unspent transaction outputs (UTXOs).
**includeScript** | *bool* | If **includeScript** is *true*, includes raw script of input or output within returned [TXRefs.](#txref)
**includeConfidence** | *bool* | If *true*, includes the **confidence** attribute (useful for unconfirmed transactions) within returned [TXRefs.](#txref) For more info about this figure, check the [Confidence Factor](#confidence-factor) documentation.
**before** | *integer* | Filters response to only include transactions below **before** height in the blockchain.
**after** | *integer* | Filters response to only include transactions above **after** height in the blockchain.
**limit** | *integer* | **limit** sets the minimum number of returned TXRefs; there can be less if there are less than **limit** TXRefs associated with this address, but there can be more in the rare case of more TXRefs in the block at the bottom of your call. This ensures paging by block height never misses TXRefs. Defaults to 200, maximum is 2000.
**confirmations** | *integer* | If set, only returns the **balance** and TXRefs that have *at least* this number of **confirmations**.
**confidence** | *integer* | Filters response to only include TXRefs above **confidence** in percent; e.g., if this is set to 99, will only return TXRefs with 99% confidence or above (including all confirmed TXRefs). For more detail on confidence, check the [Confidence Factor](#confidence-factor) documentation.
**omitWalletAddresses** | *bool* | If **omitWalletAddresses** is *true* and you're querying a [Wallet](#wallet) or [HDWallet](#hdwallet), the response will omit address information (useful to speed up the API call for larger wallets).

ADDRESS is a *string* representing the public address (or wallet/HD wallet name) you're interested in querying, for example:

`1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD`

The returned object contains information about the address, including its balance in satoshis, the number of transactions associated with it, and transaction inputs/outputs in descending order by block height---and if multiple transaction inputs/outputs associated with this address exist within the same block, by descending block index (position in block).

## Address Full Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/addrs/1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD/full?before=300000

{
"address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
"total_received": 4433416,
"total_sent": 0,
"balance": 4433416,
"unconfirmed_balance": 0,
"final_balance": 4433416,
"n_tx": 7,
"unconfirmed_n_tx": 0,
"final_n_tx": 7,
"txs": [
	{
	"block_hash": "0000000000000000af64802c79...",
	"block_height": 292586,
	"hash": "b4735a0690dab16b8789fceaf81c511f...",
	"addresses": [
		"18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa",
		"1AAuRETEcHDqL4VM3R97aZHP8DSUHxpkFV",
		"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
		"1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG"
	],
	"total": 3537488,
	"fees": 20000,
	"size": 438,
	"preference": "medium",
	"relayed_by": "",
	"confirmed": "2014-03-26T17:08:04Z",
	"received": "2014-03-26T17:08:04Z",
	"ver": 1,
	"lock_time": 0,
	"double_spend": false,
	"vin_sz": 2,
	"vout_sz": 2,
	"confirmations": 64492,
	"confidence": 1,
	"inputs": [
		{
		"prev_hash": "729f6469b59fea5da7...",
		"output_index": 0,
		"script": "483045022100d06cdad1a...",
		"output_value": 3500000,
		"sequence": 4294967295,
		"addresses": [
			"1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG"
		],
		"script_type": "pay-to-pubkey-hash"
		},
		...
	],
	"outputs": [
		{
		"value": 3500000,
		"script": "76a9148629647bd642a237...",
		"addresses": [
			"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"
		],
		"script_type": "pay-to-pubkey-hash"
		},
		{
		"value": 37488,
		"script": "76a9145049e2ad94ed9c68...",
		"spent_by": "3ebe4bb294beaed58aca83...",
		"addresses": [
			"18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa"
		],
		"script_type": "pay-to-pubkey-hash"
		}
	]
	},
	...,
]
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/addrs/1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD/full?before=300000')
  .then(function(d) {console.log(d)});
> {
> "address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
> "total_received": 4433416,
> "total_sent": 0,
> "balance": 4433416,
> "unconfirmed_balance": 0,
> "final_balance": 4433416,
> "n_tx": 7,
> "unconfirmed_n_tx": 0,
> "final_n_tx": 7,
> "txs": [
> 	{
> 	"block_hash": "0000000000000000af64802c79...",
> 	"block_height": 292586,
> 	"hash": "b4735a0690dab16b8789fceaf81c511f...",
> 	"addresses": [
> 		"18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa",
> 		"1AAuRETEcHDqL4VM3R97aZHP8DSUHxpkFV",
> 		"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
> 		"1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG"
> 	],
> 	"total": 3537488,
> 	"fees": 20000,
> 	"size": 438,
> 	"preference": "medium",
> 	"relayed_by": "",
> 	"confirmed": "2014-03-26T17:08:04Z",
> 	"received": "2014-03-26T17:08:04Z",
> 	"ver": 1,
> 	"lock_time": 0,
> 	"double_spend": false,
> 	"vin_sz": 2,
> 	"vout_sz": 2,
> 	"confirmations": 64492,
> 	"confidence": 1,
> 	"inputs": [
> 		{
> 		"prev_hash": "729f6469b59fea5da7...",
> 		"output_index": 0,
> 		"script": "483045022100d06cdad1a...",
> 		"output_value": 3500000,
> 		"sequence": 4294967295,
> 		"addresses": [
> 			"1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG"
> 		],
> 		"script_type": "pay-to-pubkey-hash"
> 		},
> 		...
> 	],
> 	"outputs": [
> 		{
> 		"value": 3500000,
> 		"script": "76a9148629647bd642a237...",
> 		"addresses": [
> 			"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"
> 		],
> 		"script_type": "pay-to-pubkey-hash"
> 		},
> 		{
> 		"value": 37488,
> 		"script": "76a9145049e2ad94ed9c68...",
> 		"spent_by": "3ebe4bb294beaed58aca83...",
> 		"addresses": [
> 			"18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa"
> 		],
> 		"script_type": "pay-to-pubkey-hash"
> 		}
> 	]
> 	},
> 	...,
> ]
> }
```

```ruby
> block_cypher.address_full_txs("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD")
=> {"address"=>"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
 "total_received"=>4433416,
 "total_sent"=>0,
 "balance"=>4433416,
 "unconfirmed_balance"=>0,
 "final_balance"=>4433416,
 "n_tx"=>7,
 "unconfirmed_n_tx"=>0,
 "final_n_tx"=>7,
 "txs"=>[
  {"block_hash"=>"00000000000000006548ac8dc283c97e8165023dc1fdbbca2eaa75f0143f4a8c",
  "block_height"=>302013,
  "hash"=>"14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e",
  "addresses"=>["17astdTmG8zzVmry8mV8A7at Ar3XefEgRX", "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"],
  "total"=>8835413,
  "fees"=>10000,
  "size"=>258,
  "preference"=>"medium",
  "relayed_by"=>"",
  "confirmed"=>"2014-05-22T03:46:25Z",
  "received"=>"2014-05-22T03:46:25Z",
  "ver"=>1,
  "lock_time"=>0,
  "double_spend"=>false,
	"vin_sz"=>1,
	"vout_sz"=>2,
	"confirmations"=>59221,
	"confidence"=>1,
	"inputs"=>[
	 {"prev_hash"=>"4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869",
	 "output_index"=>1,
	 "script" =>"483045022035695e3b237733c70a5...",
	 "output_value"=> 8845413,
	 "sequence"=>4294967295,
	 "addresses"=>["17astdTmG8zzVmry8mV8A7atAr3XefEgRX"],
	 "script_type"=>"pay-to-pubkey-hash"}],
	"outputs"=>[
	 {"value"=>20213,
	 "script"=>"76a9148629647bd642a2372d846a7660e210c8414f047c88ac",
	 "addresses"=>["1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"],
	 "script_type"=>"pay-to-pubkey-hash"},
	 ... ]},
  ...
 ]}
```

```python
>>> from blockcypher import get_address_full
>>> get_address_full(address='1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD', before_bh=300000)
{
    "address": "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", 
    "balance": 4433416, 
    "final_balance": 4433416, 
    "final_n_tx": 7, 
    "n_tx": 7, 
    "total_received": 4433416, 
    "total_sent": 0, 
    "txs": [
        {
            "addresses": [
                "18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa", 
                "1AAuRETEcHDqL4VM3R97aZHP8DSUHxpkFV", 
                "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", 
                "1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG"
            ], 
            "block_hash": "0000000000000000af64802c79f9b22e9091eb5548b4b662d5e444e61885923b", 
            "block_height": 292586, 
            "confidence": 1, 
            "confirmations": 87238, 
            "confirmed": "datetime.datetime(2014, 3, 26, 17, 8, 4, 0, tzinfo=tzutc())", 
            "double_spend": False, 
            "fees": 20000, 
            "hash": "b4735a0690dab16b8789fceaf81c511f3be484e319f684cc214380eaa2851030", 
            "inputs": [
                {
                    "addresses": [
                        "1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG"
                    ], 
                    "output_index": 0, 
                    "output_value": 3500000, 
                    "prev_hash": "729f6469b59fea5da77457f3291e2623c2516e3e8e7afc782687c6d59f4c5e41", 
                    "script": "483045022100d06cdad1a54081e8499a4117f9f52d7fbc83c679dda7e3c22c08e964915b7354022010a2d6af1601d28d33a456dab2bccf3fbde35b2f3a9db82f72d675c90d015571014104672a00c8ee6fa23d68094dd98188ea1491848498554a10e13194851b614168b225b28b7f5a1c6ba98b5463438ef030c48b60533031ff2de84104e549d8d06ea9", 
                    "script_type": "pay-to-pubkey-hash", 
                    "sequence": 4294967295
                }, 
                ...,

            ], 
            "lock_time": 0, 
            "outputs": [
                {
                    "addresses": [
                        "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"
                    ], 
                    "script": "76a9148629647bd642a2372d846a7660e210c8414f047c88ac", 
                    "script_type": "pay-to-pubkey-hash", 
                    "value": 3500000
                }, 
                ...,
            ], 
            "preference": "medium", 
            "received": "datetime.datetime(2014, 3, 26, 17, 8, 4, 0, tzinfo=tzutc())", 
            "relayed_by": "", 
            "size": 438, 
            "total": 3537488, 
            "ver": 1, 
            "vin_sz": 2, 
            "vout_sz": 2
        }, 
        ...,
    ], 
    "unconfirmed_balance": 0, 
    "unconfirmed_n_tx": 0
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
	addr, err := btc.GetAddrFull("1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD", nil)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", addr)
}

//Result from `go run`:
//{Address:1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD Wallet:{Name: Addresses:[]} HDWallet:{Name: ExtPubKey: SubchainIndexes:[] Chains:[]} TotalReceived:4443378 TotalSent:0 Balance:4443378 UnconfirmedBalance:0 FinalBalance:4443378 NumTX:8 UnconfirmedNumTX:0 FinalNumTX:8 TXs:[{BlockHash:000000000000000000768df6acbfc5b3bc18bcf3154a3e3ce02772b01d03de50 BlockHeight:382021 Hash:ce53a6ed8b8f6b39cf86d4f300c12a33b69816014cc7261f4ced8b0e23bd4aeb Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD 1FhCLQK2ZXtCUQDtG98p6fVH7S6mxAsEey 1LL74HS54ptRdWFUodXMceRbu5Ls4Q4Bhb 1Lj3WeRAmyN98jyiWE6AtnMKfzUzrPUL9z] Total:124916 Fees:10000 Size:505 Preference:low RelayedBy:107.170.13.184:8333 Received:2015-11-04 14:06:13.945 +0000 UTC Confirmed:2015-11-04 14:28:14 +0000 UTC Confirmations:39103 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:2 VoutSize:4 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:c00be7265d1ffba6ca2b870e463b5b44829b5bcdfc72a785e1ba946c6dd6d6ae OutputIndex:0 OutputValue:127948 Addresses:[1LL74HS54ptRdWFUodXMceRbu5Ls4Q4Bhb] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:4730440220271259e739026dd93fff75775fee0ee990ff4de1421d256c8af2ae46f64361a30220689f19d354cba767e204802034e4053cd478d05806aee89583ef5ad4a2fe0044014104270a9e314b83bc2df501bd0ecfe21b521780be7ffc11f939395c9dade09a14a91e59d81889b0ccf76ce15f1be2eabe63774ee1b4268b07e6b930531bd0e6bc87 Age:0 WalletName:} {PrevHash:03f87b4c20d5283dd944404f2132fd616bac23cfe9c701caa27c65156ee56ec7 OutputIndex:0 OutputValue:6968 Addresses:[1LL74HS54ptRdWFUodXMceRbu5Ls4Q4Bhb] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100c1b4c608e2f33ad730aba6fe6c93f0d25d33af8509027457b81a0c91950f4d3c02201fc0fb2a43a66673680bc9027acda3a2174b80fa6d397b6a93d2293844d17cb6014104270a9e314b83bc2df501bd0ecfe21b521780be7ffc11f939395c9dade09a14a91e59d81889b0ccf76ce15f1be2eabe63774ee1b4268b07e6b930531bd0e6bc87 Age:0 WalletName:}] Outputs:[{SpentBy: Value:18056 Addresses:[1Lj3WeRAmyN98jyiWE6AtnMKfzUzrPUL9z] ScriptType:pay-to-pubkey-hash Script:76a914d85df4dafe54bea74b59c65bc594b0c3231d836e88ac DataHex: DataString:} {SpentBy: Value:11622 Addresses:[1FhCLQK2ZXtCUQDtG98p6fVH7S6mxAsEey] ScriptType:pay-to-pubkey-hash Script:76a914a12bdc6beab772eb7a6abacfd3ae5574fc1c8cc388ac DataHex: DataString:} {SpentBy: Value:9962 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:83c1a2baf462e89831cb61309fb30051288a8eefb2d230464bfe6a8e4c316c61 Value:85276 Addresses:[1LL74HS54ptRdWFUodXMceRbu5Ls4Q4Bhb] ScriptType:pay-to-pubkey-hash Script:76a914d4075955fbad83455e88d6e7d737bb55fd60ebae88ac DataHex: DataString:}]} {BlockHash:00000000000000006548ac8dc283c97e8165023dc1fdbbca2eaa75f0143f4a8c BlockHeight:302013 Hash:14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX 1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] Total:8835413 Fees:10000 Size:258 Preference:medium RelayedBy: Received:2014-05-22 03:46:25 +0000 UTC Confirmed:2014-05-22 03:46:25 +0000 UTC Confirmations:119111 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869 OutputIndex:1 OutputValue:8845413 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022035695e3b237733c70a56286eccd8df41b4d22cd103ed9b2df44010caa3bc71430221008f58461c937e8fe6cc6d37a9aaee3927762cce4565a4c386bbcd9d82915acfc50141047b1d511b8559a2003ca88715bc8331f057fa4ebf11f411142509a8ffd2f2d36d5a5e4b6019d6eb3e16878f24fd8d55676050c28b4bc5e4c44f39245beedae100 Age:0 WalletName:}] Outputs:[{SpentBy: Value:20213 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:582a50f3a756c3261f8f085185e5975a762e239e95a30bcf1a4f2e31e0f834ab Value:8815200 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX] ScriptType:pay-to-pubkey-hash Script:76a9144838f65fc4e06c644423ad0430de11ca5785dcd088ac DataHex: DataString:}]} {BlockHash:00000000000000006d3bdfe1127d541110a18ce5b54bcdeb51e10399f6ec00b2 BlockHeight:302002 Hash:4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX 1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] Total:8886009 Fees:10000 Size:258 Preference:medium RelayedBy: Received:2014-05-22 02:56:08 +0000 UTC Confirmed:2014-05-22 02:56:08 +0000 UTC Confirmations:119122 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:ea1cbb874ed4e40add51b4d65b877dc4e8d69bb63f5b2220a79d673c413b778a OutputIndex:1 OutputValue:8896009 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100f50d876c0f213f99319752d4381c1be341f187bf32c53e2e89fae0df34bce2a202206d1f73005cdd6dcaab3cd74ef3870950202623e976b737f75980e91447fea7cc0141047b1d511b8559a2003ca88715bc8331f057fa4ebf11f411142509a8ffd2f2d36d5a5e4b6019d6eb3e16878f24fd8d55676050c28b4bc5e4c44f39245beedae100 Age:0 WalletName:}] Outputs:[{SpentBy: Value:40596 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e Value:8845413 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX] ScriptType:pay-to-pubkey-hash Script:76a9144838f65fc4e06c644423ad0430de11ca5785dcd088ac DataHex: DataString:}]} {BlockHash:00000000000000006d3bdfe1127d541110a18ce5b54bcdeb51e10399f6ec00b2 BlockHeight:302002 Hash:ea1cbb874ed4e40add51b4d65b877dc4e8d69bb63f5b2220a79d673c413b778a Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX 1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] Total:8997500 Fees:10000 Size:259 Preference:medium RelayedBy: Received:2014-05-22 02:56:08 +0000 UTC Confirmed:2014-05-22 02:56:08 +0000 UTC Confirmations:119122 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:306541aa7848b9f909282eb5134d3aa3221feed8e8022b5bf7807cbb17a9191d OutputIndex:1 OutputValue:9007500 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:493046022100fedde3515293f587c4dd9358fe7471ceef091bc0c9dcbedf1894c7caadbcf3d3022100aefa4608d4fe76270c02a8491f170fe30011e29e7235739d73c51d4c06247da00141047b1d511b8559a2003ca88715bc8331f057fa4ebf11f411142509a8ffd2f2d36d5a5e4b6019d6eb3e16878f24fd8d55676050c28b4bc5e4c44f39245beedae100 Age:0 WalletName:}] Outputs:[{SpentBy: Value:101491 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869 Value:8896009 Addresses:[17astdTmG8zzVmry8mV8A7atAr3XefEgRX] ScriptType:pay-to-pubkey-hash Script:76a9144838f65fc4e06c644423ad0430de11ca5785dcd088ac DataHex: DataString:}]} {BlockHash:0000000000000000af64802c79f9b22e9091eb5548b4b662d5e444e61885923b BlockHeight:292586 Hash:b4735a0690dab16b8789fceaf81c511f3be484e319f684cc214380eaa2851030 Addresses:[18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa 1AAuRETEcHDqL4VM3R97aZHP8DSUHxpkFV 1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD 1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG] Total:3537488 Fees:20000 Size:438 Preference:medium RelayedBy: Received:2014-03-26 17:08:04 +0000 UTC Confirmed:2014-03-26 17:08:04 +0000 UTC Confirmations:128538 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:2 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:729f6469b59fea5da77457f3291e2623c2516e3e8e7afc782687c6d59f4c5e41 OutputIndex:0 OutputValue:3500000 Addresses:[1VxsEDjo6ZLMT99dpcLu4RQonMDVEQQTG] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100d06cdad1a54081e8499a4117f9f52d7fbc83c679dda7e3c22c08e964915b7354022010a2d6af1601d28d33a456dab2bccf3fbde35b2f3a9db82f72d675c90d015571014104672a00c8ee6fa23d68094dd98188ea1491848498554a10e13194851b614168b225b28b7f5a1c6ba98b5463438ef030c48b60533031ff2de84104e549d8d06ea9 Age:0 WalletName:} {PrevHash:45b74e542799343fc5ae70b715b7dd402da3a50fec766a9ebeb0879153db10ff OutputIndex:1 OutputValue:57488 Addresses:[1AAuRETEcHDqL4VM3R97aZHP8DSUHxpkFV] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:48304502203deee40f705127eb57de0bb1bb998376e11f09bbde663c614d9cf746948f9b740221008715b8df9fa6c33fad7bfa2bb72875c8e30002a61843f430b9f0af268c136e2a0141046b29997ba1e1974f88d4513257ed4021205a94cc20ef7c1c6c7b8510becb68085220b213b8864ebfb465b614a4e7719b29d81c9849aea37343962cda47945a16 Age:0 WalletName:}] Outputs:[{SpentBy: Value:3500000 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:3ebe4bb294beaed58aca834af5e8148248abd9c6c6b56d9c73c2f25eb6a4838f Value:37488 Addresses:[18KXZzuC3xvz6upUMQpsZzXrBwNPWZjdSa] ScriptType:pay-to-pubkey-hash Script:76a9145049e2ad94ed9c683b8f6ca67db33b979dd6d13a88ac DataHex: DataString:}]} {BlockHash:00000000000000001528e12ffb2bdfc46f739c864952d85258485511d3d0aba3 BlockHeight:292505 Hash:0416b8db5db4fa088437008aea7889e966e326f11c52c1da95161cd2ded95185 Addresses:[13UrVoMywxv9yaFBWvPfZ6kugWAZhLsnKo 1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD 1DUgQLCoJowWkcDCW13ZGSAELyr6jFG7AP 1NqW3Jk1w4RisMrBs53g9bK1Rnmi2zMcfC] Total:217176 Fees:20000 Size:438 Preference:medium RelayedBy: Received:2014-03-26 04:18:38 +0000 UTC Confirmed:2014-03-26 04:18:38 +0000 UTC Confirmations:128619 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:2 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:df959bf4b6faa51aedde7de2d23309c147a1637e89afd36cb8f3f35149943a75 OutputIndex:0 OutputValue:100000 Addresses:[1DUgQLCoJowWkcDCW13ZGSAELyr6jFG7AP] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:49304602210087cfe517c22a4001230cffadf62a0e59d286b76c343f7dfa69daf4be3e4f3562022100d15fe4dc85428850af24789c50c43e95e96d3ff27ee066823ba4dbffb4e8f02f014104c40f18fab1dcc621e78e65b02f20d0e7413e95b9de60bb6c9f32dc3bc0f2ddc9b67ba1cc2e2fafc0ee14cdcd55c97c31501b4c6dde91c37135c50ff7fbec9e5a Age:0 WalletName:} {PrevHash:6b9f0e8f326bab57d56dc455dc10b00fbd5167a00475549759882b554d7217f7 OutputIndex:1 OutputValue:137176 Addresses:[13UrVoMywxv9yaFBWvPfZ6kugWAZhLsnKo] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:473044022051a83d1b8bcec5093652e51eb2d51f87572a0181c65d12b6f374ca06769b70100220447a771160387584da21a22e8d3a51ff8e2fc03538af64b6d88d23b8ea28fa99014104ac5249d12d65eb0062a13d722420f881b06c732f1a820a9962035d9f87992114bf14208552a293c2bf669c969676dfef3f36b16fff30e3deb0fb6b432092691d Age:0 WalletName:}] Outputs:[{SpentBy: Value:100000 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:5c82a033cbfa9149836840036a392566a8c7b4dfcb0c2c4c0a20993f69b49340 Value:117176 Addresses:[1NqW3Jk1w4RisMrBs53g9bK1Rnmi2zMcfC] ScriptType:pay-to-pubkey-hash Script:76a914ef86d94a858cd446ba7f0cd78d32677ffcc436a988ac DataHex: DataString:}]} {BlockHash:0000000000000000651ba18b29a1a7543d8fb9f6240d57b9e740014f51053ebc BlockHeight:292455 Hash:995a50e05d197be88d4da74160b4bcd2c363ebb1a49f95e572667d580bc70aba Addresses:[1CzuqkzUg1ybSsVaR3NWrEoXLxYtXdG35V 1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] Total:299990000 Fees:10000 Size:226 Preference:medium RelayedBy: Received:2014-03-25 21:32:08 +0000 UTC Confirmed:2014-03-25 21:32:08 +0000 UTC Confirmations:128669 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:7c72a7f748ad3d407b977ba1004855ed14367e1a9c9a3fdb916a24302f075421 OutputIndex:0 OutputValue:300000000 Addresses:[1CzuqkzUg1ybSsVaR3NWrEoXLxYtXdG35V] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:4830450221009336f886851ea3026f8d8836fc3c1a8d9d9456075ac440259410803767a4995a02202eee3c52aafa034d8058a236eabc3feaf80be5d8b3011bab192ee7856cee9d1b0121033a148923bf45432ab6cdbcb198abb8d23be28c5aa2ff3c6405837a9a9e4ef031 Age:0 WalletName:}] Outputs:[{SpentBy: Value:500000 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:3743ba41fc279cda244ec1bb5a75fba1352968870b2957cd5508a7e5b64dc94f Value:299490000 Addresses:[1CzuqkzUg1ybSsVaR3NWrEoXLxYtXdG35V] ScriptType:pay-to-pubkey-hash Script:76a914839d33de1bbd7807a03bcadbacd135316351330788ac DataHex: DataString:}]} {BlockHash:00000000000000006caa894c02a7254475acaefd5adf50d97de97f85470620db BlockHeight:292325 Hash:0c83c8321537a7c79dc6214788944ba6cd5ea76f0594453b6251fcf1856f2e4b Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD 1HBB6wHbhHBmsNmoBzzKdq77LKQUcUhGp3] Total:2990000 Fees:10000 Size:259 Preference:medium RelayedBy: Received:2014-03-25 00:07:31 +0000 UTC Confirmed:2014-03-25 00:07:31 +0000 UTC Confirmations:128799 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:ed21bb99e2df321c3e28d9ec5e11010d86a5dcf02412e989ae9dbc0f5b4ff3ff OutputIndex:0 OutputValue:3000000 Addresses:[1HBB6wHbhHBmsNmoBzzKdq77LKQUcUhGp3] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:493046022100c1eec2a231f42f0034d7c8cc479e20967214a4e9fc6da0b95ccdcc9de3e8d2e8022100a0f80d26f71dd625f262d74c9adc74412273df4e34aaacf33c44550ba545641c0141040f60c5ea2ba9bf92637083449caa13be32ebdcc55e94ddb2d1923cf704019d80e56f48800b9e6cb9d10835f10dd4426aeac8f410877a3a1f8a94caad45187c0e Age:0 WalletName:}] Outputs:[{SpentBy: Value:171116 Addresses:[1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD] ScriptType:pay-to-pubkey-hash Script:76a9148629647bd642a2372d846a7660e210c8414f047c88ac DataHex: DataString:} {SpentBy:d035e58aa0a1f174ff2c2a0d8213f6e95102c3448e122ce4b48fcd72c9af4c41 Value:2818884 Addresses:[1HBB6wHbhHBmsNmoBzzKdq77LKQUcUhGp3] ScriptType:pay-to-pubkey-hash Script:76a914b16e9aec76e2dcca9a64b5615f9f334e2250c6df88ac DataHex: DataString:}]}] TXRefs:[] UnconfirmedTXRefs:[] HasMore:false}
```

```php
<?php
// Run on console:
// php -f .\sample\address-api\AddressFullEndpoint.php

$addressClient = new AddressClient($apiContext);
$fullAddress = $addressClient->getFullAddress('1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD');

{
  "address":"1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD",
  "total_received":4433416,
  "total_sent":0,
  "balance":4433416,
  "unconfirmed_balance":0,
  "final_balance":4433416,
  "n_tx":7,
  "unconfirmed_n_tx":0,
  "final_n_tx":7,
  "txs":[
    {
      "block_hash":"00000000000000006548ac8dc283c97e8165023dc1fdbbca2eaa75f0143f4a8c",
      "block_height":302013,
      "hash":"14b1052855bbf6561bc4db8aa501762e7cc1e86994dda9e782a6b73b1ce0dc1e",
      "addresses":[
        "17astdTmG8zzVmry8mV8A7atAr3XefEgRX",
        "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"
      ],
      "total":8835413,
      "fees":10000,
      "size":258,
      "preference":"medium",
      "relayed_by":"",
      "confirmed":"2014-05-22T03:46:25Z",
      "received":"2014-05-22T03:46:25Z",
      "ver":1,
      "lock_time":0,
      "double_spend":false,
      "vin_sz":1,
      "vout_sz":2,
      "confirmations":58588,
      "confidence":1,
      "inputs":[
        {
          "prev_hash":"4cff011ec53022f2ae47197d1a2fd4a6ac2a80139f4d0131c1fed625ed5dc869",
          "output_index":1,
          "script":"483045022035695e3b237733c70a56286eccd8df41b4d22cd103ed9b2df44010caa3bc71430221008f58461c937e8fe6cc6d37a9aaee3927762cce4565a4c386bbcd9d82915acfc50141047b1d511b8559a2003ca88715bc8331f057fa4ebf11f411142509a8ffd2f2d36d5a5e4b6019d6eb3e16878f24fd8d55676050c28b4bc5e4c44f39245beedae100",
          "output_value":8845413,
          "sequence":4294967295,
          "addresses":[
            "17astdTmG8zzVmry8mV8A7atAr3XefEgRX"
          ],
          "script_type":"pay-to-pubkey-hash"
        }
      ],
      "outputs":[
        {
          "value":20213,
          "script":"76a9148629647bd642a2372d846a7660e210c8414f047c88ac",
          "addresses":[
            "1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD"
          ],
          "script_type":"pay-to-pubkey-hash"
        },
        {
          "value":8815200,
          "script":"76a9144838f65fc4e06c644423ad0430de11ca5785dcd088ac",
          "spent_by":"582a50f3a756c3261f8f085185e5975a762e239e95a30bcf1a4f2e31e0f834ab",
          "addresses":[
            "17astdTmG8zzVmry8mV8A7atAr3XefEgRX"
          ],
          "script_type":"pay-to-pubkey-hash"
        }
      ]
    },
    ...
  ]
}
```

The Address Full Endpoint returns all information available about a particular address, including an array of complete [transactions](#tx) instead of just transaction inputs and outputs. Unfortunately, because of the amount of data returned, it is the slowest of the address endpoints, but it returns the most detailed data record.

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS/full | GET | [Address](#address)

Flag | Type | Effect
---- | ---- | ------
**before** | *integer* | Filters response to only include transactions below **before** height in the blockchain.
**after** | *integer* | Filters response to only include transactions above **after** height in the blockchain.
**limit** | *integer* | **limit** sets the minimum number of returned TXs; there can be less if there are less than **limit** TXs associated with this address, but there can also be more in the rare case of more TXs in the block at the bottom of your call. This ensures paging by block height never misses TXs. Defaults to 10, maximum is 50.
**txlimit** | *integer* | This filters the TXInputs/TXOutputs within the returned TXs to include a maximum of **txlimit** items.
**confirmations** | *integer* | If set, only returns the **balance** and TXs that have *at least* this number of **confirmations**.
**confidence** | *integer* | Filters response to only include TXs above **confidence** in percent; e.g., if this is set to 99, will only return TXs with 99% confidence or above (including all confirmed TXs). For more detail on confidence, check the [Confidence Factor](#confidence-factor) documentation.
**includeHex** | *bool* | If *true*, includes hex-encoded raw transaction for each TX; *false* by default.
**includeConfidence** | *bool* | If *true*, includes the **confidence** attribute (useful for unconfirmed transactions) within returned TXs. For more info about this figure, check the [Confidence Factor](#confidence-factor) documentation.
**omitWalletAddresses** | *bool* | If **omitWalletAddresses** is *true* and you're querying a [Wallet](#wallet) or [HDWallet](#hdwallet), the response will omit address information (useful to speed up the API call for larger wallets).

ADDRESS is a *string* representing the public address (or wallet/HD wallet name) you're interested in querying, for example:

`1DEP8i3QJCsomS4BSMY2RpU1upv62aGvhD`

The returned object contains information about the address, including its balance in satoshis, the number of transactions associated with it, and the corresponding full transaction records in descending order by block height---and if multiple transactions associated with this address exist within the same block, by descending block index (position in block).

<aside class="notice">
If your returned <a href="#address">Address</a> object includes the <b>hasMore</b> attribute, there are more transactions associated with the address than transfered through this endpoint. If this happens, note the block height of the last transaction in the array, and then you can use the <b>before</b> flag to page through results.
</aside>

## Generate Address Endpoint

```shell
curl -X POST https://api.blockcypher.com/v1/btc/test3/addrs

{
"private": "81ee75559d37cbe4b7cbbfb9931ab1ba32172c5cdfc3ac2d020259b4c1104198",
"public": "0231ff9ec76820cb36b69061f6ffb125db3793b4aced468a1261b0680e1ef4883a",
"address": "mvpW7fMSi1nbZhJJDySNS2PUau8ppnu4kY",
"wif": "cRwGhRjCuuNtPgLcoYd1CuAqjFXCV5YNCQ1LB8RsFCvu61VfSsgR"
}
```

```shell
# generate bech32 p2wpkh 
curl -X POST https://api.blockcypher.com/v1/btc/test3/addrs?bech32=true

{
  "private": "516c110f271f2b26ad8ed2d72c469d2dde0db5030241926b6b0388c602cf572c",
  "public": "023766804d9c34ead560be1d1f819127cb2636305ac9ba2f76c176efd2b3913a25",
  "address": "tb1q2u0dzlwf805qy62w099p06f8z5nxz5kk34a456",
  "wif": "cQJyYH6rjkV2W9jR9PjnJftBmGt93Yi4gpn494oEAiWvoUJoEpCs"
}
```

```javascript
$.post('https://api.blockcypher.com/v1/btc/test3/addrs')
  .then(function(d) {console.log(d)});
> {
> "private": "81ee75559d37cbe4b7cbbfb9931ab1ba32172c5cdfc3ac2d020259b4c1104198",
> "public": "0231ff9ec76820cb36b69061f6ffb125db3793b4aced468a1261b0680e1ef4883a",
> "address": "mvpW7fMSi1nbZhJJDySNS2PUau8ppnu4kY",
> "wif": "cRwGhRjCuuNtPgLcoYd1CuAqjFXCV5YNCQ1LB8RsFCvu61VfSsgR"
> }
```

```ruby
> block_cypher.address_generate
=> {"private"=>"39c8788ef9815d061a2a4528817b3c23730b1129e5ca60032138061f35483951",
 "public"=>"039dc801dd76a7fa03789e0fe8efdd7e0857e73fa4ae38a294a60002ef949e83c4",
 "address"=>"1FdQDEnHXibwMMMKaaNGrhrcujJNZyszUN",
 "wif"=>"KyA2uUUMZkiRcSTx3PE1pBeMFgyifpxswcJu3Q7fVwXXZmvh48JA"}
```

```python
# Please use a python library such as https://github.com/sbuss/bitmerchant to securely generate an address client-side
```

```go
package main

import (
	"fmt"

	"github.com/blockcypher/gobcy"
)

func main() {
	btc := gobcy.API{"YOURTOKEN", "btc", "main"}
	addrKeys, err := btc.GenAddrKeychain()
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", addrKeys)
}

//Result from `go run`:
//{Address:18P5PFbuevRn4G3X27LF9ZEcfW3pKgy1Yp Private:b833a204a295c26dd19e49a0cc9fbb2f6475a886b7dc5b47b78261aa0ce07d94 Public:03685bbf3756a89240e7fa2b1312c48dc7b0c455116b9cc3e463b53643716396d5 Wif:L3Pmu1ZnRMUWmDoiXecA4m1gPhVwFMgm7CirQxWxxb437nQhESbT PubKeys:[] ScriptType:}
```

```php
<?php
// Run on console:
// php -f .\sample\address-api\GenerateAddressEndpoint.php

$addressClient = new AddressClient($apiContext);
$addressKeyChain = $addressClient->generateAddress();

{
  "private":"3f3cea5a7373011d6f51844bf986abe6950d7a30eaaab247fc951c3ea9f13705",
  "public":"02d1c8ccc7131a3c39b9e7001741f68d339e5d6941caf31fe9f43b15d6cf09dcb9",
  "address":"1rA7AB93qziWzHfTFXn5n3GYJ1mhkG8tn",
  "wif":"KyLdumvGqB86v3D9sKqAMGhNXB1UtYSsxb9deH1nxpooskVF7Rgz"
}
```

The Generate Address endpoint allows you to generate private-public key-pairs along with an associated public address. No information is required with this POST request.

<aside class="success">
The private key returned is immediately discarded by our servers, but we advise that these keys should not be used for any high-value---or long-term storage---addresses.
</aside>

<aside class="warning">
Always use HTTPS for Address Generation requests. Otherwise, your generated private keys will be sent over insecure channels and could be MITM'd.
</aside>

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/addrs | POST | *nil* | [AddressKeychain](#addresskeychain)

Flag | Type | Effect
---- | ---- | ------
**bech32** | *bool* | Whether or not to generate a p2wpkh bech32 address. Default is false.

The returned object contains a private key in hex-encoded and wif-encoded format, a public key, and a public address.

## Generate Multisig Address Endpoint

```shell
curl -d '{"pubkeys": ["02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3", "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea", "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"], "script_type": "multisig-2-of-3"}' https://api.blockcypher.com/v1/btc/main/addrs

{
"private": "",
"public": "",
"address": "3BF1M1PnTge94QewuWh3B8mRVw8U4SVnb4",
"wif": "",
"pubkeys": [
	"02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3",
	"033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea",
	"022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"
],
"script_type": "multisig-2-of-3"
}
```

```javascript
var data = {
  "pubkeys": [
    "02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3",
    "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea",
    "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"
  ],
  "script_type": "multisig-2-of-3"
};
$.post('https://api.blockcypher.com/v1/btc/test3/addrs', JSON.stringify(data))
  .then(function(d) {console.log(d)});
> {
> "private": "",
> "public": "",
> "address": "3BF1M1PnTge94QewuWh3B8mRVw8U4SVnb4",
> "wif": "",
> "pubkeys": [
> 	"02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3",
> 	"033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea",
> 	"022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"
> ],
> "script_type": "multisig-2-of-3"
> }
```

```ruby
> block_cypher.address_generate_multi(["02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3", "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea", "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"], "multisig-2-of-3")
=> {"private"=>"",
 "public"=>"",
 "address"=>"3BF1M1PnTge94QewuWh3B8mRVw8U4SVnb4",
 "wif"=>"",
 "pubkeys"=>
  ["02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3",
   "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea",
   "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"],
 "script_type"=>"multisig-2-of-3"}
```

```python
>>> from blockcypher import generate_multisig_address
>>> pubkey_list = ['02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3', '033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea', '022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca']
>>> generate_multisig_address(pubkey_list=pubkey_list, script_type='multisig-2-of-3')
{
    "address": "3BF1M1PnTge94QewuWh3B8mRVw8U4SVnb4", 
    "private": "", 
    "pubkeys": [
        "02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3", 
        "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea", 
        "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"
    ], 
    "public": "", 
    "script_type": "multisig-2-of-3", 
    "wif": ""
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
	addrKeys, err := btc.GenAddrMultisig(gobcy.AddrKeychain{PubKeys: []string{"02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3", "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea", "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"}, ScriptType: "multisig-2-of-3"})
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", addrKeys)
}

//Result from `go run`:
//{Address:3BF1M1PnTge94QewuWh3B8mRVw8U4SVnb4 Private: Public: Wif: PubKeys:[02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3 033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea 022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca] ScriptType:multisig-2-of-3}
```

```php
<?php
// Run on console:
// php -f .\sample\address-api\GenerateMultisigAddressEndpoint.php

$addressClient = new AddressClient($apiContext);
$pubkeys = array(
    "02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3",
    "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea",
    "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"
);
$addressKeyChain = $addressClient->generateMultisigAddress($pubkeys, 'multisig-2-of-3');

{
  "pubkeys":[
    "02c716d071a76cbf0d29c29cacfec76e0ef8116b37389fb7a3e76d6d32cf59f4d3",
    "033ef4d5165637d99b673bcdbb7ead359cee6afd7aaf78d3da9d2392ee4102c8ea",
    "022b8934cc41e76cb4286b9f3ed57e2d27798395b04dd23711981a77dc216df8ca"
  ],
  "script_type":"multisig-2-of-3",
  "private":"",
  "public":"",
  "address":"3BF1M1PnTge94QewuWh3B8mRVw8U4SVnb4",
  "wif":""
}
```

The Generate Multisig Address Endpoint is a convenience method to help you generate multisig addresses from multiple public keys. After supplying a partially filled-out [AddressKeychain](#addresskeychain) object (including only an array of hex-encoded public keys and the script type), the returned object includes the computed public address.

<aside class="notice">
This endpoint is the same resource as the <a href="#generateadressendpoint">Generate Address Endpoint</a>, but with data in the request body.
</aside>

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/addrs | POST | [AddressKeychain](#addresskeychain) | [AddressKeychain](#addresskeychain)

