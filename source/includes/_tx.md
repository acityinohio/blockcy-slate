# Transaction API

BlockCypher's Transaction API allows you to look up information about unconfirmed transactions, query transactions based on hash, create and propagate your own transactions, including multisignature transactions, and embed data on the blockchain---all [based on the coin/chain resource](#restful-resources) you've selected for your endpoints.

If you're new to blockchains, the idea of transactions is relatively self-explanatory. Here's what's going on underneath the hood: a transaction takes previous "unspent transaction outputs" (also known as [UTXOs](https://bitcoin.org/en/glossary/unspent-transaction-output)) as "transaction inputs" and creates new "locking scripts" on those inputs such that they are "sent" to new addresses (to become new UTXOs). While most of these public addresses are reference points for single private keys that can "unlock" the newly created UTXOs, occasionally they are sent to more exotic addresses through *pay-to-script-hash*, typically multisignature addresses.

Generally speaking, UTXOs are generated from previous transactions (except for [Coinbase inputs](https://bitcoin.org/en/glossary/coinbase)).

<aside class="success">
Even if you don't use the API to assist in creating transactions, we highly recommend using BlockCypher to push your raw transactions. BlockCypher maintains connections to 20% of all nodes on all blockchains it supports, which means we can propagate transactions faster than almost anyone else. Given our speed and reliability, this is especially powerful when used in concert with our <a href="#confidence-factor">Confidence Factor.</a>
</aside>

## Transaction Hash Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/txs/f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449

{
"block_hash": "0000000000000000c504bdea36e5...",
"block_height": 293000,
"hash": "f854aebae95150b379cc1187d848d58225f...",
"addresses": [
  "13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy",
  "19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L",
  "1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy",
  "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq",
  "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
],
"total": 70320221545,
"fees": 0,
"size": 636,
"preference": "low",
"relayed_by": "",
"confirmed": "2014-03-29T01:29:19Z",
"received": "2014-03-29T01:29:19Z",
"ver": 1,
"lock_time": 0,
"double_spend": false,
"vin_sz": 4,
"vout_sz": 1,
"confirmations": 64373,
"inputs": [
  {
  "prev_hash": "583910b7bf90ab802e22e5c25a89b59...",
  "output_index": 1,
  "script": "4830450220504b1ccfddf508422bdd8b0f...",
  "output_value": 16450000,
  "sequence": 4294967295,
  "addresses": [
    "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq"
  ],
  "script_type": "pay-to-pubkey-hash"
  },
  ...
],
"outputs": [
  {
  "value": 70320221545,
  "script": "76a914e6aad9d712c419ea8febf009a3f3...",
  "spent_by": "35832d6c70b98b54e9a53ab2d51176eb...",
  "addresses": [
    "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
  ],
  "script_type": "pay-to-pubkey-hash"
  }
]
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/txs/f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449').then(function(d) {console.log(d)});
> {
> "block_hash": "0000000000000000c504bdea36e5...",
> "block_height": 293000,
> "hash": "f854aebae95150b379cc1187d848d58225f...",
> "addresses": [
>   "13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy",
>   "19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L",
>   "1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy",
>   "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq",
>   "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
> ],
> "total": 70320221545,
> "fees": 0,
> "size": 636,
> "preference": "low",
> "relayed_by": "",
> "confirmed": "2014-03-29T01:29:19Z",
> "received": "2014-03-29T01:29:19Z",
> "ver": 1,
> "lock_time": 0,
> "double_spend": false,
> "vin_sz": 4,
> "vout_sz": 1,
> "confirmations": 64373,
> "inputs": [
>   {
>   "prev_hash": "583910b7bf90ab802e22e5c25a89b59...",
>   "output_index": 1,
>   "script": "4830450220504b1ccfddf508422bdd8b0f...",
>   "output_value": 16450000,
>   "sequence": 4294967295,
>   "addresses": [
>     "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq"
>   ],
>   "script_type": "pay-to-pubkey-hash"
>   },
>   ...
> ],
> "outputs": [
>   {
>   "value": 70320221545,
>   "script": "76a914e6aad9d712c419ea8febf009a3f3...",
>   "spent_by": "35832d6c70b98b54e9a53ab2d51176eb...",
>   "addresses": [
>     "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
>   ],
>   "script_type": "pay-to-pubkey-hash"
>   }
> ]
> }
```

```ruby
> block_cypher.blockchain_transaction("f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449")
=> {"block_hash"=>"0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328",
 "block_height"=>293000,
 "hash"=>"f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449",
 "addresses"=>
  ["13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy",
   "19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L",
   "1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy",
   "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq",
   "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"],
 "total"=>70320221545,
 "fees"=>0,
 "size"=>636,
 "preference"=>"low",
 "relayed_by"=>"",
 "confirmed"=>"2014-03-29T01:29:19Z",
 "received"=>"2014-03-29T01:29:19Z",
 "ver"=>1,
 "lock_time"=>0,
 "double_spend"=>false,
 "vin_sz"=>4,
 "vout_sz"=>1,
 "confirmations"=>68362,
 "inputs"=>
  [{"prev_hash"=>"583910b7bf90ab802e22e5c25a89b59862b20c8c1aeb24dfb94e7a508a70f121",
    "output_index"=>1,
    "script"=>
     "4830450220504b1ccfddf508422bdd8b0fcda2b148...",
    "output_value"=>16450000,
    "sequence"=>4294967295,
    "addresses"=>["1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq"],
    "script_type"=>"pay-to-pubkey-hash"},...],
 "outputs"=>
  [{"value"=>70320221545,
    "script"=>"76a914e6aad9d712c419ea8febf009a3f3bfdd8d222fac88ac",
    "spent_by"=>"35832d6c70b98b54e9a53ab2d51176eb19ad11bc4505d6bb1ea6c51a68cb92ee",
    "addresses"=>["1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"],
    "script_type"=>"pay-to-pubkey-hash"}]}
```

```python
>>> from blockcypher import get_transaction_details
>>> get_transaction_details('f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449')
{
    "addresses": [
        "13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy",
        "19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L",
        "1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy",
        "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq",
        "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
    ],
    "block_hash": "0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328",
    "block_height": 293000,
    "confirmations": 86918,
    "confirmed": "datetime.datetime(2014, 3, 29, 1, 29, 19, 0, tzinfo=tzutc())",
    "double_spend": False,
    "fees": 0,
    "hash": "f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449",
    "inputs": [
        {
            "addresses": [
                "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq"
            ],
            "output_index": 1,
            "output_value": 16450000,
            "prev_hash": "583910b7bf90ab802e22e5c25a89b59862b20c8c1aeb24dfb94e7a508a70f121",
            "script": "4830450220504b1ccfddf508422bdd8b0fcda2b1483e87aee1b486c0130bc29226bbce3b4e022100b5befcfcf0d3bf6ebf0ac2f93badb19e3042c7bed456c398e743b885e782466c012103b1feb40b99e8ff18469484a50e8b52cc478d5f4f773a341fbd920a4ceaedd4bf",
            "script_type": "pay-to-pubkey-hash",
            "sequence": 4294967295
        },
        ...,

    ],
    "lock_time": 0,
    "outputs": [
        {
            "addresses": [
                "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
            ],
            "script": "76a914e6aad9d712c419ea8febf009a3f3bfdd8d222fac88ac",
            "script_type": "pay-to-pubkey-hash",
            "spent_by": "35832d6c70b98b54e9a53ab2d51176eb19ad11bc4505d6bb1ea6c51a68cb92ee",
            "value": 70320221545
        }
    ],
    "preference": "low",
    "received": "datetime.datetime(2014, 3, 29, 1, 29, 19, 0, tzinfo=tzutc())",
    "relayed_by": "",
    "size": 636,
    "total": 70320221545,
    "ver": 1,
    "vin_sz": 4,
    "vout_sz": 1
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
  tx, err := btc.GetTX("f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449", nil)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", tx)
}

//Result from `go run`:
//{BlockHash:0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328 BlockHeight:293000 Hash:f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449 Addresses:[13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy 19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L 1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy 1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq 1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV] Total:{neg:false abs:[70320221545]} Fees:{neg:false abs:[]} Size:636 Preference:low RelayedBy: Received:2014-03-29 01:29:19 +0000 UTC Confirmed:2014-03-29 01:29:19 +0000 UTC Confirmations:355807 Confidence:1 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:4 VoutSize:1 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:583910b7bf90ab802e22e5c25a89b59862b20c8c1aeb24dfb94e7a508a70f121 OutputIndex:1 OutputValue:16450000 Addresses:[1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:4830450220504b1ccfddf508422bdd8b0fcda2b1483e87aee1b486c0130bc29226bbce3b4e022100b5befcfcf0d3bf6ebf0ac2f93badb19e3042c7bed456c398e743b885e782466c012103b1feb40b99e8ff18469484a50e8b52cc478d5f4f773a341fbd920a4ceaedd4bf Age:292997 WalletName:} {PrevHash:f6966bb3d3ba0eb97fd11b223fb13c793c0b4a1b3deb575e8ae38d666c1d00d9 OutputIndex:36 OutputValue:10061545 Addresses:[19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:48304502210086de855e03008abcc49335c775973eab9ace2e16c3bfe6536218c1d029287fdb0220129ced657870af63f61cdd4b941996f9a243d1f306e774fc9c5f3dea0af8d581012103cbe40d1d790799a6494c07f844eaf05b4c6deab0b9dee2ee45c8decead12c5cd Age:292998 WalletName:} {PrevHash:9ea2f9695e6694309fb0d8b966afb4678a77a59f160822e33819fada9e0c8288 OutputIndex:1 OutputValue:70000000000 Addresses:[1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:48304502201f1eb5b79279258a91c00dee09dff5d6f6ece7c01639e66a6bdd579136ecddee022100d4a9ed93183bf338e51ba80bc1dd10ff03e9e159bd8ea59db3a5c10aa0ccd3400121022667ee37e180c1ad2fef6f16aa52ed27799f629364dfe51e144dd683317dbbd2 Age:292998 WalletName:} {PrevHash:279ccbbab8605390a85fe6f0e4fb04ec1946ee6033054b16fec72e1304742d5d OutputIndex:0 OutputValue:293710000 Addresses:[13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100baac0c25867855f62592872cfac522d59fddd590a6cc290c8ad3bbe6b1151b5802204f2713c565ce6b00e5ea00e955d35e3b0878af5474feda35ebbb73232122d5480121023ed3b44ad598e3834e561efed205c221b7bc2e577e752eeaa66e85e60d0381c9 Age:292998 WalletName:}] Outputs:[{SpentBy:35832d6c70b98b54e9a53ab2d51176eb19ad11bc4505d6bb1ea6c51a68cb92ee Value:{neg:false abs:[70320221545]} Addresses:[1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV] ScriptType:pay-to-pubkey-hash Script:76a914e6aad9d712c419ea8febf009a3f3bfdd8d222fac88ac DataHex: DataString:}]}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\TransactionHashEndpoint.php

$txClient = new TXClient($apiContext);
$transaction = $txClient->get('f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449');

{
"block_hash": "0000000000000000c504bdea36e531d80...",
"block_height": 293000,
"hash": "f854aebae95150b379cc1187d848d58225f3c41...",
"addresses": [
  "13XXaBufpMvqRqLkyDty1AXqueZHVe6iyy",
  "19YtzZdcfs1V2ZCgyRWo8i2wLT8ND1Tu4L",
  "1BNiazBzCxJacAKo2yL83Wq1VJ18AYzNHy",
  "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq",
  "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
],
"total": 70320221545,
"fees": 0,
"size": 636,
"preference": "low",
"relayed_by": "",
"confirmed": "2014-03-29T01:29:19Z",
"received": "2014-03-29T01:29:19Z",
"ver": 1,
"lock_time": 0,
"double_spend": false,
"vin_sz": 4,
"vout_sz": 1,
"confirmations": 63171,
"inputs": [
  {
    "prev_hash": "583910b7bf90ab802e22e5c25a89...",
    "output_index": 1,
    "script": "4830450220504b1ccfddf508422bdd8...",
    "output_value": 16450000,
    "sequence": 4294967295,
    "addresses": [
      "1GbMfYui17L5m6sAy3L3WXAtf1P32bxJXq"
    ],
    "script_type": "pay-to-pubkey-hash"
  },
  ...,
  ...,
],
"outputs": [
  {
    "value": 70320221545,
    "script": "76a914e6aad9d712c419ea8febf009a...",
    "spent_by": "35832d6c70b98b54e9a53ab2d5117...",
    "addresses": [
      "1N2f642sbgCMbNtXFajz9XDACDFnFzdXzV"
    ],
    "script_type": "pay-to-pubkey-hash"
  }
]
}
```

The Transaction Hash Endpoint returns detailed information about a given transaction based on its hash.

Resource | Method | Return Object
-------- | ------ | -------------
/txs/$TXHASH | GET | [TX](#tx)

Flag | Type | Effect
---- | ---- | ------
**limit** | *integer* | Filters TXInputs/TXOutputs, if unset, default is 20.
**instart** | *integer* | Filters TX to only include TXInputs from this input index and above.
**outstart** | *integer* | Filters TX to only include TXOutputs from this output index and above.
**legacyaddrs** | *bool* | **Litecoin Only.** Replaces P2SH prefix with legacy 3 instead of M. Disabled by default.
**includeHex** | *bool* | If *true*, includes hex-encoded raw transaction; *false* by default.
**includeConfidence** | *bool* | If *true*, includes the **confidence** attribute (useful for unconfirmed transactions). For more info about this figure, check the [Confidence Factor](#confidence-factor) documentation.

TXHASH is a *string* representing the hex-encoded transaction hash you're interested in querying, for example:

`f854aebae95150b379cc1187d848d58225f3c4157fe992bcd166f58bd5063449`

The returned object contains detailed information about the transaction, including the value transfered, date received, and a full listing of inputs and outputs.

<aside class="warning">
Please note that, <b>for Litecoin only</b>, since July 1 2020:
<ul>
<li><code>maddrs</code> flag no longer exist. Use <code>legacyaddrs</code> to display legacy 3 prefix.</li>
<li>By default, P2SH addresses have a M prefix instead of the legacy 3.</li>
</ul>
</aside>

## Unconfirmed Transactions Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/txs

[
  {
  "block_height": -1,
  "hash": "7f3af38d988e479f651520b13b85...",
  "addresses": [
    "19ZZonTmBD3VBhgFSWrtNis6Wo4aNRyjAV",
    "1GD2Gnt3Vk51FpUhZWrFpSa24KE2ZGPaWG",
    "1HEJiW8jxvUvVQ5cuButRmFj6ZzKzM9Fp6"
  ],
  "total": 46977000,
  "fees": 10000,
  "size": 225,
  "preference": "medium",
  "relayed_by": "54.209.56.58:8333",
  ...
  },
  {
  "block_height": -1,
  "hash": "7e2d8b4e95c8246e81f297539409...",
  "addresses": [
    "12u8EFJR6QJpNYbuQcYt2iMsAtPdnCFwQb",
    "1MUbYoqSadRSEipwcmKnH37NaKakDCmgwW"
  ],
  "total": 1070000,
  "fees": 10000,
  "size": 225,
  "preference": "medium",
  "relayed_by": "52.1.229.213:8333",
  "received": "2015-05-21T05:44:13.878Z",
  ...
  },
...
]
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/txs').then(function(d) {console.log(d)});
> [
>   {
>   "block_height": -1,
>   "hash": "7f3af38d988e479f651520b13b85...",
>   "addresses": [
>     "19ZZonTmBD3VBhgFSWrtNis6Wo4aNRyjAV",
>     "1GD2Gnt3Vk51FpUhZWrFpSa24KE2ZGPaWG",
>     "1HEJiW8jxvUvVQ5cuButRmFj6ZzKzM9Fp6"
>   ],
>   "total": 46977000,
>   "fees": 10000,
>   "size": 225,
>   "preference": "medium",
>   "relayed_by": "54.209.56.58:8333",
>   ...
>   },
>   {
>   "block_height": -1,
>   "hash": "7e2d8b4e95c8246e81f297539409...",
>   "addresses": [
>     "12u8EFJR6QJpNYbuQcYt2iMsAtPdnCFwQb",
>     "1MUbYoqSadRSEipwcmKnH37NaKakDCmgwW"
>   ],
>   "total": 1070000,
>   "fees": 10000,
>   "size": 225,
>   "preference": "medium",
>   "relayed_by": "52.1.229.213:8333",
>   "received": "2015-05-21T05:44:13.878Z",
>   ...
>   },
> ...
> ]
```

```ruby
> block_cypher.blockchain_unconfirmed_tx
=> [{"block_height"=>-1,
  "hash"=>"125abd75941fb8fd499d514979a764b9bf4bf0b0208dcff2790a6b4d7c165911",
  "addresses"=>
   ["137REkr34fuwZY6Fu5Numz7DDNt14y4eNr",
    "1LoveUTs984niMqTqTZyDoRUuqpZV5VQ1K",
    "1PSGAQAHS5fE745a4pwD7NSxJ3Gt5e1vSU",
    "3FaskXUYNSdeczUt1e9c6eGnWj1ifEwm94"],
  "total"=>11357528,
  "fees"=>10000,
  "size"=>699,
  "preference"=>"low",
  "relayed_by"=>"198.27.97.172:8333",
  "received"=>"2015-06-17T20:28:04.298Z",
  "ver"=>1,
  "lock_time"=>0,
  "double_spend"=>false,...},
  {"block_height"=>-1,
   "hash"=>"125abd7...",
   ...},
  ...]
```

```python
>>> from blockcypher import get_broadcast_transactions
>>> get_broadcast_transactions(limit=1)
[
    {
        "addresses": [
            "1CM6sSAT6jyGmkvx7zoEGs7cM8EvG39Cah",
            "1GKfznPeMp7Ey1MYfCS3CyrZZV2sRdBGWs"
        ],
        "block_height": -1,
        "confirmations": 0,
        "double_spend": False,
        "fees": 90000,
        "hash": "915b22630b74f6dd12ef723ab0e0f99931faa1c343d488118aa528542e99afb7",
        "inputs": [
            {
                "addresses": [
                    "1CM6sSAT6jyGmkvx7zoEGs7cM8EvG39Cah"
                ],
                "age": 6802,
                "output_index": 1,
                "output_value": 599559,
                "prev_hash": "825f6f06241ef6e3bb94c7c39126f79e0a9af725fd185f28ff7f0c4d4fb1144c",
                "script": "483045022100d549d7b6033081844b7b854055c76d87809ca9ba13ae5179310b3bcffaef2495022008f61a6b4f6a905ed84b05dfb9e21840d547cd409b8a4e713477444e79593792012103c95e6dd62dd842058568d31011d8a425b5209a6758ce45b210af095e78c158e2",
                "script_type": "pay-to-pubkey-hash",
                "sequence": 4294967295
            }
        ],
        "lock_time": 0,
        "next_inputs": "https://api.blockcypher.com/v1/btc/main/txs/915b22630b74f6dd12ef723ab0e0f99931faa1c343d488118aa528542e99afb7?instart=1&outstart=0&limit=1",
        "next_outputs": "https://api.blockcypher.com/v1/btc/main/txs/915b22630b74f6dd12ef723ab0e0f99931faa1c343d488118aa528542e99afb7?instart=0&outstart=1&limit=1",
        "outputs": [
            {
                "addresses": [
                    "1GKfznPeMp7Ey1MYfCS3CyrZZV2sRdBGWs"
                ],
                "script": "76a914a811ed2f211a447ba18eb4f7f849a86c201427f388ac",
                "script_type": "pay-to-pubkey-hash",
                "value": 1000000
            }
        ],
        "preference": "low",
        "receive_count": 569,
        "received": "datetime.datetime(2015, 10, 21, 19, 6, 25, 725000, tzinfo=tzutc())",
        "relayed_by": "95.211.104.246:8333",
        "size": 5837,
        "total": 1117192,
        "ver": 1,
        "vin_sz": 39,
        "vout_sz": 2
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
  txs, err := btc.GetUnTX()
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", txs)
}

//Result from `go run`:
//[{BlockHash: BlockHeight:-1 Hash:1cdfbc1d0261beb2fb90e5d487cc5958ca5e558303538e43e099a5a49950a4e7 Addresses:[3JMz7MeHFPsZDDk5bi73skPEFj1U5D1Qtb 3HEhppcPZd63eF2NsznskYTSG2qDPERNN3 3AtUpLCxBYEr4KAbf1KrhnRtifswKrNjGt 3C4Awf7Krjd2SQvVNLcH35iki8fWcPU74E 3NJkrHZBtAUyRCBM5fzoEQXDrtQSymUzcU 361efkmBbGnB6EhqEQ3ydfrY3NAuzyPxJy 3HbvuXMFJbMxcmuiBTHLdoWYrKcYkNRbni 37t2bpQEjJbz5Qtj4p9hhyVhJkwCpZckVR 3L2xdqCFUCTdAY2SbkH9kAnidb5dCta6cT] Total:{neg:false abs:[13272144]} Fees:{neg:false abs:[20048]} Size:522 Preference:low RelayedBy:51.159.56.35:20008 Received:2020-09-17 21:15:07.209035123 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:2 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:7 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:945d4c531def721fe6a10dfeeec073217987eff1f0cc93ada280be546cb6bfa9 OutputIndex:14 OutputValue:445112 Addresses:[3HEhppcPZd63eF2NsznskYTSG2qDPERNN3] Sequence:4294967295 ScriptType:pay-to-script-hash Script:1600140cc50ea32e3887ae8af49363555dc0303e0c475f Age:648800 WalletName:} {PrevHash:386512a1766070684f15e408a6c35e5773a2c2149fe2db496ef531144df9fce5 OutputIndex:0 OutputValue:447796 Addresses:[3HbvuXMFJbMxcmuiBTHLdoWYrKcYkNRbni] Sequence:4294967295 ScriptType:pay-to-script-hash Script:16001469616b09680ed79ab2806203eead38f1b9540bdb Age:648801 WalletName:} {PrevHash:a50195f38583a38e5baba453a7fe93ac4e66bbf30073144baf1a7398f55ef97a OutputIndex:0 OutputValue:450000 Addresses:[37t2bpQEjJbz5Qtj4p9hhyVhJkwCpZckVR] Sequence:4294967295 ScriptType:pay-to-script-hash Script:16001451991c92d4f9a57887114857c30a91d8b4e30e67 Age:648796 WalletName:} {PrevHash:e3fd7fa009269f1684fa9ae37e836a499210e6cc05e9ee82fb8729fd85f0d497 OutputIndex:1 OutputValue:10603691 Addresses:[3AtUpLCxBYEr4KAbf1KrhnRtifswKrNjGt] Sequence:4294967295 ScriptType:pay-to-script-hash Script:160014e8eb291d158e1eaca3bd7739cdbc262638d983a5 Age:638194 WalletName:} {PrevHash:ce649fa0baff4d4f48d597e99575d1c60495346cc07051d98110fb14755a53f1 OutputIndex:1 OutputValue:445764 Addresses:[3L2xdqCFUCTdAY2SbkH9kAnidb5dCta6cT] Sequence:4294967295 ScriptType:pay-to-script-hash Script:160014ead1e6d8cac4516ec0b5371ce5f260a765e54978 Age:648798 WalletName:} {PrevHash:671bf165b807746432062ac261f94f6e5004eb7cdb58fe333aaf974a65b9a652 OutputIndex:2 OutputValue:449829 Addresses:[3NJkrHZBtAUyRCBM5fzoEQXDrtQSymUzcU] Sequence:4294967295 ScriptType:pay-to-script-hash Script:160014349e80eabeca8db9338e1d966df76cebfd5000ba Age:648793 WalletName:} {PrevHash:e50b8a52dfc22a0c9557a2b4f88eeb6e6b8a3489c1e697cba9ccfd10f3eaf9fd OutputIndex:0 OutputValue:450000 Addresses:[361efkmBbGnB6EhqEQ3ydfrY3NAuzyPxJy] Sequence:4294967295 ScriptType:pay-to-script-hash Script:1600145e95efc41142e30df37e2cf7511577afb7ad6137 Age:648799 WalletName:}] Outputs:[{SpentBy: Value:{neg:false abs:[12268612]} Addresses:[3C4Awf7Krjd2SQvVNLcH35iki8fWcPU74E] ScriptType:pay-to-script-hash Script:a91471b1859ab3202abaf9be149b5a14e6b2dd9d379987 DataHex: DataString:} {SpentBy: Value:{neg:false abs:[1003532]} Addresses:[3JMz7MeHFPsZDDk5bi73skPEFj1U5D1Qtb] ScriptType:pay-to-script-hash Script:a914b6e0b89ddb315d44c0b38eadabe537369e44230d87 DataHex: DataString:}]} {BlockHash: BlockHeight:-1 Hash:05d1a6bb4301fbb7afd888b011e06892f524744a4c0ef122f7fe1cea83083a1d Addresses:[1DEzbyPer6B2VtT7gFn5jKe1MFAwUnWF41 1EMa5CGu48RVvR7LiNsWhTPk9YHj1LeG2n] Total:{neg:false abs:[3948638]} Fees:{neg:false abs:[1362]} Size:226 Preference:low RelayedBy:162.252.84.26:8333 Received:2020-09-17 21:15:07.618 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:802cb7d2a2c096a143d2c74515b871175466d46d17562e2bb95da3de3a988757 OutputIndex:1 OutputValue:3950000 Addresses:[1EMa5CGu48RVvR7LiNsWhTPk9YHj1LeG2n] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100961d30d541897b609d4284c3d98cdacef9c884dc3767196ffca17d2173e728e1022070ffdd7ad49d8fb78482adc37b295cce91358b31876fe5f68cde82eb751176540121022bbb89a2225a3906b5a11286ee33fbe516e93782384468810b1497d38f0fac8f Age:0 WalletName:}] Outputs:[{SpentBy: Value:{neg:false abs:[91265]} Addresses:[1DEzbyPer6B2VtT7gFn5jKe1MFAwUnWF41] ScriptType:pay-to-pubkey-hash Script:76a9148647005ec8ddcbc22c63d4436a39822bca3a8d0288ac DataHex: DataString:} {SpentBy: Value:{neg:false abs:[3857373]} Addresses:[1EMa5CGu48RVvR7LiNsWhTPk9YHj1LeG2n] ScriptType:pay-to-pubkey-hash Script:76a914927d8fd26fbac4aa54b325cfba95c0b98fa7e9bb88ac DataHex: DataString:}]} {BlockHash: BlockHeight:-1 Hash:7bb3c3ed3572f78aaf773792ceb34199791a2e334124f0b6f32a66b62547db63 Addresses:[1Cmc8Pj2gmYwZkVyBGdnVFMQ3iHg9nasRv 1KtaKKnWgNqVD3AkVWNhiX7pMDPvhguco3 1N1uvydMyQVR8k5jSUdru4rRSqfYKSw7dg] Total:{neg:false abs:[4607112]} Fees:{neg:false abs:[3762]} Size:340 Preference:low RelayedBy:162.252.84.26:8333 Received:2020-09-17 21:15:07.422 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:2 VoutSize:1 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:9b92f21a6bb911dfca56aa0b6ef4cc74611cc21195c5d3145d471410e91a02cf OutputIndex:0 OutputValue:10292 Addresses:[1KtaKKnWgNqVD3AkVWNhiX7pMDPvhguco3] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100910a2f20388a1cf86cf053a19b95d03887003310110b5e36fccbf4da221cbd220220512c6e86f572e0c9ecf024a71de22449c70b18b9c0dd39be156cf5c7aad8a39c012103f2f96148e0e5187440003f1883f958eb66582ba28a49ae4ce7409beb90ab6928 Age:648764 WalletName:} {PrevHash:9b92f21a6bb911dfca56aa0b6ef4cc74611cc21195c5d3145d471410e91a02cf OutputIndex:1 OutputValue:4600582 Addresses:[1Cmc8Pj2gmYwZkVyBGdnVFMQ3iHg9nasRv] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100be8edaa188b19dc92ad2ae9bb33d1a57e7adbdda25495b068d42049cdbdafd0e02201123ad42f21db68d71700f83651a66582a9168b247c64ef9d9d350156d004e16012102c2f81c194a0469091784dfbaaa8d5c6fcb78accac7621dd2acaada744625276a Age:648764 WalletName:}] Outputs:[{SpentBy: Value:{neg:false abs:[4607112]} Addresses:[1N1uvydMyQVR8k5jSUdru4rRSqfYKSw7dg] ScriptType:pay-to-pubkey-hash Script:76a914e686d36274d0a3d97675703eb4d912c48b4b86ee88ac DataHex: DataString:}]} {BlockHash: BlockHeight:-1 Hash:1f2befc56c12871b8a4cccdece5f4e675e93f1252ed79331683886be4369a5d3 Addresses:[1LCtCWHAGJyK49AZP57Doi1E6bLwTXkorF 1MxBr1aN3Pxn2cgBJToDThPSuNrkKufk2P 1UxrZDFKTiFEezRSPZAT3SsJSK2SPsQAZ 39XftCh58MeBg5qGj49vx4EafvS9AE4uTd] Total:{neg:false abs:[1800536]} Fees:{neg:false abs:[1480]} Size:370 Preference:low RelayedBy:207.180.242.103:8333 Received:2020-09-17 21:15:07.179 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:2 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:26db4e0f1492cc689d27fd29f190a38c3a08396e6a184be09ad8178fd18cf190 OutputIndex:0 OutputValue:1796889 Addresses:[1MxBr1aN3Pxn2cgBJToDThPSuNrkKufk2P] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:4730440220162b9fb69f26f2c294c96563ce7bd20d8b6765f3d082202a3e0e91079f05bdf1022037db040ce63faaea11b55747bac355dc520be0e5a75cb2c889d70aa1d54fcf78012102411558a3b5b11199858a86889b2053fdf270642284912a3df04ff550cf3fbf3b Age:648806 WalletName:} {PrevHash:ff508647759217c1f277a9fefdbf656ac1ac12c2ee86acc75ef9c6748a1d0550 OutputIndex:0 OutputValue:5127 Addresses:[1LCtCWHAGJyK49AZP57Doi1E6bLwTXkorF] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:473044022015e127bf2682a0fbda14f0ea39ecce12e9d65186dba4614affd161e8f474d7b7022013e95a85a65c90fd40e5321e146e1a536ecc9558b67fb788d0a4454fa7a31bc20121038b148a3f47297212d586600a5f73991fc01a7abd661c32329507e623e4aac11a Age:642926 WalletName:}]...}]
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\UnconfirmedTransactionsEndpoint.php

$txClient = new TXClient($apiContext);
$txs = $txClient->getUnconfirmed(array(), $apiContext);

[{'received': '2015-06-10T23:10:31.534Z',
  'ver': 1,
  'double_spend': False,
  'outputs': [{'value': 131910000,
    'addresses': ['1Gb4TAU4GD73akvt4V7tg2WdqadcpQSvhH'],
    'script_type': 'pay-to-pubkey-hash',
    'script': '76a914aafae908428a1778d0e76f0dca4cb800f731873e88ac'},
    ...
    ],
  'block_height': -1,
  'receive_count': 109,
  'total': 131940196,
  'fees': 13754,
  'inputs': [{'output_value': 9467,
    'sequence': 4294967295,
    'prev_hash': '3c65f3bdec16c55fcedec8159ca8b7decf12393e3a7088febbfacfd752534ee0',
    'script': '47304402207510efc3ff2ed868478ef627c4fd53a27254874e61c1ff1be6fb5221844c08ac02204a8a50c35d3fac8ec0c15f468c98abb629cadf3ac898f7be5e6f464ba9e4fba00121035649100ba29d0b34df8a928c2ed7ab1389d017b4ed206f6df5c212a461a764f2',
    'age': 38,
    'addresses': ['1MBuZ5ZxhytsjRZmec5rLr1WhDWm9DgFUJ'],
    'output_index': 1,
    'script_type': 'pay-to-pubkey-hash'},
    ...
   ],
  'preference': 'medium',
  'vout_sz': 2,
  'vin_sz': 3,
  'relayed_by': '167.114.118.213:8333',
  'hash': '97f551c7d200f3acf322160d6ada87830d2ad8d935909630a35a87d61bc8fa74',
  'addresses': ['1DLDFKuAWGUJqZoYrevG8bHufuZCjCdSFZ',
   '1Gb4TAU4GD73akvt4V7tg2WdqadcpQSvhH',
   ...
   ]
  'lock_time': 0,
  'size': 521,
  'confirmations': 0},
 ...
]
```

The Unconfirmed Transactions Endpoint returns an array of the latest transactions relayed by nodes in a blockchain that haven't been included in any blocks.

Resource | Method | Return Object
-------- | ------ | -------------
/txs | GET | Array[[TX](#TX)]

The returned object is an array of transactions that haven't been included in blocks, arranged in reverse chronological order (latest is first, then older transactions follow).

<aside class="notice">
Due to <a href="https://bitcoin.org/en/glossary/malleability">transaction malleability</a> it can be difficult to deal with transaction hashes before they've been confirmed in blocks. Use caution, and consider applying our <a href="#confidence-factor">Confidence Factor</a> to mitigate potential issues.
</aside>

## Creating Transactions

Using BlockCypher's API, you can push transactions to blockchains one of two ways:

- Use a third party library to create your transactions and [push raw transactions](#push-raw-transaction-endpoint)
- Use our two-endpoint process outlined below, wherein we generate a [TXSkeleton](#txskeleton) based on your input address, output address, and value to transfer.

In either case, for security reasons, we never take possession of your private keys. We do use private keys with our [Microtransaction API](#microtransaction-api), but they are for low-value transactions and we discard them immediately from our servers' memory.

<aside class="warning">
Always use HTTPS for creating and pushing transactions.
</aside>

### New Transaction Endpoint

```shell
# Note resource change to bcy/test instead of btc/main, makes sharing private keys easier :)
curl -d '{"inputs":[{"addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"]}],"outputs":[{"addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"], "value": 1000000}]}' https://api.blockcypher.com/v1/bcy/test/txs/new

{
"tx": {
  "block_height": -1,
  "hash": "c2b350b273b3bf04791d8e59fc9c021fd91fa423c50c29473dc079150f5a778a",
  "addresses": [
    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
  ],
  "total": 4988000,
  "fees": 12000,
  "size": 119,
  "preference": "high",
  ...,
  "confirmations": 0,
  "inputs": [
    {
    "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
    "output_value": 5000000,
    "addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script": "",
    "script_type": "",
    ...
    }
  ],
  "outputs": [
    {
    "value": 1000000,
    "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
    "addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
    "script_type": "pay-to-pubkey-hash"
    },
    {
    "value": 3988000,
    "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
    "addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script_type": "pay-to-pubkey-hash"
    }
  ]
},
"tosign": ["32b5ea64c253b6b466366647458cfd60de9cd29d7dc542293aa0b8b7300cd827"]
}
```

```javascript
var newtx = {
  inputs: [{addresses: ['CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9']}],
  outputs: [{addresses: ['C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn'], value: 100000}]
};
$.post('https://api.blockcypher.com/v1/bcy/test/txs/new', JSON.stringify(newtx))
  .then(function(d) {console.log(d)});
> {
>   "tx": {
>     "block_height": -1,
>     "hash": "c2b350b273b3bf04791d8e59fc9c021fd91fa423c50c29473dc079150f5a778a",
>     "addresses": [
>       "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
>       "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>     ],
>     "total": 4988000,
>     "fees": 12000,
>     "size": 119,
>     "preference": "high",
>     "relayed_by": "208.71.159.84",
>     "received": "2015-05-21T19:04:14.492743867Z",
>     "ver": 1,
>     "lock_time": 0,
>     "double_spend": false,
>     "vin_sz": 1,
>     "vout_sz": 2,
>     "confirmations": 0,
>     "inputs": [
>       {
>         "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
>         "output_index": 0,
>         "script": "",
>         "output_value": 5000000,
>         "sequence": 4294967295,
>         "addresses": [
>           "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>         ],
>         "script_type": "",
>         "age": 4
>       }
>     ],
>     "outputs": [
>       {
>         "value": 1000000,
>         "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
>         "addresses": [
>           "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>         ],
>         "script_type": "pay-to-pubkey-hash"
>       },
>       {
>         "value": 3988000,
>         "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
>         "addresses": [
>           "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>         ],
>         "script_type": "pay-to-pubkey-hash"
>       }
>     ]
>   },
>   "tosign": [
>     "32b5ea64c253b6b466366647458cfd60de9cd29d7dc542293aa0b8b7300cd827"
>   ]
> }
```

```ruby
# Using BlockCypher's Internal Testnet
> bc_test = BlockCypher::Api.new(api_token:"YOURTOKEN", currency:BlockCypher::BCY, network:BlockCypher::TEST_NET)
=> #<BlockCypher::Api:0x00000001a9ebf0 @api_token="YOURTOKEN", @currency="bcy", @network="test", @version="v1">

> txskel = bc_test.transaction_new(["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"], ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"], 100000)
=> {"tx"=>
  {"block_height"=>-1,
   "hash"=>"2094c09f880f88dea1a1ff3a6e19c6af4892b4b26f8e6315fd76c6ddc369ffbf",
   "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
   "total"=>3856000,
   "fees"=>12000,
   "size"=>119,
   "preference"=>"high",
   "relayed_by"=>"199.83.221.11",
   "received"=>"2015-06-17T20:41:22.818882286Z",
   "ver"=>1,
   "lock_time"=>0,
   "double_spend"=>false,
   "vin_sz"=>1,
   "vout_sz"=>2,
   "confirmations"=>0,
   "inputs"=>
    [{"prev_hash"=>"6b1c30cad97df956cfcb47b4cd471bb69112fb726b0fb129575337e3fb9f2c1a",
      "output_index"=>2,
      "script"=>"",
      "output_value"=>3868000,
      "sequence"=>4294967295,
      "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
      "script_type"=>"",
      "age"=>9739}],
   "outputs"=>
    [{"value"=>100000,
      "script"=>"76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
      "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
      "script_type"=>"pay-to-pubkey-hash"},
     {"value"=>3756000,
      "script"=>"76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
      "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
      "script_type"=>"pay-to-pubkey-hash"}]},
 "tosign"=>["c24150bea2d3b50c852d1c5eaccd3b6e286623b516e255f876d2b7c0d93f200b"]}
```

```python
# Creating transactions manually is very complicated.
# We recommend you look at blockcypher.simple_spend() for a full working example of building a transaction using the blockcypher API, verifying and signing that transactionally locally, and then broadcasting it to the network using blockypher's API
# With blockcypher.simple_spend(), this whole example would look like just the following:
>>> from blockcypher import simple_spend
>>> simple_spend(from_privkey='97838249d77bfa65f97be02b63fd1b7bb6a58474c7c22784a0da63993d1c2f90', to_address='C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn', to_satoshis=1000000, coin_symbol='bcy')
'7981c7849294648c1e79dd16077a388b808fcf8c20035aec7cc5315b37dacfee'

# That said, here it is in parts
>>> from blockcypher import create_unsigned_tx
>>> inputs = [{'address': 'CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9'}, ]
>>> outputs = [{'address': 'C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn', 'value': 1000000}]
>>> unsigned_tx = create_unsigned_tx(inputs=inputs, outputs=outputs, coin_symbol='bcy')
>>> unsigned_tx
{
    "tosign": [
        "c55602559d9306c0932da726ceda788bdca7f7b82d989f0c76c99161941f1198",
        "9b3fcf3d94fae6fe0cf4e8a82fa408c5e8c56ee1c03ae88962e3f03879f1ffdb",
        "eb2beed1f015ec88714e1e2335af3a0d8e535397ce52a430f649b4f465677ed0",
        ...,
    ],
    "tx": {
        "addresses": [
            "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
            "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
        ],
        "block_height": -1,
        "confidence": 0,
        "confirmations": 0,
        "double_spend": False,
        "fees": 142700,
        "hash": "afceb017a0d280a746ad5ed5c18228e302ebc2aa71ec768454b9a768f73c24c1",
        "inputs": [
            {
                "addresses": [
                    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
                ],
                "age": 163376,
                "output_index": 0,
                "output_value": 23455,
                "prev_hash": "457a538479fb50f3e060702594ef92962c2570d592b8e7c089d7b13fc9b5b3dc",
                "script": "",
                "script_type": "",
                "sequence": 4294967295
            },
            ...,
        ],
        "lock_time": 0,
        "outputs": [
            {
                "addresses": [
                    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
                ],
                "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
                "script_type": "pay-to-pubkey-hash",
                "value": 1000000
            },
            ...,
        ],
        "preference": "high",
        "received": "2015-10-21T19:11:35.774722671Z",
        "relayed_by": "207.38.134.25, 127.0.0.1",
        "size": 775,
        "total": 3773210,
        "ver": 1,
        "vin_sz": 17,
        "vout_sz": 2
    }
}
```

```go
//this is the same example as below, demonstrates
//creating New TXSkels, signing, and Sending TXSkels
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  //note the change to BlockCypher Testnet
  bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
  //generate two addresses
  addr1, err := bcy.GenAddrKeychain()
  addr2, err := bcy.GenAddrKeychain()
  //use faucet to fund first
  _, err = bcy.Faucet(addr1, 3e5)
  if err != nil {
    fmt.Println(err)
  }
  //Post New TXSkeleton
  skel, err := bcy.NewTX(gobcy.TempNewTX(addr1.Address, addr2.Address, *big.NewInt(2e5)), false)
  //Sign it locally
  err = skel.Sign([]string{addr1.Private})
  if err != nil {
    fmt.Println(err)
  }
  //Send TXSkeleton
  skel, err = bcy.SendTX(skel)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", skel)
}

//Result from `go run`:
//{Trans:{BlockHash: BlockHeight:-1 Hash:fbf755272feac2e82e86c8bd46edcf427d88667737d27557ac0ff51a347b14bc Addresses:[BuDPcfmGXfhkkVkJpNjeofiyEedHnUyJqs BsZhuenEzwAVWJC5KVKj4M4cbuownuwDsN] Total:{neg:false abs:[272700]} Fees:{neg:false abs:[27300]} Size:225 Preference:high RelayedBy:147.253.37.187 Received:2020-09-17 21:16:42.749132419 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:38776ceecb3b5541e5d4863124a72dc20dc913156075b8c7f09e1435cfaded84 OutputIndex:0 OutputValue:300000 Addresses:[BsZhuenEzwAVWJC5KVKj4M4cbuownuwDsN] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:47304402200d144dad3ca80337d33d4ab4622c2e74e97be366c3f8f752d7835dedc7a5b39902202b2ddb1b46cc82040e0821f7f6e446c8a186c77a726dfc73509ed1727deb787e0121020c78f0b849ba80d7ad91e0fac7943965380582e751376721f7180c13163ba711 Age:0 WalletName:}] Outputs:[{SpentBy: Value:{neg:false abs:[200000]} Addresses:[BuDPcfmGXfhkkVkJpNjeofiyEedHnUyJqs] ScriptType:pay-to-pubkey-hash Script:76a91416e75d6b264c4643a76bb21ead74d3a7aefce49288ac DataHex: DataString:} {SpentBy: Value:{neg:false abs:[72700]} Addresses:[BsZhuenEzwAVWJC5KVKj4M4cbuownuwDsN] ScriptType:pay-to-pubkey-hash Script:76a91404ceb3e125b65c12c6bb13e20137e8d0bcefb35288ac DataHex: DataString:}]} ToSign:[] Signatures:[] PubKeys:[] ToSignTX:[] Errors:[]}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\NewTransactionEndpoint.php

// Create a new instance of Transaction object
$tx = new Transaction();

// Tx inputs
$input = new \BlockCypher\Api\Input();
$input->addAddress("C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm");
$tx->addInput($input);
// Tx outputs
$output = new \BlockCypher\Api\Output();
$output->addAddress("C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi");
$tx->addOutput($output);
// Tx amount
$output->setValue(1000); // Satoshis

$txClient = new TXClient($apiContext);
$txSkeleton = $txClient->create($tx);

{
  "tx":{
    "block_height":-1,
    "hash":"edb785c310ea58c70245c9a89130efca8fb0a02d4bee47b18542986e7bf95806",
    "addresses":[
      "C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm",
      "C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi"
    ],
    "total":5420100,
    "fees":12000,
    "size":119,
    "preference":"high",
    "relayed_by":"88.7.90.149",
    "received":"2015-06-17T10:23:02.475392139Z",
    "ver":1,
    "lock_time":0,
    "double_spend":false,
    "vin_sz":1,
    "vout_sz":2,
    "confirmations":0,
    "inputs":[{ "prev_hash":"64e39fa5322ee604ad548dc5d4ae1a61b0a575278724d0df7e53dd334f5f13e3",
        "output_index":0,
        "script":"",
        "output_value":5432100,
        "sequence":4294967295,
        "addresses":["C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm"],
        "script_type":"",
        "age":0
      }
    ],
    "outputs":[{ "value":1000,
        "script":"76a9147b2b09ad46d95e177df6969a275db321c66cecf388ac",
        "addresses":["C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi"],
        "script_type":"pay-to-pubkey-hash"
      },
      { "value":5419100,
        "script":"76a9148c6f28c814116f1ae3d0940b2fb05a68a9507e8a88ac",
        "addresses":["C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm"],
        "script_type":"pay-to-pubkey-hash"
      }]
  },
  "tosign":["770af2c9a9b7b8b1998e36d4c1ce2b8fa3b9d4d6575679a513722d9f478177d2"]
}
```

To use BlockCypher's two-endpoint transaction creation tool, first you need to provide the input address(es), output address, and value to transfer (in satoshis). Provide this in a partially-filled out [TX](#tx) request object.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/new | POST | [TX](#tx) | [TXSkeleton](#txskeleton)

Flag | Type | Effect
---- | ---- | ------
**includeToSignTx** | *bool* | If *true*, includes **tosign_tx** array in [TXSkeleton](#txskeleton), useful for validating data to sign; *false* by default.

As you can see from the code example, you only need to provide a single public address within the **addresses** array of both the **input** and **output** of your [TX](#tx) request object. You also need to fill in the **value** with the amount you'd like to transfer from one address to another.

If you'd like, you can even use a [Wallet](#wallet) instead of addresses as your input. You just need to use two non-standard fields (your **wallet_name** and **wallet_token**) within the **inputs** array in your transaction, instead of **addresses**:

`{inputs:[{"wallet_name":"alice", "wallet_token":"YOURTOKEN"}], value: 5000000}`

While this particular usage will differ between client libraries, the result is the same: the addresses within your wallet will be used as the **inputs**, as if all of them had been placed within the **addresses** array.

As a return object, you'll receive a [TXSkeleton](#txskeleton) containing a slightly-more complete [TX](#tx) alongside data you need to sign in the **tosign** array. You'll need this object for the next steps of the transaction creation process.

<aside class="warning">
The <a href="#txskeleton">TXSkeleton</a> returned by this endpoint may contain some data that's temporary or incomplete, like the <b>hash</b>, <b>size</b>, and the inputs' <b>script</b> fields. This is by design, as the final <a href="#tx">TX</a> can only be computed once signed data has been added. Do not rely on these fields until they are returned and sent to the network via the Send Transaction Endpoint outlined below.
</aside>

### Validating the Data to Sign

For the extra cautious, you can protect yourself from a potential malicious attack on BlockCypher by validating the data we're asking you to sign. Unfortunately, it's impossible to do so directly, as pre-signed signature data is hashed twice using SHA256. To get around this, set the **includeToSignTx** URL flag to *true*. The optional **tosign_tx** array will be returned within the [TXSkeleton](#txskeleton), which you can use in the following way:

- Hashing the hex-encoded string twice using SHA256 should give you back the corresponding **tosign** data.
- Decoding the hex-encoded string using our `/txs/decode` endpoint (or an independent, client-side source) should give you the output addresses and amounts that match your work-in-progress transaction.

<aside class="notice">
If you want to automatically empty your input address(es) without knowing their exact value, your <a href="#tx">TX</a> request object's <b>value</b> can be set to -1 to <i>sweep</i> all value from your input address(es) to your output address. Please be advised that this only works with a single output address.
</aside>

<aside class="notice">
There are many manually configurable options available via your <a href="#tx">TX</a> request object. You can read about them in more detail in the <a href="#customizing-transaction-requests">Customizing Transaction Requests</a> section below.
</aside>

```shell
# next, you sign the data returned in the tosign array locally
# here we're using our signer tool (https://github.com/blockcypher/btcutils/tree/master/signer), but any ECDSA secp256k1 signing tool should work
# $PRIVATEKEY here is a hex-encoded private key corresponding to the input from address CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9

./signer 32b5ea64c253b6b466366647458cfd60de9cd29d7dc542293aa0b8b7300cd827 $PRIVATEKEY

3045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca
```

```javascript
// next, you sign the data returned in the tosign array locally
// here we're using bitcoinjs built to expose bigi, buffer, and require
// you can get bitcoin.js here:
// http://bitcoinjs.org/

var bitcoin = require("bitcoinjs-lib");
var bigi    = require("bigi");
var buffer  = require('buffer');
var keys    = new bitcoin.ECPair(bigi.fromHex(my_hex_private_key));

var newtx = {
  inputs: [{addresses: ['CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9']}],
  outputs: [{addresses: ['C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn'], value: 100000}]
};
// calling the new endpoint, same as above
$.post('https://api.blockcypher.com/v1/bcy/test/txs/new', JSON.stringify(newtx))
  .then(function(tmptx) {
    // signing each of the hex-encoded string required to finalize the transaction
    tmptx.pubkeys = [];
    tmptx.signatures = tmptx.tosign.map(function(tosign, n) {
      tmptx.pubkeys.push(keys.getPublicKeyBuffer().toString("hex"));
      return keys.sign(new buffer.Buffer(tosign, "hex")).toDER().toString("hex");
    });
    // sending back the transaction with all the signatures to broadcast
    $.post('https://api.blockcypher.com/v1/bcy/test/txs/send', tmptx).then(function(finaltx) {
      console.log(finaltx);
    })
  });
```

```python
>>> from blockcypher import make_tx_signatures
# Using our same unsigned_tx as before, iterate through unsigned_tx['tx']['inputs'] to find each address in order.
# Include duplicates as many times as they may appear:
>>> privkey_list = ['privkeyhex1', 'privkeyhex2', ...]
>>> pubkey_list = ['pubkeyhex1', 'pubkeyhex2', ,,,]
>>> tx_signatures = make_tx_signatures(txs_to_sign=unsigned_tx['tosign'], privkey_list=privkey_list, pubkey_list=pubkey_list)
```

```ruby
# Signing is handled by our SDK in the next step
```

```go
//signing handled in both examples
```

```php
<?php
// Sign the transaction
// Private key for address: C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm
$privateKeys = array("2c2cc015519b79782bd9c5af66f442e808f573714e3c4dc6df7d79c183963cff");
$txSkeleton = $txClient->sign($txSkeleton, $privateKeys);
```

### Locally Sign Your Transaction

With your [TXSkeleton](#txskeleton) returned from the New Transaction Endpoint, you now need to use your private key(s) to sign the data provided in the **tosign** array.

Digital signing can be a difficult process, and is where the majority of issues arise when dealing with cryptocurrency transactions. We are working on integrating client-side signing solutions into our libraries to make this process easier. You can read more about [signing here.](https://bitcoin.org/en/developer-guide#term-signature) In the mean time, if you want to experiment with client-side signing, consider using our [signer tool](https://github.com/blockcypher/btcutils/tree/master/signer).

<aside class="notice">
One of the most common errors in the signing process is a data format mismatch. We always return and expect hex-encoded data, but oftentimes, standard signing libraries require byte arrays. Remember to convert your data, and always send hex-encoded signatures to BlockCypher.
</aside>

```shell
# the request body is truncated because it's huge, but it's the same as the returned object from above plus the signatures and public keys
# remember, still using bcy/test instead of btc/main
curl -d '{"tx": {...}, "tosign": [ "32b5ea64c253b6b466366647458cfd60de9cd29d7dc542293aa0b8b7300cd827" ], "signatures": [ "3045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca" ], "pubkeys": [ "02152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044" ] }' https://api.blockcypher.com/v1/bcy/test/txs/send?token=YOURTOKEN

{
"tx": {
  "block_height": -1,
  "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
  "addresses": [
    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
  ],
  "total": 4988000,
  "fees": 12000,
  "size": 226,
  "preference": "high",
  ...,
  "confirmations": 0,
  "inputs": [
    {
    "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
    "output_value": 5000000,
    "addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script": "483045022100921fc36b911094280f...",
    "script_type": "pay-to-pubkey-hash",
    ...
    }
  ],
  "outputs": [
    {
    "value": 1000000,
    "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
    "addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
    "script_type": "pay-to-pubkey-hash"
    },
    {
    "value": 3988000,
    "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
    "addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script_type": "pay-to-pubkey-hash"
    }
  ]
},
"tosign": [ "" ]
}
```

```javascript
// this shows just the last step of sending your transaction, see the signing section
// for the fully integrated sample
// the request body is truncated because it's huge, but it's the same as the returned
// object from calling the new endpoint plus the signatures and public keys added in the
// signing section
var sendtx = {
  tx: {...},
  tosign: [
    "32b5ea64c253b6b466366647458cfd60de9cd29d7dc542293aa0b8b7300cd827"
  ],
  signatures: [
    "3045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca"
  ],
  pubkeys: [
    "02152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044"
  ]
};
$.post('https://api.blockcypher.com/v1/bcy/test/txs/send', JSON.stringify(sendtx))
  .then(function(d) {console.log(d)});
> {
>   "tx": {
>     "block_height": -1,
>     "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
>     "addresses": [
>       "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
>       "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>     ],
>     "total": 4988000,
>     "fees": 12000,
>     "size": 226,
>     "preference": "high",
>     "relayed_by": "73.162.198.68",
>     "received": "2015-05-22T04:38:57.470017042Z",
>     "ver": 1,
>     "lock_time": 0,
>     "double_spend": false,
>     "vin_sz": 1,
>     "vout_sz": 2,
>     "confirmations": 0,
>     "inputs": [
>       {
>         "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
>         "output_index": 0,
>         "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
>         "output_value": 5000000,
>         "sequence": 4294967295,
>         "addresses": [
>           "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>         ],
>         "script_type": "pay-to-pubkey-hash",
>         "age": 546
>       }
>     ],
>     "outputs": [
>       {
>         "value": 1000000,
>         "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
>         "addresses": [
>           "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>         ],
>         "script_type": "pay-to-pubkey-hash"
>       },
>       {
>         "value": 3988000,
>         "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
>         "addresses": [
>           "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>         ],
>         "script_type": "pay-to-pubkey-hash"
>       }
>     ]
>   },
>   "tosign": [
>     ""
>   ]
> }
```

```ruby
> bc_test.transaction_sign_and_send(txskel, "YOURCOMPRESSEDPRIVATEKEYINHEX")
=> {"tx"=>
  {"block_height"=>-1,
   "hash"=>"620f484194eca649ccd48501bff1c8046fed4a1b28bee9f6b0db8c93ced672be",
   "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9", "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
   "total"=>3856000,
   "fees"=>12000,
   "size"=>226,
   "preference"=>"high",
   "relayed_by"=>"199.83.221.11",
   "received"=>"2015-06-17T20:47:36.092834184Z",
   "ver"=>1,
   "lock_time"=>0,
   "double_spend"=>false,
   "vin_sz"=>1,
   "vout_sz"=>2,
   "confirmations"=>0,
   "inputs"=>
    [{"prev_hash"=>"6b1c30cad97df956cfcb47b4cd471bb69112fb726b0fb129575337e3fb9f2c1a",
      "output_index"=>2,
      "script"=>
       "483045022100803c8b097d0653cc094362f772e687da00aa263f22ccc6a203b0e0234e096fbd022068d3a0ef0306ee9698313bdac044cfb32f2cf63b7c29dbf43568721f040c7b80012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
      "output_value"=>3868000,
      "sequence"=>4294967295,
      "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
      "script_type"=>"pay-to-pubkey-hash",
      "age"=>9745}],
   "outputs"=>
    [{"value"=>100000,
      "script"=>"76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
      "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
      "script_type"=>"pay-to-pubkey-hash"},
     {"value"=>3756000,
      "script"=>"76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
      "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
      "script_type"=>"pay-to-pubkey-hash"}]},
 "tosign"=>[""]}
```

```go
//this is the same example as above, demonstrates
//creating New TXSkels, signing, and Sending TXSkels
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  //note the change to BlockCypher Testnet
  bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
  //generate two addresses
  addr1, err := bcy.GenAddrKeychain()
  addr2, err := bcy.GenAddrKeychain()
  //use faucet to fund first
  _, err = bcy.Faucet(addr1, 3e5)
  if err != nil {
    fmt.Println(err)
  }
  //Post New TXSkeleton
  skel, err := bcy.NewTX(gobcy.TempNewTX(addr1.Address, addr2.Address, *big.NewInt(2e5)), false)
  //Sign it locally
  err = skel.Sign([]string{addr1.Private})
  if err != nil {
    fmt.Println(err)
  }
  //Send TXSkeleton
  skel, err = bcy.SendTX(skel)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", skel)
}

//Result from `go run`:
//{Trans:{BlockHash: BlockHeight:-1 Hash:f1bcbb63da39d00f10ba369c4a8537d11aff22184371399f391d5476644ca08a Addresses:[C35m91DjyASyJd9HJrYpGUyWDsgg2xYf4D C2MUyA8UcdLDKihSBS6uQGs15Fq8FsVvNj] Total:{neg:false abs:[272700]} Fees:{neg:false abs:[27300]} Size:226 Preference:high RelayedBy:147.253.37.187 Received:2020-09-17 21:17:32.844203434 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:694b9d7f7f4ce1d713335378d20af4f5674a01522c45735547fbb08d420bd0e8 OutputIndex:0 OutputValue:300000 Addresses:[C35m91DjyASyJd9HJrYpGUyWDsgg2xYf4D] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:483045022100b8af200fac11459601e4bffcd63705cd64df66073114d0a9d8170999595047cb0220119ee69fa3b4e14a52fdf48c1c4cca5df67d4c38d19881f7593b7874724363aa01210333a77a74fac42bebe99e08b1ff5cc9ede404259cecbcddbce50c376c3a28943b Age:0 WalletName:}] Outputs:[{SpentBy: Value:{neg:false abs:[200000]} Addresses:[C2MUyA8UcdLDKihSBS6uQGs15Fq8FsVvNj] ScriptType:pay-to-pubkey-hash Script:76a91465380ab1a5132b4776f756c643a4a711ce33ef4188ac DataHex: DataString:} {SpentBy: Value:{neg:false abs:[72700]} Addresses:[C35m91DjyASyJd9HJrYpGUyWDsgg2xYf4D] ScriptType:pay-to-pubkey-hash Script:76a9146d37028fee0a1c96cb9dc4a5d6ad25ea3ab7740088ac DataHex: DataString:}]} ToSign:[] Signatures:[] PubKeys:[] ToSignTX:[] Errors:[]}
```

```python
>>> from blockcypher import broadcast_signed_transaction
>>> broadcast_signed_transaction(unsigned_tx=unsigned_tx, signatures=tx_signatures, pubkeys=pubkey_list)
{
    "tosign": [
        ""
    ],
    "tx": {
        "addresses": [
            "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
            "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
        ],
        "block_height": -1,
        "confidence": 0,
        "confirmations": 0,
        "double_spend": False,
        "fees": 142700,
        "hash": "afceb017a0d280a746ad5ed5c18228e302ebc2aa71ec768454b9a768f73c24c1",
        "inputs": [
            {
                "addresses": [
                    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
                ],
                "age": 163376,
                "output_index": 0,
                "output_value": 23455,
                "prev_hash": "457a538479fb50f3e060702594ef92962c2570d592b8e7c089d7b13fc9b5b3dc",
                "script": "",
                "script_type": "",
                "sequence": 4294967295
            },
            ...,
        ],
        "lock_time": 0,
        "outputs": [
            {
                "addresses": [
                    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
                ],
                "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
                "script_type": "pay-to-pubkey-hash",
                "value": 1000000
            }
        ],
        "preference": "high",
        "received": datetime.datetime(2015, 10, 21, 19, 11, 35, 774722, tzinfo=tzutc()),
        "relayed_by": "207.38.134.25, 127.0.0.1",
        "size": 192,
        "total": 10000,
        "ver": 1,
        "vin_sz": 1,
        "vout_sz": 1
    }
}

```

```php
<?php
// Send the transaction
$txSkeleton = $txClient->send($txSkeleton);

{
  "tx":{
    "block_height":-1,
    "hash":"edb785c310ea58c70245c9a89130efca8fb0a02d4bee47b18542986e7bf95806",
    "addresses":[
      "C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm",
      "C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi"
    ],
    "total":5420100,
    "fees":12000,
    "size":119,
    "preference":"high",
    "relayed_by":"88.7.90.149",
    "received":"2015-06-17T10:23:02.475392139Z",
    "ver":1,
    "lock_time":0,
    "double_spend":false,
    "vin_sz":1,
    "vout_sz":2,
    "confirmations":0,
    "inputs":[{
        "prev_hash":"64e39fa5322ee604ad548dc5d4ae1a61b0a575278724d0df7e53dd334f5f13e3",
        "output_index":0,
        "script":"",
        "output_value":5432100,
        "sequence":4294967295,
        "addresses":["C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm"],
        "script_type":"",
        "age":0
      }
    ],
    "outputs":[
      {"value":1000,
        "script":"76a9147b2b09ad46d95e177df6969a275db321c66cecf388ac",
        "addresses":["C4MYFr4EAdqEeUKxTnPUF3d3whWcPMz1Fi"],
        "script_type":"pay-to-pubkey-hash"
      },
      { "value":5419100,
        "script":"76a9148c6f28c814116f1ae3d0940b2fb05a68a9507e8a88ac",
        "addresses":["C5vqMGme4FThKnCY44gx1PLgWr86uxRbDm"],
        "script_type":"pay-to-pubkey-hash"
      }]
  },
  "tosign":[]
}
```

### Send Transaction Endpoint

Once you've finished signing the **tosign** array locally, put that (hex-encoded) data into the **signatures** array of the [TXSkeleton](#txskeleton). You also need to include the corresponding (hex-encoded) public key(s) in the **pubkeys** array, in the order of the addresses/inputs provided. Signature and public key order matters, so make sure they are returned in the same order as the inputs you provided.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/send | POST | [TXSkeleton](#txskeleton) | [TXSkeleton](#txskeleton)

If the transaction was successful, you'll receive a [TXSkeleton](#TXskeleton) with the completed [TX](#tx) (which contains its final **hash**) and an HTTP Status Code 201.

### Dealing with Errors

Signing and creating transactions can be one of the trickiest parts of using blockchains in your applications. Consequently, when an error is encountered when [Creating Transactions](#creating-transactions), we send back an HTTP Status Code 400 alongside a descriptive array of **errors** within the [TXSkeleton](#txskeleton).

<aside class="notice">
One of the most common errors we see are users who use uncompressed public keys when compressed public keys were used to generate the address; remember to be careful to use the right keys!
</aside>

## Customizing Transaction Requests

```shell
# here are some example request objects for `/txs/new`
# simplest posssible case, with input address, output address and value
'{
"inputs":
  [{"addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"]}],
"outputs":
  [{"addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"], "value": 1000000}]
}'

# let's add confirmation field to only allow UTXOs, and set fee preference to medium
'{
"inputs":
  [{"addresses": ["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"]}],
"outputs":
  [{"addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"], "value": 1000000}],
"confirmations": 1,
"preference": "medium"
}'

# instead of providing an address, let's specify a particular UTXO via prev_hash and output_index, and add a different change_address
'{
"inputs":
  [{"prev_hash":"a238c817bea2d54dad4cdde38a1cfeb2aa5592313bc49514f3cd0d9db71c69c8", "output_index":0}],
"outputs":
  [{"addresses": ["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"], "value": 1000000}],
"confirmations": 1,
"preference": "medium",
"change_address": "CFr99841LyMkyX5ZTGepY58rjXJhyNGXHf"
}'
```

While we demonstrated the simplest use of our [two-endpoint process to create transactions](#creating-transactions), you can have finer-grain control by modifying the [TX](#tx) request object before sending to `/txs/new`.

By default, we allow unconfirmed UTXOs as inputs when creating transactions. If you only want to allow confirmed UTXOs, set the **confirmations** value in your [TX](#tx) request object to 1.

Instead of providing **addresses**, you can use **prev_hash** and **output_index** within the **inputs** array of your request object, in which case, we'll use the inputs as provided and not attempt to generate them from a list of addresses. We will compute change and fees the same way.

BlockCypher will set the change address to the first transaction input/address listed in the transaction. To redirect this default behavior, you can set an optional **change_address** field within the [TX](#tx) request object.

Fees in cryptocurrencies can be complex. We provide 2 different ways for you to control the fees included in your transactions:

1. Set the **preference** property in your [TX](#tx) request object to "high", "medium", or "low". This will calculate and include appropriate fees for your transaction to be included in the next 1-2 blocks, 3-6 blocks or 7 or more blocks respectively. You can see the explicit estimates per kilobyte for these high, medium, and low ranges by calling your base resource through the [Chain Endpoint.](#chain-endpoint) The default fee calculation is based on a "high" **preference**. A preference set to "zero" will set no fee.
1. Manually set the fee to a desired amount by setting the **fees** property in your [TX](#tx) request object. Note that a fee too low may result in an error for some transactions that would require it.

To learn more about fees, [bitcoinfees.com](http://bitcoinfees.com/) is a good resource.

For even more control, you can also change the **script_type** in the **outputs** of your partially filled [TX](#tx). You'll notice this used to powerful effect in the section on [Multisig Transactions](#multisig-transactions). These are the possible script types:

1. *pay-to-pubkey-hash* (most common transaction transferring to a public key hash, and the default behavior if no out)
1. *pay-to-multi-pubkey-hash* (multi-signatures transaction, now actually less used than *pay-to-script-hash* for this purpose)
1. *pay-to-pubkey* (used for mining transactions)
1. *pay-to-script-hash* (used for transactions relying on arbitrary scripts, now used primarily for multi-sig transactions)
1. *multisig-m-of-n* (not present in blockchain, but used by BlockCypher to construct P2SH multisig transactions; see [Multisig Transactions](#multisig-transactions) for more info)
1. *null-data* (sometimes called op-return; used to embed small chunks of data in the blockchain)
1. *empty* (no script present, mostly used for mining transaction inputs)
1. *unknown* (non-standard script)

<aside class="warning">
Due to safety checks within the API's transaction creation process, the user-set <b>value</b> field of an <b>output</b> may never be 0, except when using a <i>null-data</i> <b>script-type</b>, where it must be 0.
</aside>

## Push Raw Transaction Endpoint

```shell
# Note resource change to bcy/test instead of btc/main
curl -d '{"tx":"01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000"}' https://api.blockcypher.com/v1/bcy/test/txs/push?token=YOURTOKEN

{
  "block_height": -1,
  "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
  "addresses": [
    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
  ],
  "total": 4988000,
  "fees": 12000,
  "size": 226,
  "preference": "high",
  "relayed_by": "73.162.198.68",
  "received": "2015-05-22T05:10:00.305308666Z",
  "ver": 1,
  "lock_time": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 2,
  "confirmations": 0,
  "inputs": [
    {
      "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
      "output_index": 0,
      "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
      "output_value": 5000000,
      "sequence": 4294967295,
      "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
      ],
      "script_type": "pay-to-pubkey-hash",
      "age": 576
    }
  ],
  "outputs": [
    {
      "value": 1000000,
      "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
      "addresses": [
        "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
      ],
      "script_type": "pay-to-pubkey-hash"
    },
    {
      "value": 3988000,
      "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
      "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
      ],
      "script_type": "pay-to-pubkey-hash"
    }
  ]
}
```

```javascript
var pushtx = {
  tx: "01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000"
};
$.post('https://api.blockcypher.com/v1/bcy/test/txs/push', JSON.stringify(pushtx))
  .then(function(d) {console.log(d)});
> {
>   "block_height": -1,
>   "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
>   "addresses": [
>     "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
>     "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>   ],
>   "total": 4988000,
>   "fees": 12000,
>   "size": 226,
>   "preference": "high",
>   "relayed_by": "73.162.198.68",
>   "received": "2015-05-22T05:10:00.305308666Z",
>   "ver": 1,
>   "lock_time": 0,
>   "double_spend": false,
>   "vin_sz": 1,
>   "vout_sz": 2,
>   "confirmations": 0,
>   "inputs": [
>     {
>       "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
>       "output_index": 0,
>       "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
>       "output_value": 5000000,
>       "sequence": 4294967295,
>       "addresses": [
>         "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>       ],
>       "script_type": "pay-to-pubkey-hash",
>       "age": 576
>     }
>   ],
>   "outputs": [
>     {
>       "value": 1000000,
>       "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
>       "addresses": [
>         "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>       ],
>       "script_type": "pay-to-pubkey-hash"
>     },
>     {
>       "value": 3988000,
>       "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
>       "addresses": [
>         "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>       ],
>       "script_type": "pay-to-pubkey-hash"
>     }
>   ]
> }
```

```ruby
> bc_test.push_hex("01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000")
=> {"block_height"=>-1,
 "hash"=>"4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
 "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
 "total"=>4988000,
 "fees"=>12000,
 "size"=>226,
 "preference"=>"high",
 "relayed_by"=>"199.83.221.11",
 "received"=>"2015-06-17T21:01:54.319930181Z",
 "ver"=>1,
 "lock_time"=>0,
 "double_spend"=>false,
 "vin_sz"=>1,
 "vout_sz"=>2,
 "confirmations"=>0,
 "inputs"=>
  [{"prev_hash"=>"c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
    "output_index"=>0,
    "script"=>
     "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
    "output_value"=>5000000,
    "sequence"=>4294967295,
    "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script_type"=>"pay-to-pubkey-hash",
    "age"=>37633}],
 "outputs"=>
  [{"value"=>1000000,
    "script"=>"76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
    "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
    "script_type"=>"pay-to-pubkey-hash"},
   {"value"=>3988000,
    "script"=>"76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
    "spent_by"=>"892fd7b36c1c3a2e5edb9b4a5d4ffd9ba74d78d3acf3b249991bd8d10a287dbd",
    "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script_type"=>"pay-to-pubkey-hash"}]}
```

```python
>>> from blockcypher import pushtx
>>> pushtx(tx_hex='01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000')
{
    "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
        "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
    ],
    "block_height": -1,
    "confirmations": 0,
    "double_spend": False,
    "fees": 12000,
    "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
    "inputs": [
        {
            "addresses": [
                "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
            ],
            "age": 576,
            "output_index": 0,
            "output_value": 5000000,
            "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
            "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
            "script_type": "pay-to-pubkey-hash",
            "sequence": 4294967295
        }
    ],
    "lock_time": 0,
    "outputs": [
        {
            "addresses": [
                "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
            ],
            "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
            "script_type": "pay-to-pubkey-hash",
            "value": 1000000
        },
        {
            "addresses": [
                "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
            ],
            "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
            "script_type": "pay-to-pubkey-hash",
            "value": 3988000
        }
    ],
    "preference": "high",
    "received": "2015-05-22T05:10:00.305308666Z",
    "relayed_by": "73.162.198.68",
    "size": 226,
    "total": 4988000,
    "ver": 1,
    "vin_sz": 1,
    "vout_sz": 2
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
  skel, err := bcy.PushTX("01000000011fdd692992f3d3859d01762119313a655177a59226ede31adc0199b28c73c450000000006a4730440220298915c785165233e2f19192bbbb9b8c3fed6d064279cb8968af6698c783b875022037287134f9f0aaf84429ba9bb50f1efcb4cedcfbd34407c285134e068234432c012102b17f04f5fa1bd44315ef33f8759822a302a2ba7fa28c56a50fac0441a341b96affffffff02400d0300000000001976a91446c7681dcacaee1ea3f57eced37755d1d09e070188accc550100000000001976a91490d969679032cc1af6a69b4161778f12e90cb13588ac00000000")
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", skel)
}

//Result from `go run`:
// {Trans:{BlockHash: BlockHeight:-1 Hash:55d6ef290774f5428b66a0a27e46a8c37032c33bc15abce4791d56a2f8adb773 Addresses:[C6LBLb668ZTS4okXhjtzapFWAdnFjH7A4i ByaXmP5k14WRBFLXArfCcqcpAHrUQRy3vL] Total:287500 Fees:12500 Size:225 Preference:high RelayedBy:52.27.84.91, 127.0.0.1 Received:2015-10-16 23:38:09.536632402 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:50c4738cb29901dc1ae3ed2692a57751653a31192176019d85d3f3922969dd1f OutputIndex:0 OutputValue:300000 Addresses:[C6LBLb668ZTS4okXhjtzapFWAdnFjH7A4i] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:4730440220298915c785165233e2f19192bbbb9b8c3fed6d064279cb8968af6698c783b875022037287134f9f0aaf84429ba9bb50f1efcb4cedcfbd34407c285134e068234432c012102b17f04f5fa1bd44315ef33f8759822a302a2ba7fa28c56a50fac0441a341b96a Age:0 WalletName:}] Outputs:[{SpentBy: Value:200000 Addresses:[ByaXmP5k14WRBFLXArfCcqcpAHrUQRy3vL] ScriptType:pay-to-pubkey-hash Script:76a91446c7681dcacaee1ea3f57eced37755d1d09e070188ac DataHex: DataString:} {SpentBy: Value:87500 Addresses:[C6LBLb668ZTS4okXhjtzapFWAdnFjH7A4i] ScriptType:pay-to-pubkey-hash Script:76a91490d969679032cc1af6a69b4161778f12e90cb13588ac DataHex: DataString:}]} ToSign:[] Signatures:[] PubKeys:[] ToSignTX:[] Errors:[]}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\PushRawTransactionEndpoint.php

$txClient = new TXClient($apiContext);
$hexRawTx = "01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000";
$tx = $txClient->push($hexRawTx);

{
  "block_height": -1,
  "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
  "addresses": [
    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
  ],
  "total": 4988000,
  "fees": 12000,
  "size": 226,
  "preference": "high",
  "relayed_by": "73.162.198.68",
  "received": "2015-05-22T05:10:00.305308666Z",
  "ver": 1,
  "lock_time": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 2,
  "confirmations": 0,
  "inputs": [
    {
      "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
      "output_index": 0,
      "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
      "output_value": 5000000,
      "sequence": 4294967295,
      "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
      ],
      "script_type": "pay-to-pubkey-hash",
      "age": 576
    }
  ],
  "outputs": [
    {
      "value": 1000000,
      "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
      "addresses": [
        "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
      ],
      "script_type": "pay-to-pubkey-hash"
    },
    {
      "value": 3988000,
      "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
      "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
      ],
      "script_type": "pay-to-pubkey-hash"
    }
  ]
}
```

If you'd prefer to use your own transaction library instead of the recommended path of our two-endpoint [transaction generation](#creating-transactions) we're still happy to help you propagate your raw transactions. Simply send your raw hex-encoded transaction to this endpoint and we'll leverage our huge network of nodes to propagate your transaction faster than anywhere else.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/push | POST | {"tx":$TXHEX} | [TX](#tx)

$TXHEX is a hex-encoded raw representation of your transaction, for example:

`01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000`

If it succeeds, you'll receive a decoded [TX](#tx) object and an HTTP Status Code 201. If you'd like, you can use the decoded transaction **hash** alongside an [Event](#events-and-hooks) to track its progress in the network.

## Decode Raw Transaction Endpoint

```shell
# Note resource change to bcy/test instead of btc/main
curl -d '{"tx":"01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000"}' https://api.blockcypher.com/v1/bcy/test/txs/decode?token=YOURTOKEN

{
  "block_height": -1,
  "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
  "addresses": [
    "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
    "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
  ],
  "total": 4988000,
  "fees": 12000,
  "size": 226,
  "preference": "high",
  "relayed_by": "73.162.198.68",
  "received": "2015-05-22T05:10:00.305308666Z",
  "ver": 1,
  "lock_time": 0,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 2,
  "confirmations": 0,
  "inputs": [
    {
      "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
      "output_index": 0,
      "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
      "output_value": 5000000,
      "sequence": 4294967295,
      "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
      ],
      "script_type": "pay-to-pubkey-hash",
      "age": 576
    }
  ],
  "outputs": [
    {
      "value": 1000000,
      "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
      "addresses": [
        "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
      ],
      "script_type": "pay-to-pubkey-hash"
    },
    {
      "value": 3988000,
      "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
      "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
      ],
      "script_type": "pay-to-pubkey-hash"
    }
  ]
}
```

```javascript
var decodetx = {
  tx: "01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000"
};
$.post('https://api.blockcypher.com/v1/bcy/test/txs/decode', JSON.stringify(decodetx))
  .then(function(d) {console.log(d)});
> {
>   "block_height": -1,
>   "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
>   "addresses": [
>     "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
>     "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>   ],
>   "total": 4988000,
>   "fees": 12000,
>   "size": 226,
>   "preference": "high",
>   "relayed_by": "73.162.198.68",
>   "received": "2015-05-22T05:10:00.305308666Z",
>   "ver": 1,
>   "lock_time": 0,
>   "double_spend": false,
>   "vin_sz": 1,
>   "vout_sz": 2,
>   "confirmations": 0,
>   "inputs": [
>     {
>       "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
>       "output_index": 0,
>       "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
>       "output_value": 5000000,
>       "sequence": 4294967295,
>       "addresses": [
>         "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>       ],
>       "script_type": "pay-to-pubkey-hash",
>       "age": 576
>     }
>   ],
>   "outputs": [
>     {
>       "value": 1000000,
>       "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
>       "addresses": [
>         "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
>       ],
>       "script_type": "pay-to-pubkey-hash"
>     },
>     {
>       "value": 3988000,
>       "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
>       "addresses": [
>         "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
>       ],
>       "script_type": "pay-to-pubkey-hash"
>     }
>   ]
> }
```

```ruby
> bc_test.decode_hex("01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000")
=> {"block_height"=>-1,
 "hash"=>"4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
 "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn", "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
 "total"=>4988000,
 "fees"=>12000,
 "size"=>226,
 "preference"=>"high",
 "relayed_by"=>"199.83.221.11",
 "received"=>"2015-06-17T21:01:54.319930181Z",
 "ver"=>1,
 "lock_time"=>0,
 "double_spend"=>false,
 "vin_sz"=>1,
 "vout_sz"=>2,
 "confirmations"=>0,
 "inputs"=>
  [{"prev_hash"=>"c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
    "output_index"=>0,
    "script"=>
     "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
    "output_value"=>5000000,
    "sequence"=>4294967295,
    "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script_type"=>"pay-to-pubkey-hash",
    "age"=>37633}],
 "outputs"=>
  [{"value"=>1000000,
    "script"=>"76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
    "addresses"=>["C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"],
    "script_type"=>"pay-to-pubkey-hash"},
   {"value"=>3988000,
    "script"=>"76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
    "spent_by"=>"892fd7b36c1c3a2e5edb9b4a5d4ffd9ba74d78d3acf3b249991bd8d10a287dbd",
    "addresses"=>["CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"],
    "script_type"=>"pay-to-pubkey-hash"}]}
```

```python
>>> from blockcypher import decodetx
>>> tx_hex = '01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000'}
>>> decodetx(tx_hex=tx_hex, coin_symbol='bcy')
{
    "addresses": [
        "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9",
        "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
    ],
    "block_height": -1,
    "confirmations": 0,
    "double_spend": False,
    "fees": 12000,
    "hash": "4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
    "inputs": [
        {
            "addresses": [
                "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
            ],
            "age": 28188,
            "output_index": 0,
            "output_value": 5000000,
            "prev_hash": "c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
            "script": "483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044",
            "script_type": "pay-to-pubkey-hash",
            "sequence": 4294967295
        }
    ],
    "lock_time": 0,
    "outputs": [
        {
            "addresses": [
                "C1rGdt7QEPGiwPMFhNKNhHmyoWpa5X92pn"
            ],
            "script": "76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
            "script_type": "pay-to-pubkey-hash",
            "value": 1000000
        },
        {
            "addresses": [
                "CEztKBAYNoUEEaPYbkyFeXC5v8Jz9RoZH9"
            ],
            "script": "76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
            "script_type": "pay-to-pubkey-hash",
            "spent_by": "892fd7b36c1c3a2e5edb9b4a5d4ffd9ba74d78d3acf3b249991bd8d10a287dbd",
            "value": 3988000
        }
    ],
    "preference": "high",
    "received": "2015-06-10T23:37:05.211843254Z",
    "relayed_by": "207.38.134.25",
    "size": 226,
    "total": 4988000,
    "ver": 1,
    "vin_sz": 1,
    "vout_sz": 2
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
  skel, err := bcy.DecodeTX("01000000011fdd692992f3d3859d01762119313a655177a59226ede31adc0199b28c73c450000000006a4730440220298915c785165233e2f19192bbbb9b8c3fed6d064279cb8968af6698c783b875022037287134f9f0aaf84429ba9bb50f1efcb4cedcfbd34407c285134e068234432c012102b17f04f5fa1bd44315ef33f8759822a302a2ba7fa28c56a50fac0441a341b96affffffff02400d0300000000001976a91446c7681dcacaee1ea3f57eced37755d1d09e070188accc550100000000001976a91490d969679032cc1af6a69b4161778f12e90cb13588ac00000000")
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", skel)
}

//Result from `go run`:
//{BlockHash: BlockHeight:-1 Hash:55d6ef290774f5428b66a0a27e46a8c37032c33bc15abce4791d56a2f8adb773 Addresses:[C6LBLb668ZTS4okXhjtzapFWAdnFjH7A4i ByaXmP5k14WRBFLXArfCcqcpAHrUQRy3vL] Total:{neg:false abs:[287500]} Fees:{neg:false abs:[12500]} Size:225 Preference:low RelayedBy:147.253.37.187 Received:2020-09-18 18:55:32.88345921 +0000 UTC Confirmed:0001-01-01 00:00:00 +0000 UTC Confirmations:0 Confidence:0 Ver:1 LockTime:0 DoubleSpend:false DoubleOf: ReceiveCount:0 VinSize:1 VoutSize:2 Hex: DataProtocol: ChangeAddress: NextInputs: NextOutputs: Inputs:[{PrevHash:50c4738cb29901dc1ae3ed2692a57751653a31192176019d85d3f3922969dd1f OutputIndex:0 OutputValue:300000 Addresses:[C6LBLb668ZTS4okXhjtzapFWAdnFjH7A4i] Sequence:4294967295 ScriptType:pay-to-pubkey-hash Script:4730440220298915c785165233e2f19192bbbb9b8c3fed6d064279cb8968af6698c783b875022037287134f9f0aaf84429ba9bb50f1efcb4cedcfbd34407c285134e068234432c012102b17f04f5fa1bd44315ef33f8759822a302a2ba7fa28c56a50fac0441a341b96a Age:495036 WalletName:}] Outputs:[{SpentBy: Value:{neg:false abs:[200000]} Addresses:[ByaXmP5k14WRBFLXArfCcqcpAHrUQRy3vL] ScriptType:pay-to-pubkey-hash Script:76a91446c7681dcacaee1ea3f57eced37755d1d09e070188ac DataHex: DataString:} {SpentBy: Value:{neg:false abs:[87500]} Addresses:[C6LBLb668ZTS4okXhjtzapFWAdnFjH7A4i] ScriptType:pay-to-pubkey-hash Script:76a91490d969679032cc1af6a69b4161778f12e90cb13588ac DataHex: DataString:}]}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\DecodeRawTransactionEndpoint.php

$txClient = new TXClient($apiContext);
$hexRawTx = "01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000";
$tx = $txClient->decode($hexRawTx);

{
  "block_height":-1,
  "hash":"4e6dfb1415b4fba5bd257c129847c70fbd4e45e41828079c4a282680528f3a50",
  "addresses":[
    "1NsbjF7mCvxa9sdCwRyeZ8qqvWMWuAyjtg",
    "19iz3x4d4Wm4rgav33KmbuRjots6r1K9Wg"
  ],
  "total":4988000,
  "fees":0,
  "size":226,
  "preference":"low",
  "relayed_by":"",
  "received":"0001-01-01T00:00:00Z",
  "ver":1,
  "lock_time":0,
  "double_spend":false,
  "vin_sz":1,
  "vout_sz":2,
  "confirmations":0,
  "inputs":[
    {
      "prev_hash":"c8ea8b221580ebb2f1cabc8b40797bffec742b97c82a329df96d93121db43519",
      "output_index":0,
      "script":"483045022100921fc36b9110942...44f5ab0bc940c105045e2cc77e618044",
      "output_value":0,
      "sequence":4294967295,
      "script_type":"empty",
      "age":0
    }
  ],
  "outputs":[
    {
      "value":1000000,
      "script":"76a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac",
      "addresses":[
        "19iz3x4d4Wm4rgav33KmbuRjots6r1K9Wg"
      ],
      "script_type":"pay-to-pubkey-hash"
    },
    {
      "value":3988000,
      "script":"76a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac",
      "addresses":[
        "1NsbjF7mCvxa9sdCwRyeZ8qqvWMWuAyjtg"
      ],
      "script_type":"pay-to-pubkey-hash"
    }
  ]
}
```

We also offer the ability to decode raw transactions without sending propagating them to the network; perhaps you want to double-check another client library or confirm that another service is sending proper transactions.

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/decode | POST | {"tx":$TXHEX} | [TX](#tx)

$TXHEX is a hex-encoded raw representation of your transaction, for example:

`01000000011935b41d12936df99d322ac8972b74ecff7b79408bbccaf1b2eb8015228beac8000000006b483045022100921fc36b911094280f07d8504a80fbab9b823a25f102e2bc69b14bcd369dfc7902200d07067d47f040e724b556e5bc3061af132d5a47bd96e901429d53c41e0f8cca012102152e2bb5b273561ece7bbe8b1df51a4c44f5ab0bc940c105045e2cc77e618044ffffffff0240420f00000000001976a9145fb1af31edd2aa5a2bbaa24f6043d6ec31f7e63288ac20da3c00000000001976a914efec6de6c253e657a9d5506a78ee48d89762fb3188ac00000000`

If it succeeds, you'll receive your decoded [TX](#tx) object.

## Decode Transaction Witness To Sign Endpoint

 ```shell
 curl -d '{"witness_tosign_tx":"01000000d0fe2d3feb5f3a5b6a99c244f21924b0249e2bc65c5745bdb618b1799066a80a3bb13029ce7b1f559ef5e747fcac439f1455a2ec7c5f09b72290795e706650448b3658589c258fb4b44180f158ae73d4dbaaf26d64c43a97a5cd78d9577f786d010000001976a9141b80a938a784abe7cdee8fdaf95f5f7e0b8cd0b988ac40420f0000000000ffffffff5fa4537c4c565583f8d32a36c3510808d38acc0ad18cc7e637be6523ebb39ff30000000001000000"}' https://api.blockcypher.com/v1/bcy/test/txs/decodeWitnessToSign?token=YOURTOKEN

 {
   "version": 1,
   "hash_prevouts": "0aa8669079b118b6bd45575cc62b9e24b02419f244c2996a5b3a5feb3f2dfed0",
   "hash_sequence": "445066705e799022b7095f7ceca255149f43acfc47e7f59e551f7bce2930b13b",
   "outpoint": "6d787f57d978cda5973ac4646df2aadbd473ae58f18041b4b48f259c5858368b",
   "outpoint_index": 1,
   "script_code": "76a9141b80a938a784abe7cdee8fdaf95f5f7e0b8cd0b988ac",
   "value": 1000000,
   "sequence": 4294967295,
   "hash_outputs": "f39fb3eb2365be37e6c78cd10acc8ad3080851c3362ad3f88355564c7c53a45f",
   "lock_time": 0,
   "sighash_type": 1
 }
 ```

 ```javascript
 var decodewitnesstosigntx = {
   witness_tosign_tx: "01000000d0fe2d3feb5f3a5b6a99c244f21924b0249e2bc65c5745bdb618b1799066a80a3bb13029ce7b1f559ef5e747fcac439f1455a2ec7c5f09b72290795e706650448b3658589c258fb4b44180f158ae73d4dbaaf26d64c43a97a5cd78d9577f786d010000001976a9141b80a938a784abe7cdee8fdaf95f5f7e0b8cd0b988ac40420f0000000000ffffffff5fa4537c4c565583f8d32a36c3510808d38acc0ad18cc7e637be6523ebb39ff30000000001000000"
 };
 $.post('https://api.blockcypher.com/v1/bcy/test/txs/decodeWitnessToSign', JSON.stringify(decodewitnesstosigntx))
   .then(function(d) {console.log(d)});
 > {
 >   "version": 1,
 >   "hash_prevouts": "0aa8669079b118b6bd45575cc62b9e24b02419f244c2996a5b3a5feb3f2dfed0",
 >   "hash_sequence": "445066705e799022b7095f7ceca255149f43acfc47e7f59e551f7bce2930b13b",
 >   "outpoint": "6d787f57d978cda5973ac4646df2aadbd473ae58f18041b4b48f259c5858368b",
 >   "outpoint_index": 1,
 >   "script_code": "76a9141b80a938a784abe7cdee8fdaf95f5f7e0b8cd0b988ac",
 >   "value": 1000000,
 >   "sequence": 4294967295,
 >   "hash_outputs": "f39fb3eb2365be37e6c78cd10acc8ad3080851c3362ad3f88355564c7c53a45f",
 >   "lock_time": 0,
 >   "sighash_type": 1
 > }
 ```

 This endpoint allows you to decode the `tosign_tx` only in the case of the spending of a native segwit input (P2WPKH).
 This allows you to double check the which input you are spending and the value transfered.

 Resource | Method | Request Object | Return Object
 -------- | ------ | -------------- | -------------
 /txs/decodeWitnessToSign | POST | {"witness_tosign_tx":$WITNESSTOSIGNTXHEX} | [WitnessToSignTx](#witnesstosigntx)

 $WITNESSTOSIGNTXHEX is a hex-encoded raw given by the [#creating-transactions](#creating-transactions) when `includeToSignTx` is to `true`, for example:

 `01000000d0fe2d3feb5f3a5b6a99c244f21924b0249e2bc65c5745bdb618b1799066a80a3bb13029ce7b1f559ef5e747fcac439f1455a2ec7c5f09b72290795e706650448b3658589c258fb4b44180f158ae73d4dbaaf26d64c43a97a5cd78d9577f786d010000001976a9141b80a938a784abe7cdee8fdaf95f5f7e0b8cd0b988ac40420f0000000000ffffffff5fa4537c4c565583f8d32a36c3510808d38acc0ad18cc7e637be6523ebb39ff30000000001000000`

 If it succeeds, you'll receive your decoded [WitnessToSignTx](#witnesstosigntx) object.


## Multisig Transactions

```shell
{
  "inputs": [{"addresses": [sourceAddr]}],
  "outputs": [{
    "addresses"   : [pubkey1, pubkey2, pubkey3],
    "script_type" : "multisig-2-of-3",
    "value"       : 250000
  }]
}
```

```javascript
{
  "inputs": [{"addresses": [sourceAddr]}],
  "outputs": [{
    "addresses"   : [pubkey1, pubkey2, pubkey3],
    "script_type" : "multisig-2-of-3",
    "value"       : 250000
  }]
}
```

```ruby
# Create a custom payload, then use the function:
# BlockCypher.transaction_new_custom(payload)
# Sign your data in the returned TXSkeleton, then use:
# BlockCypher.transaction_send_custom(your_signed_tx_skeleton)
require 'JSON'
payload = JSON.parse('{ "inputs": [{"addresses": [sourceAddr]}],
  "outputs": [{
    "addresses"   : [pubkey1, pubkey2, pubkey3],
    "script_type" : "multisig-2-of-3",
    "value"       : 250000
  }]
}')
```

```go
package main

import "github.com/blockcypher/gobcy"

func main() {
  //note the change to BlockCypher Testnet
  bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
  //use TempMultiTX to set up multisig
  temptx := gobcy.TempMultiTX("sourceAddr", "", 25000, 2, []string{"pubkey1", "pubkey2", "pubkey3"})
  //Then follow the New/Send two-step process with this temptx as the input
}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\CreateTxToFundMultisigAddrWithBuilderEndpoint.php
// Builder classes are optional, see CreateTxToFundMultisigAddrEndpoint.php to see a sample using only base API classes.

$input = TXInputBuilder::aTXInput()
    ->addAddress("n3D2YXwvpoPg8FhcWpzJiS3SvKKGD8AXZ4")
    ->build();

$output = TXOutputBuilder::aTXOutput()
    ->withScryptType("multisig-2-of-3")
    ->withValue(1000)
    ->addAddress("03798be8162d7d6bc5c4e3b236100fcc0dfee899130f84c97d3a49faf83450fd81")
    ->addAddress("03dd9f1d4a39951013b4305bf61887ada66439ab84a9a2f8aca9dc736041f815f1")
    ->addAddress("03c8e6e99c1d0b42120d5cf40c963e5e8048fd2d2a184758784a041a9d101f1f02")
    ->build();

$tx = TXBuilder::aTX()
    ->addTXInput($input)
    ->addTXOutput($output)
    ->build();

$txClient = new TXClient($apiContext);
$txSkeleton = $txClient->create($tx);
```

Multisignature transactions are made simple by the method described in the [Creating Transactions](#creating-transactions) section, but they deserve special mention. In order to use them, you first need to fund a multisignature address. You use the `/txs/new` endpoint as before, but instead of the **outputs** **addresses** array containing public addresses, it instead contains the public keys associated with the new address. In addition, you must select a **script_type** of *mutlisig-n-of-m*, where *n* and *m* are numbers (e.g., *multisig-2-of-3*). The code example demonstrates how the partially filled [TX request object](#tx) would appear.

After you've set up your request object, you send to `/txs/new` and receive a partially filled [TXSkeleton](#txskeleton) as before, but with data to sign from the source address. Sign this data and include the public key(s) of the source address---as demonstrated in the [Creating Transactions](#creating-transactions)---then send along to the `/txs/send` endpoint. If it returns with an HTTP Status Code 201, then your multisignature address (via a *pay-to-script-hash* address) is funded.

<aside class="notice">
If you only need a <i>pay-to-script-hash</i> address corresponding to N-of-M multisig and don't want---or need---to fund it immediately, you should use the comparatively easier <a href="#generate-multisig-address-endpoint">Generate Multisig Address Endpoint.</a>
</aside>

### Spending Multisig Funds

```shell
{
  "inputs": [{
    "addresses"   : [pubkey1, pubkey2, pubkey3],
    "script_type" : "multisig-2-of-3"
  }],
  "outputs": [{
    "addresses" : [destAddr],
    "value"     : 150000
  }]
}
```

```javascript
{
  "inputs": [{
    "addresses"   : [pubkey1, pubkey2, pubkey3],
    "script_type" : "multisig-2-of-3"
  }],
  "outputs": [{
    "addresses" : [destAddr],
    "value"     : 150000
  }]
}
```

```ruby
# Create a custom payload, then use the function:
# BlockCypher.transaction_new_custom(payload)
# Sign your data in the returned TXSkeleton, then use:
# BlockCypher.transaction_send_custom(your_signed_tx_skeleton)
require 'JSON'
payload = JSON.parse('{ "inputs": [{
    "addresses"   : [pubkey1, pubkey2, pubkey3],
    "script_type" : "multisig-2-of-3"
  }],
  "outputs": [{
    "addresses" : [destAddr],
    "value"     : 150000
  }] }')
```

```go
package main

import "github.com/blockcypher/gobcy"

func main() {
  //note the change to BlockCypher Testnet
  bcy := gobcy.API{"YOURTOKEN", "bcy", "test"}
  //use TempMultiTX to set up multisig
  temptx := gobcy.TempMultiTX("", "destAddr", 25000, 2, []string{"pubkey1", "pubkey2", "pubkey3"})
  //Then follow the New/Send two-step process with this temptx as the input
}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\CreateTxToSpendMultisigFundsWithBuilderEndpoint.php
// Builder classes are optional, see CreateTxToSpendMultisigFunds.php to see a sample using only base API classes.

$input = TXInputBuilder::aTXInput()
    ->addAddress("03798be8162d7d6bc5c4e3b236100fcc0dfee899130f84c97d3a49faf83450fd81")
    ->addAddress("03dd9f1d4a39951013b4305bf61887ada66439ab84a9a2f8aca9dc736041f815f1")
    ->addAddress("03c8e6e99c1d0b42120d5cf40c963e5e8048fd2d2a184758784a041a9d101f1f02")
    ->withScryptType("multisig-2-of-3")
    ->build();

$output = TXOutputBuilder::aTXOutput()
    ->addAddress("n3D2YXwvpoPg8FhcWpzJiS3SvKKGD8AXZ4")
    ->withValue(1000)
    ->build();

$tx = TXBuilder::aTX()
    ->addTXInput($input)
    ->addTXOutput($output)
    ->build();

$txClient = new TXClient($apiContext);
$txSkeleton = $txClient->create($tx);
```

Once funded, you might want to programmatically spend the money in the address at some point. Here the process is similar, but with the inputs and outputs reversed. As you can see in the code sample, you need to provide the public keys within the **inputs** **addresses** array, and the corresponding **script_type** of *multisig-n-of-m* (e.g., *multisig-2-of-3*). Then you follow the same process of sending to `/txs/new` and getting an array of data to be signed.

Each party can send their signed data individually to `/txs/send` and we can correlate the signatures to the public keys; once we have enough signatures we'll propagate the transaction. Or you can send all needed signatures alongside ordered public keys with a single call to `/txs/send`.

If you send all signatures in a single call, and you're spending inputs locked by a *multisig-n-of-m* **script_type**, remember that you'll need *n* signatures for every element in the [TXSkeleton's](#txskeleton) **tosign** array. For example, let's say `/txs/new` returns two elements in the **tosign** array, corresponding to two *multisig-2-of-3* inputs locked by *pubkey1*, *pubkey2*, and *pubkey3:*

`{...,"tosign":[data1,data2],...}`

Then you'd need to return each piece of data signed twice, preserving order between signatures and pubkeys, resulting in four elements for the **signatures** and **pubkeys** arrays:

`{...,"tosign":[data1,data2], "signatures":[data1sig1,data1sig2,data2sig1,data2sig2], "pubkeys":[pubkey1,pubkey2,pubkey1,pubkey2],...}`

## Data Endpoint

<aside class="warning">
Due to congestion on the Bitcoin network and the drastic increase in miner fees, data embedding has become uneconomical. As a result, our data endpoint isn't supported on bitcoin anymore. To continue embedding data on the bitcoin blockchain, use our <a href="#creating-transactions">transaction API</a> to build transactions with an OP_RETURN output instead.
</aside>

```shell
# Embedding (Hash) Data
curl -d '{"data":"6cbe5d6c75bc36879b5fd32e29b325e288599d31edc76327c7889d407985e2aa"}' https://api.blockcypher.com/v1/btc/main/txs/data?token=YOURTOKEN

{
  "data": "6cbe5d6c75bc36879b5fd32e29b325e288599d31edc76327c7889d407985e2aa",
  "encoding": "hex",
  "token": "YOURTOKEN",
  "hash": "87d494918559a14ea6238ba0e25fb07bffb08716f4a55e25f32ea95a148c85d1"
}

# Embedding String Data
curl -d '{"data":"I am the walrus", "encoding":"string"}' https://api.blockcypher.com/v1/btc/main/txs/data?token=YOURTOKEN

{
  "data": "I am the walrus",
  "encoding": "string",
  "token": "YOURTOKEN",
  "hash": "cb6974e0fd57c91b70403e85ef48c840eecdca4804dfc4897b1321d5328e4f18"
}
```

```python
# Embedding (Hash) Data
>>> from blockcypher import embed_data
>>> embed_data(to_embed='6cbe5d6c75bc36879b5fd32e29b325e288599d31edc76327c7889d407985e2aa', api_key='YOUR_TOKEN')
{
  "data": "6cbe5d6c75bc36879b5fd32e29b325e288599d31edc76327c7889d407985e2aa",
  "encoding": "hex",
  "token": "YOUR_TOKEN",
  "hash": "87d494918559a14ea6238ba0e25fb07bffb08716f4a55e25f32ea95a148c85d1"
}

# Embedding String Data
>>> from blockcypher import embed_data
>>> embed_data(to_embed='I am the walrus', api_key='YOUR_TOKEN', data_is_hex=False)
{
  "data": "I am the walrus",
  "encoding": "string",
  "token": "YOUR_TOKEN",
  "hash": "cb6974e0fd57c91b70403e85ef48c840eecdca4804dfc4897b1321d5328e4f18"
}
```

```php
<?php
// Run on console:
// php -f .\sample\transaction-api\DataEndpoint.php

$nullDataClient = new NullDataClient($apiContext);
$nullData = $nullDataClient->embedString('***BlockCypher Data Endpoint Test***'); // max 40 bytes

{
  "data": "***BlockCypher Data Endpoint Test***",
  "encoding": "string",
  "token": "c0afcccdde5081d6429de37d16166ead",
  "hash": "cb6974e0fd57c91b70403e85ef48c840eecdca4804dfc4897b1321d5328e4f18"
}
```

Some of the most interesting blockchain applications involve embedding data through *null-data* (also known as OP_RETURN) output scripts with transactions. While you can certainly use our [guided transaction process](#creating-transactions) and customize your script output, there's a simpler way: through our Data Endpoint. All you need is the data you want to embed (up to 40 bytes, inclusive) and we'll handle the rest.

<aside class="notice">
Embedding data into a blockchain is not free, but we're covering the cost for now, leveraging our <a href="#microtransaction-api">Microtransaction</a> infrastructure. Consequently, in order for us to keep track of fees, we require that all calls to the Data Endpoint use a token. You can <a href="http://accounts.blockcypher.com/">register for one here.</a>
</aside>

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/txs/data | POST | [NullData](#nulldata) | [NullData](#nulldata)

In your request object, simply include the **data** you want to embed. By default, you needn't set an **encoding** if your data is hex-encoded (like a hash, the typical use case for *null-data*). That said, if you want to embed plaintext messages in the blockchain, you can set **encoding** to *string*. If successful, the return object will include your original request along with the **hash** of the transaction containing your **data** as a *null-data* output.

<aside class="warning">
While some miners have a patch allowing 80 byte <i>null-data</i> outputs on Bitcoin's blockchain, it is not yet ubiquitous. The 40 byte limit is a hard ceiling for now; pay special attention if you're embedding special characters with plaintext encoding. If your <b>data</b> is over 40 bytes, the endpoint will return an error.
</aside>

To view the data output on the blockchain, simply [query the transaction](#transaction-hash-endpoint) via the **hash** returned, and check the *null-data* [output](#txoutput) within the returned transaction. Please keep in mind that it will always be represented by hex-encoding on the blockchain, even if you selected *string* as your data **encoding**; to see it as your original plaintext, you have to convert it client-side.

## Transaction Propagation Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/txs/5cad31bd8baf5d10ecdc275193f878226bb51f549c2357658f3dd0d7c5402a7b/propagation?token=YOURTOKEN

{
  "transaction": "5cad31bd8baf5d10ecdc275193f878226bb51f549c2357658f3dd0d7c5402a7b",
  "first_location": {
    "latitude": 43.171,
    "longitude": -71.8229
  },
  "first_city": "Henniker",
  "first_country": "United States",
  "aggregated_origin": {
    "latitude": 44.283110947350366,
    "longitude": -69.82911485365968
  },
  "aggregated_origin_radius": 1023192,
  "first_received": "2016-08-30T21:25:37.427Z"
}
```

By operating a well-connected node, we collect a lot of information about how transactions propagate; for example, our [Confidence Factor](#confidence-factor) relies on this connectivity. With this endpoint, you can leverage our connectivity to get an approximation of a transaction's location of origin.

Resource | Method | Return Object
-------- | ------ | -------------
/txs/$TXHASH/propagation | GET | ***Described Below***

TXHASH is a *string* representing the hex-encoded transaction hash you're interested in querying for propagation information. The return object is described below:

Attribute | Type | Description
--------- | ---- | -----------
**transaction** | *string* | The hash of the transaction you queried.
**first_location** | *Object* | An object containing **latitude** and **longitude** *floats* representing the first location to broadcast this transaction to BlockCypher.
**first_city** | *string* | ***Optional*** The name of the city closest to the **first_location**. If no nearby city can be found, this is not returned.
**first_country** | *string* | The name of the country containing the **first_location.**
**aggregated_origin** | *Object* | An object containing **latitude** and **longitude** *floats* representing BlockCypher's best guess of likely origin of this transaction, based on the radius of the smallest circle containing the first peer from which we detect this **transaction**, the average of the first five peers, and the average of the first ten peers.
**aggregated_origin_radius** | *integer* | The radius (in meters) of the smallest circle containing the first peer from which we detect this **transaction**, the average of the first five peers, and the average of the first ten peers. In a general sense, this represents an approximate confidence interval in our calculated **aggregated_origin**; the smaller the radius, the more confidence in our **aggregated_origin** assessment.
**first_received** | [*time*](https://tools.ietf.org/html/rfc3339) | The timestamp when BlockCypher first received this transaction.

<aside class="notice">
Want more information about transaction propagation? (e.g., IP addresses) Reach out to us to discuss <a href="mailto:contact@blockcypher.com">custom endpoints.</a>
</aside>

### Transaction Propagation WebSocket

You can get a live view of unconfirmed transaction propagation analysis on Bitcoin by connecting to our Transaction Propagation WebSocket, at the following address:

`wss://socket.blockcypher.com/v1/btc/main?token=$TOKEN/txs/propagation`

As soon as an unconfirmed transaction hits 10 peers, we send the object described above through this WebSocket. For more WebSocket implementation details, check [Using WebSockets](#using-websockets).
