# Blockchain API

The first component---and highest level---of the BlockCypher API allows you to query general information about blockchain and blocks [based on the coin/chain resource](#restful-resources) you've selected for your endpoints.

If you're new to blockchains, you can think of the blockchain itself as an immutable, distributed ledger. Each block in the blockchain is like a "page" in the ledger containing information about transactions between parties. A great place to start understanding the mechanics behind blockchains is the [original Bitcoin whitepaper.](http://bitcoin.org/bitcoin.pdf)

## Chain Endpoint

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
    "hash": "0000000000000000097ebad5b89f15d46bc2bb36423af7cbcaa83a87f3f2a178",
    "height": 379892,
    "high_fee_per_kb": 51535,
    "last_fork_hash": "000000000000000004d09e31247aa0efbba89cb35c146f96f7150d76c810d395",
    "last_fork_height": 379357,
    "latest_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000097ebad5b89f15d46bc2bb36423af7cbcaa83a87f3f2a178",
    "low_fee_per_kb": 24372,
    "medium_fee_per_kb": 29490,
    "name": "BTC.main",
    "peer_count": 896,
    "previous_hash": "00000000000000000bbd491173b40160b74e212650b7324bc74b33367d0e0a6b",
    "previous_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000bbd491173b40160b74e212650b7324bc74b33367d0e0a6b",
    "time": "datetime.datetime(2015, 10, 21, 14, 11, 21, 603114, tzinfo=tzutc())",
    "unconfirmed_count": 78701
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
  chain, err := btc.GetChain()
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", chain)
}

//Result from `go run`:
//{Name:BTC.main Height:648800 Hash:00000000000000000006f4157b3fa48928e09f5d8a6696604df43f1187547354 Time:2020-09-17 20:39:41.770732359 +0000 UTC PrevHash:000000000000000000017aa67661ee1764357965e04b58b7b789be67af2b5acc PeerCount:1045 HighFee:86603 MediumFee:61014 LowFee:49127 UnconfirmedCount:17910 LastForkHeight:644543 LastForkHash:0000000000000000000ac8d61492ab76dab7451373c6eaa6803ec0244f623395}
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

General information about a blockchain is available by GET-ing the [base resource](#restful-resources).

Resource | Method | Return Object
-------- | ------ | -------------
/ | GET | [Blockchain](#blockchain)

The returned object contains a litany of information about the blockchain, including its height, the time/hash of the latest block, and more.

For more detailed information about the data returned, check the [Blockchain](#blockchain) object.

## Block Hash Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412

{
"hash": "0000000000000000189bba3564a63772107b567...",
"height": 294322,
"chain": "BTC.main",
"total": 1146652915,
"fees": 130999,
"ver": 2,
"time": "2014-04-05T07:49:18Z",
"received_time": "2014-04-05T07:49:18Z",
"bits": 419486617,
"nonce": 1225187768,
"n_tx": 10,
"prev_block": "0000000000000000ced0958bd27720b71d3...",
"mrkl_root": "359d624d37aee1efa5662b7f5dbc390e996d...",
"txids": [
  "32b3b86e40d996b1f281e24e8d4af2ceacbf874c403836...",
  "1579f716359ba1a207f70248135f5e5fadf539be1dcf53...",
  ...,
],
"depth": 61793,
"prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
"tx_url": "https://api.blockcypher.com/v1/btc/main/txs/"
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412')
  .then(function(d) {console.log(d)});
> {
> "hash": "0000000000000000189bba3564a63772107b567...",
> "height": 294322,
> "chain": "BTC.main",
> "total": 1146652915,
> "fees": 130999,
> "ver": 2,
> "time": "2014-04-05T07:49:18Z",
> "received_time": "2014-04-05T07:49:18Z",
> "bits": 419486617,
> "nonce": 1225187768,
> "n_tx": 10,
> "prev_block": "0000000000000000ced0958bd27720b71d3...",
> "mrkl_root": "359d624d37aee1efa5662b7f5dbc390e996d...",
> "txids": [
>   "32b3b86e40d996b1f281e24e8d4af2ceacbf874c403836...",
>   "1579f716359ba1a207f70248135f5e5fadf539be1dcf53...",
>   ...,
> ],
> "depth": 61793,
> "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
> "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/"
> }
```

```ruby
> block_cypher.blockchain_block("0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412")
=> {"hash"=>"0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412",
 "height"=>294322,
 "chain"=>"BTC.main",
 "total"=>1146652915,
 "fees"=>130999,
 "ver"=>2,
 "time"=>"2014-04-05T07:49:18Z",
 "received_time"=>"2014-04-05T07:49:18Z",
 "bits"=>419486617,
 "nonce"=>1225187768,
 "n_tx"=>10,
 "prev_block"=>"0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
 "mrkl_root"=>"359d624d37aee1efa5662b7f5dbc390e996d561afc8148e8d716cf6ad765a952",
 "txids"=>
  ["32b3b86e40d996b1f281e24e8d4af2ceacbf874c4038369cc21baa807409b277",
   "1579f716359ba1a207f70248135f5e5fadf539be1dcf5300613aedcb6577d287",
   "dd1f183348eb41eaaa9ecf8012f9cca3ecbae41a6349f0cc4bfd2b1a497fa3d0",
   "749d12ccd180968b82aef4c271ca4effdf981d9b5d12523264457c9d4e6fa78e",
   "c4fe2ee16b8e3067d3d95caf7944011f4959781288b807df8bf853b7f80ed97c",
   "5a2114675265522d2b7ce8a7874cfa7a22ccc3fb6566a8599d6432c6805b1b5f",
   "077d851c8240671de80caa8be9f5285201c08a70edc5a45a9cd35fe7eaebf5e1",
   "6202cc55fbd9130e065c9294a5b2e061c26f3d2c8df56c32da605d9f183103f9",
   "ad3e7aa1c33f1d3e1c105d94f7b1542808da07bbe66b9621b050104a85dbf650",
   "36cc61016b9d1bd69768666f287db1edaa9b292fb442f152af7099305677230e"],
 "depth"=>66905,
 "prev_block_url"=>"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
 "tx_url"=>"https://api.blockcypher.com/v1/btc/main/txs/"}
```

```python
>>> from blockcypher import get_block_overview
>>> get_block_overview('0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412')
{
    "bits": 419486617,
    "chain": "BTC.main",
    "depth": 85570,
    "fees": 130999,
    "hash": "0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412",
    "height": 294322,
    "mrkl_root": "359d624d37aee1efa5662b7f5dbc390e996d561afc8148e8d716cf6ad765a952",
    "n_tx": 10,
    "nonce": 1225187768,
    "prev_block": "0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
    "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
    "received_time": "datetime.datetime(2014, 4, 5, 7, 49, 18, 0, tzinfo=tzutc())",
    "relayed_by": "",
    "time": "datetime.datetime(2014, 4, 5, 7, 49, 18, 0, tzinfo=tzutc())",
    "total": 1146652915,
    "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
    "txids": [
        "32b3b86e40d996b1f281e24e8d4af2ceacbf874c4038369cc21baa807409b277",
        "1579f716359ba1a207f70248135f5e5fadf539be1dcf5300613aedcb6577d287",
        "dd1f183348eb41eaaa9ecf8012f9cca3ecbae41a6349f0cc4bfd2b1a497fa3d0",
        "749d12ccd180968b82aef4c271ca4effdf981d9b5d12523264457c9d4e6fa78e",
        "c4fe2ee16b8e3067d3d95caf7944011f4959781288b807df8bf853b7f80ed97c",
        "5a2114675265522d2b7ce8a7874cfa7a22ccc3fb6566a8599d6432c6805b1b5f",
        "077d851c8240671de80caa8be9f5285201c08a70edc5a45a9cd35fe7eaebf5e1",
        "6202cc55fbd9130e065c9294a5b2e061c26f3d2c8df56c32da605d9f183103f9",
        "ad3e7aa1c33f1d3e1c105d94f7b1542808da07bbe66b9621b050104a85dbf650",
        "36cc61016b9d1bd69768666f287db1edaa9b292fb442f152af7099305677230e"
    ],
    "ver": 2
}
# note that you can batch blocks with blockcypher.get_blocks_overview()
```

```go
package main

import (
  "fmt"

  "github.com/blockcypher/gobcy"
)

func main() {
  btc := gobcy.API{"YOURTOKEN", "btc", "main"}
  block, err := btc.GetBlock(0, "0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412", nil)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", block)
}

//Result from `go run`:
//{Hash:0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412 Height:294322 Depth:354479 Chain:BTC.main Total:{neg:false abs:[1146652915]} Fees:{neg:false abs:[130999]} Ver:2 Time:2014-04-05 07:49:18 +0000 UTC ReceivedTime:2014-04-05 07:49:18 +0000 UTC RelayedBy: Bits:419486617 Nonce:1225187768 NumTX:10 PrevBlock:0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0 MerkleRoot:359d624d37aee1efa5662b7f5dbc390e996d561afc8148e8d716cf6ad765a952 TXids:[32b3b86e40d996b1f281e24e8d4af2ceacbf874c4038369cc21baa807409b277 1579f716359ba1a207f70248135f5e5fadf539be1dcf5300613aedcb6577d287 dd1f183348eb41eaaa9ecf8012f9cca3ecbae41a6349f0cc4bfd2b1a497fa3d0 749d12ccd180968b82aef4c271ca4effdf981d9b5d12523264457c9d4e6fa78e c4fe2ee16b8e3067d3d95caf7944011f4959781288b807df8bf853b7f80ed97c 5a2114675265522d2b7ce8a7874cfa7a22ccc3fb6566a8599d6432c6805b1b5f 077d851c8240671de80caa8be9f5285201c08a70edc5a45a9cd35fe7eaebf5e1 6202cc55fbd9130e065c9294a5b2e061c26f3d2c8df56c32da605d9f183103f9 ad3e7aa1c33f1d3e1c105d94f7b1542808da07bbe66b9621b050104a85dbf650 36cc61016b9d1bd69768666f287db1edaa9b292fb442f152af7099305677230e] NextTXs:}
```

```php
<?php
// Run on console:
// php -f .\sample\block-api\BlockHashEndpoint.php

$blockClient = new BlockClient($apiContext);
$block = $blockClient->get('0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412');

{
  "hash":"0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328",
  "height":293000,
  "chain":"BTC.main",
  "total":288801092067,
  "fees":6635337,
  "ver":2,
  "time":"2014-03-29T01:29:19Z",
  "received_time":"2014-03-29T01:29:19Z",
  "bits":419486617,
  "nonce":704197304,
  "n_tx":373,
  "prev_block":"0000000000000000b358b3b54788547080f49ed52392c2ed32a241951e2c9d5f",
  "mrkl_root":"5edf6a7e92e65d32843a79227042c215b875675fb92ff9613c90d6964fb069cd",
  "txids":[
    "7f00b52d075b3596cbd37ba5418640ace14a22ed6c5d154c4db5dd80e049b800",
    "a90d9bc04a4e6be53e0ddc47b043625a0bdc849ef3bc05f551bc05336de3a87c",
    ...
  ],
  "depth":67602,
  "prev_block_url":"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000b358b3b54788547080f49ed52392c2ed32a241951e2c9d5f",
  "tx_url":"https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids":"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328?txstart=20\u0026limit=20"
}
```

If you want more data on a particular block, you can use the Block Hash endpoint.

Resource | Method | Return Object
-------- | ------ | -------------
/blocks/$BLOCK_HASH | GET | [Block](#block)

Flag | Type | Effect
---- | ---- | ------
**txstart** | *integer* | Filters response to only include transaction hashes after **txstart** in the block.
**limit** | *integer* | Filters response to only include a maximum of **limit** transactions hashes in the block. Maximum value allowed is 500.

BLOCK_HASH is a *string* representing the hash of the block you're interested in querying, for example:

`0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412`

The returned object contains information about the block, including its height, the total amount of satoshis transacted within it, the number of transactions in it, transaction hashes listed in the canonical order in which they appear in the block, and more. For more detail on the data returned, check the [Block](#block) object.

## Block Height Endpoint

```shell
curl 'https://api.blockcypher.com/v1/btc/main/blocks/294322?txstart=1&limit=1'

{
"hash": "0000000000000000189bba3564a63772107b567...",
"height": 294322,
"chain": "BTC.main",
"total": 1146652915,
"fees": 130999,
"ver": 2,
"time": "2014-04-05T07:49:18Z",
"received_time": "2014-04-05T07:49:18Z",
"bits": 419486617,
"nonce": 1225187768,
"n_tx": 10,
"prev_block": "0000000000000000ced0958bd27720b71d3...",
"mrkl_root": "359d624d37aee1efa5662b7f5dbc390e996d...",
"txids": [
  "1579f716359ba1a207f70248135f5e5fadf539be1dcf53...",
],
"depth": 61793,
"prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
"tx_url": "https://api.blockcypher.com/v1/btc/main/txs/"
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/blocks/294322?txstart=1&limit=1')
  .then(function(d) {console.log(d)});
> {
> "hash": "0000000000000000189bba3564a63772107b567...",
> "height": 294322,
> "chain": "BTC.main",
> "total": 1146652915,
> "fees": 130999,
> "ver": 2,
> "time": "2014-04-05T07:49:18Z",
> "received_time": "2014-04-05T07:49:18Z",
> "bits": 419486617,
> "nonce": 1225187768,
> "n_tx": 10,
> "prev_block": "0000000000000000ced0958bd27720b71d3...",
> "mrkl_root": "359d624d37aee1efa5662b7f5dbc390e996d...",
> "txids": [
>   "1579f716359ba1a207f70248135f5e5fadf539be1dcf53...",
> ],
> "depth": 61793,
> "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
> "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/"
> }
```

```ruby
> block_cypher.blockchain_block("294322", {"txstart"=>1,"limit"=>1})
=> {"hash"=>"0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412",
 "height"=>294322,
 "chain"=>"BTC.main",
 "total"=>1146652915,
 "fees"=>130999,
 "ver"=>2,
 "time"=>"2014-04-05T07:49:18Z",
 "received_time"=>"2014-04-05T07:49:18Z",
 "bits"=>419486617,
 "nonce"=>1225187768,
 "n_tx"=>10,
 "prev_block"=>"0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
 "mrkl_root"=>"359d624d37aee1efa5662b7f5dbc390e996d561afc8148e8d716cf6ad765a952",
 "txids"=>["1579f716359ba1a207f70248135f5e5fadf539be1dcf5300613aedcb6577d287"],
 "depth"=>66905,
 "prev_block_url"=>"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
 "tx_url"=>"https://api.blockcypher.com/v1/btc/main/txs/",
 "next_txids"=>"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412?txstart=2&limit=1"}
```

```python
>>> from blockcypher import get_block_overview
>>> get_block_overview('294322', txn_limit=1, txn_offset=1)
{
    "bits": 419486617,
    "chain": "BTC.main",
    "depth": 85574,
    "fees": 130999,
    "hash": "0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412",
    "height": 294322,
    "mrkl_root": "359d624d37aee1efa5662b7f5dbc390e996d561afc8148e8d716cf6ad765a952",
    "n_tx": 10,
    "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412?txstart=2&limit=1",
    "nonce": 1225187768,
    "prev_block": "0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
    "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0",
    "received_time": "datetime.datetime(2014, 4, 5, 7, 49, 18, 0, tzinfo=tzutc())",
    "relayed_by": "",
    "time": "datetime.datetime(2014, 4, 5, 7, 49, 18, 0, tzinfo=tzutc())",
    "total": 1146652915,
    "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
    "txids": [
        "1579f716359ba1a207f70248135f5e5fadf539be1dcf5300613aedcb6577d287"
    ],
    "ver": 2
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
  //setting txstart to 1 and limit to 1
	params := make(map[string]string)
	params["txstart"] = "1"
	params["limit"] = "1"
	block, err := btc.GetBlock(294322, "", params)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", block)
}

//Result from `go run`:
//{Hash:0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412 Height:294322 Depth:354479 Chain:BTC.main Total:{neg:false abs:[1146652915]} Fees:{neg:false abs:[130999]} Ver:2 Time:2014-04-05 07:49:18 +0000 UTC ReceivedTime:2014-04-05 07:49:18 +0000 UTC RelayedBy: Bits:419486617 Nonce:1225187768 NumTX:10 PrevBlock:0000000000000000ced0958bd27720b71d32c5847e40660aaca39f33c298abb0 MerkleRoot:359d624d37aee1efa5662b7f5dbc390e996d561afc8148e8d716cf6ad765a952 TXids:[1579f716359ba1a207f70248135f5e5fadf539be1dcf5300613aedcb6577d287] NextTXs:https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000189bba3564a63772107b5673c940c16f12662b3e8546b412?txstart=2&limit=1}
```

```php
<?php
// Run on console:
// php -f .\sample\block-api\BlockHeightEndpoint.php

$blockClient = new BlockClient($apiContext);
$params = array(
    'txstart' => 1,
    'limit' => 1,
);
$block = $blockClient->get('293000', $params);

{
  "hash":"0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328",
  "height":293000,
  "chain":"BTC.main",
  "total":288801092067,
  "fees":6635337,
  "ver":2,
  "time":"2014-03-29T01:29:19Z",
  "received_time":"2014-03-29T01:29:19Z",
  "bits":419486617,
  "nonce":704197304,
  "n_tx":373,
  "prev_block":"0000000000000000b358b3b54788547080f49ed52392c2ed32a241951e2c9d5f",
  "mrkl_root":"5edf6a7e92e65d32843a79227042c215b875675fb92ff9613c90d6964fb069cd",
  "txids":[
    "a90d9bc04a4e6be53e0ddc47b043625a0bdc849ef3bc05f551bc05336de3a87c"
  ],
  "depth":67602,
  "prev_block_url":"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000b358b3b54788547080f49ed52392c2ed32a241951e2c9d5f",
  "tx_url":"https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids":"https://api.blockcypher.com/v1/btc/main/blocks/0000000000000000c504bdea36e531d8089d324f2d936c86e3274f97f8a44328?txstart=2\u0026limit=1"
}
```

You can also query for information on a block using its height, using the same resource but with a different variable type.

Resource | Method | Return Object
-------- | ------ | -------------
/blocks/$BLOCK_HEIGHT | GET | [Block](#block)

Flag | Type | Effect
---- | ---- | ------
**txstart** | *integer* | Filters response to only include transaction hashes after **txstart** in the block.
**limit** | *integer* | Filters response to only include a maximum of **limit** transactions hashes in the block. Maximum value allowed is 500.

BLOCK_HEIGHT is an *integer* representing the height of the block you're interested in querying, for example:

`294322`

As above, the returned object contains information about the block, including its hash, the total amount of satoshis transacted within it, the number of transactions in it, transaction hashes listed in the canonical order in which they appear in the block, and more. For more detail on the data returned, check the [Block](#block) object.

<aside class="warning">
With recent blocks, $BLOCK_HEIGHT is not always a unique identifier, due to the possibility of soft forks and the nature of the consensus model with blockchains. If you're querying blocks with <b>depth above 10</b> (i.e., there are more than 10 blocks ahead of your target) the height should be a safe identifier.
</aside>

## Feature Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/feature/bip65?token=YOURTOKEN

{
  "name": "bip65",
  "state": "EXCLUSIVE",
  "last_transition_height": 388380,
  "last_transition_hash": "000000000000000009f886db2c7c12a497603e86378bace3ead93d350be3f38c"
}
```

Resource | Method
-------- | ------
/feature/$NAME | GET

If you're curious about the adoption of upgrade features on a blockchain, you can use this endpoint to get some information about its state on the network. For example, for bip65 on bitcoin, you could check its state via this URL: `https://api.blockcypher.com/v1/btc/main/feature/bip65?token=YOURTOKEN`. Generally speaking, for bitcoin, this will follow the form of tracking bipXX (where XX = a number), but the list of features we're tracking is always changing.
