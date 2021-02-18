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
curl https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db

{
  "hash": "00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
  "height": 671142,
  "chain": "BTC.main",
  "total": 2928942845838,
  "fees": 135268918,
  "size": 1448143,
  "vsize": 998941,
  "ver": 1073725440,
  "time": "2021-02-18T16:01:12Z",
  "received_time": "2021-02-18T16:02:17.341Z",
  "coinbase_addr": "",
  "relayed_by": "68.168.176.51:8333",
  "bits": 386736569,
  "nonce": 3270005482,
  "n_tx": 2025,
  "prev_block": "00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "mrkl_root": "5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
  "txids": [
    "39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b",
    "ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e",
    ...,
  ],
  "depth": 5,
  "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20\u0026limit=20"
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db')
  .then(function(d) {console.log(d)});
>{
> "hash": "00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
> "height": 671142,
> "chain": "BTC.main",
> "total": 2928942845838,
> "fees": 135268918,
> "size": 1448143,
> "vsize": 998941,
> "ver": 1073725440,
> "time": "2021-02-18T16:01:12Z",
> "received_time": "2021-02-18T16:02:17.341Z",
> "coinbase_addr": "",
> "relayed_by": "68.168.176.51:8333",
> "bits": 386736569,
> "nonce": 3270005482,
> "n_tx": 2025,
> "prev_block": "00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
> "mrkl_root": "5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
> "txids": [
>   "39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b",
>   "ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e",
>   "2d11ef988d9960a12f94d76525cc0001cf56bb9d3c7d47d405e8a40a008da653",
>   "17648e06bdb4a126b8ebdf763e21107a5e54b548c266b056dfcb4aae3343253e",
>   "7fbb8ce7ab5aba798eca6eab0076af2f5c621576d90a1cb471da62f4fca61df3",
>   "acf6a8781c6df77a437daa70fa2d51acf4b2fc70dc60cb3890b74901173ad125",
>   "cab8f38c42d82524767ad1c3443bf4c00cb6a4f9f5b19eb1c57e6c13123c2d67",
>   "dfb6dcce198a2d6415d3e322a0c3b6957d5a35e7cbdffeac209ed71cac9dd3f5",
>   "786c4e4bd5f456bf2c42eef8374cf41b5a4e6ccccda3043ea2612840a2523fc4",
>   "f28339e0247323e59c0d49d2480b8abb48fba4190e8f83bc37d1f095e29dd958",
>   "f7133abcf74884575296c1e2c3545080f88aa753f9542c88bffb79cdb34a4aa5",
>   "5db89c67ccaaa248028989c52bfa952eafc171d3e48fd15ba5dd0dfd368e1c06",
>   "dc4cc5acf9edb14cd9b8659d4a2405eae5d425867d861607f89826ffcb0f5459",
>   "fec4304054685c4a25f56b057ae3bab421fa998ca91812ad849c2518b3969288",
>   "e8441b85cc6036e40f745de4fd909b98507656d7e3a5762578ec50a03219099a",
>   "023c1577f21a7fdd428cc514726483c10039bd65afdf2f3da2b890f74350a073",
>   "389a04bfa0b809c33b147f24104d4a60581578da644282931abfff8d5a248679",
>   "ea31698b76076d4a2fef47d1c0dbc9df4539abf072e8bd29cefbf65daddaa6c5",
>   "1d52fbf97eaf19935edf21543f27455b7102290edc1d1f143fcb7b235196ddd3",
>   "60e5282257e1ebd742589a7122ff52aa7d78ef1821027301e84335e71ac19790"
> ],
> "depth": 5,
> "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
> "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
> "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20\u0026limit=20"
>}
```

```ruby
> block_cypher.blockchain_block("00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db","")
=> {
  "hash"=>"00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
  "height"=>671142,
  "chain"=>"BTC.main",
  "total"=>2928942845838,
  "fees"=>135268918,
  "size"=>1448143,
  "vsize"=>998941,
  "ver"=>1073725440,
  "time"=>"2021-02-18T16:01:12Z",
  "received_time"=>"2021-02-18T16:02:17.341Z",
  "coinbase_addr"=>"",
  "relayed_by"=>"68.168.176.51:8333",
  "bits"=>386736569, "nonce"=>3270005482,
  "n_tx"=>2025,
  "prev_block"=>"00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "mrkl_root"=>"5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
  "txids"=>["39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b",
    "ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e",
    "2d11ef988d9960a12f94d76525cc0001cf56bb9d3c7d47d405e8a40a008da653",
    "17648e06bdb4a126b8ebdf763e21107a5e54b548c266b056dfcb4aae3343253e",
    "7fbb8ce7ab5aba798eca6eab0076af2f5c621576d90a1cb471da62f4fca61df3",
    "acf6a8781c6df77a437daa70fa2d51acf4b2fc70dc60cb3890b74901173ad125",
    "cab8f38c42d82524767ad1c3443bf4c00cb6a4f9f5b19eb1c57e6c13123c2d67",
    "dfb6dcce198a2d6415d3e322a0c3b6957d5a35e7cbdffeac209ed71cac9dd3f5",
    "786c4e4bd5f456bf2c42eef8374cf41b5a4e6ccccda3043ea2612840a2523fc4",
    "f28339e0247323e59c0d49d2480b8abb48fba4190e8f83bc37d1f095e29dd958",
    "f7133abcf74884575296c1e2c3545080f88aa753f9542c88bffb79cdb34a4aa5",
    "5db89c67ccaaa248028989c52bfa952eafc171d3e48fd15ba5dd0dfd368e1c06",
    "dc4cc5acf9edb14cd9b8659d4a2405eae5d425867d861607f89826ffcb0f5459",
    "fec4304054685c4a25f56b057ae3bab421fa998ca91812ad849c2518b3969288",
    "e8441b85cc6036e40f745de4fd909b98507656d7e3a5762578ec50a03219099a",
    "023c1577f21a7fdd428cc514726483c10039bd65afdf2f3da2b890f74350a073",
    "389a04bfa0b809c33b147f24104d4a60581578da644282931abfff8d5a248679",
    "ea31698b76076d4a2fef47d1c0dbc9df4539abf072e8bd29cefbf65daddaa6c5",
    "1d52fbf97eaf19935edf21543f27455b7102290edc1d1f143fcb7b235196ddd3",
    "60e5282257e1ebd742589a7122ff52aa7d78ef1821027301e84335e71ac19790"],
  "depth"=>5,
  "prev_block_url"=>"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "tx_url"=>"https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids"=>"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20&limit=20"
  }
```

```python
>>> from blockcypher import get_block_overview
>>> get_block_overview('00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db')
{
 'hash': '00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db',
 'height': 671142,
 'chain': 'BTC.main',
 'total': 2928942845838,
 'fees': 135268918,
 'size': 1448143,
 'vsize': 998941,
 'ver': 1073725440,
 'time': datetime.datetime(2021, 2, 18, 16, 1, 12, tzinfo=tzlocal()),
 'received_time': datetime.datetime(2021, 2, 18, 16, 2, 17, 341000, tzinfo=tzlocal()),
 'coinbase_addr': '',
 'relayed_by': '68.168.176.51:8333',
 'bits': 386736569,
 'nonce': 3270005482,
 'n_tx': 2025,
 'prev_block': '00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d',
 'mrkl_root': '5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a',
 'txids': ['39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b',
  'ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e',
  '2d11ef988d9960a12f94d76525cc0001cf56bb9d3c7d47d405e8a40a008da653',
  '17648e06bdb4a126b8ebdf763e21107a5e54b548c266b056dfcb4aae3343253e',
  '7fbb8ce7ab5aba798eca6eab0076af2f5c621576d90a1cb471da62f4fca61df3',
  'acf6a8781c6df77a437daa70fa2d51acf4b2fc70dc60cb3890b74901173ad125',
  'cab8f38c42d82524767ad1c3443bf4c00cb6a4f9f5b19eb1c57e6c13123c2d67',
  'dfb6dcce198a2d6415d3e322a0c3b6957d5a35e7cbdffeac209ed71cac9dd3f5',
  '786c4e4bd5f456bf2c42eef8374cf41b5a4e6ccccda3043ea2612840a2523fc4',
  'f28339e0247323e59c0d49d2480b8abb48fba4190e8f83bc37d1f095e29dd958',
  'f7133abcf74884575296c1e2c3545080f88aa753f9542c88bffb79cdb34a4aa5',
  '5db89c67ccaaa248028989c52bfa952eafc171d3e48fd15ba5dd0dfd368e1c06',
  'dc4cc5acf9edb14cd9b8659d4a2405eae5d425867d861607f89826ffcb0f5459',
  'fec4304054685c4a25f56b057ae3bab421fa998ca91812ad849c2518b3969288',
  'e8441b85cc6036e40f745de4fd909b98507656d7e3a5762578ec50a03219099a',
  '023c1577f21a7fdd428cc514726483c10039bd65afdf2f3da2b890f74350a073',
  '389a04bfa0b809c33b147f24104d4a60581578da644282931abfff8d5a248679',
  'ea31698b76076d4a2fef47d1c0dbc9df4539abf072e8bd29cefbf65daddaa6c5',
  '1d52fbf97eaf19935edf21543f27455b7102290edc1d1f143fcb7b235196ddd3',
  '60e5282257e1ebd742589a7122ff52aa7d78ef1821027301e84335e71ac19790'],
 'depth': 1,
 'prev_block_url': 'https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d',
 'tx_url': 'https://api.blockcypher.com/v1/btc/main/txs/',
 'next_txids': 'https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20&limit=20'
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
  block, err := btc.GetBlock(0, "00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db", nil)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", block)
}

//Result from `go run`:
//{Hash:00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db Height:671142 Depth:9 Chain:BTC.main Total:{neg:false abs:[2928942845838]} Fees:{neg:false abs:[135268918]} Size:1448143 VirtualSize:998941 Ver:1073725440 Time:2021-02-18 16:01:12 +0000 UTC ReceivedTime:2021-02-18 16:02:17.341 +0000 UTC RelayedBy:68.168.176.51:8333 Bits:386736569 Nonce:3270005482 NumTX:2025 PrevBlock:00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d MerkleRoot:5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a TXids:[39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e 2d11ef988d9960a12f94d76525cc0001cf56bb9d3c7d47d405e8a40a008da653 17648e06bdb4a126b8ebdf763e21107a5e54b548c266b056dfcb4aae3343253e 7fbb8ce7ab5aba798eca6eab0076af2f5c621576d90a1cb471da62f4fca61df3 acf6a8781c6df77a437daa70fa2d51acf4b2fc70dc60cb3890b74901173ad125 cab8f38c42d82524767ad1c3443bf4c00cb6a4f9f5b19eb1c57e6c13123c2d67 dfb6dcce198a2d6415d3e322a0c3b6957d5a35e7cbdffeac209ed71cac9dd3f5 786c4e4bd5f456bf2c42eef8374cf41b5a4e6ccccda3043ea2612840a2523fc4 f28339e0247323e59c0d49d2480b8abb48fba4190e8f83bc37d1f095e29dd958 f7133abcf74884575296c1e2c3545080f88aa753f9542c88bffb79cdb34a4aa5 5db89c67ccaaa248028989c52bfa952eafc171d3e48fd15ba5dd0dfd368e1c06 dc4cc5acf9edb14cd9b8659d4a2405eae5d425867d861607f89826ffcb0f5459 fec4304054685c4a25f56b057ae3bab421fa998ca91812ad849c2518b3969288 e8441b85cc6036e40f745de4fd909b98507656d7e3a5762578ec50a03219099a 023c1577f21a7fdd428cc514726483c10039bd65afdf2f3da2b890f74350a073 389a04bfa0b809c33b147f24104d4a60581578da644282931abfff8d5a248679 ea31698b76076d4a2fef47d1c0dbc9df4539abf072e8bd29cefbf65daddaa6c5 1d52fbf97eaf19935edf21543f27455b7102290edc1d1f143fcb7b235196ddd3 60e5282257e1ebd742589a7122ff52aa7d78ef1821027301e84335e71ac19790] NextTXs:https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20&limit=20}
```

```php
<?php
// Run on console:
// php -f .\sample\block-api\BlockHashEndpoint.php

$blockClient = new BlockClient($apiContext);
$block = $blockClient->get('00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db');

{
  "hash": "00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
  "height": 671142,
  "chain": "BTC.main",
  "total": 2928942845838,
  "fees": 135268918,
  "size": 1448143,
  "vsize": 998941,
  "ver": 1073725440,
  "time": "2021-02-18T16:01:12Z",
  "received_time": "2021-02-18T16:02:17.341Z",
  "coinbase_addr": "",
  "relayed_by": "68.168.176.51:8333",
  "bits": 386736569,
  "nonce": 3270005482,
  "n_tx": 2025,
  "prev_block": "00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "mrkl_root": "5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
  "txids": [
    "39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b",
    "ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e",
    "2d11ef988d9960a12f94d76525cc0001cf56bb9d3c7d47d405e8a40a008da653",
    "17648e06bdb4a126b8ebdf763e21107a5e54b548c266b056dfcb4aae3343253e",
    "7fbb8ce7ab5aba798eca6eab0076af2f5c621576d90a1cb471da62f4fca61df3",
    "acf6a8781c6df77a437daa70fa2d51acf4b2fc70dc60cb3890b74901173ad125",
    "cab8f38c42d82524767ad1c3443bf4c00cb6a4f9f5b19eb1c57e6c13123c2d67",
    "dfb6dcce198a2d6415d3e322a0c3b6957d5a35e7cbdffeac209ed71cac9dd3f5",
    "786c4e4bd5f456bf2c42eef8374cf41b5a4e6ccccda3043ea2612840a2523fc4",
    "f28339e0247323e59c0d49d2480b8abb48fba4190e8f83bc37d1f095e29dd958",
    "f7133abcf74884575296c1e2c3545080f88aa753f9542c88bffb79cdb34a4aa5",
    "5db89c67ccaaa248028989c52bfa952eafc171d3e48fd15ba5dd0dfd368e1c06",
    "dc4cc5acf9edb14cd9b8659d4a2405eae5d425867d861607f89826ffcb0f5459",
    "fec4304054685c4a25f56b057ae3bab421fa998ca91812ad849c2518b3969288",
    "e8441b85cc6036e40f745de4fd909b98507656d7e3a5762578ec50a03219099a",
    "023c1577f21a7fdd428cc514726483c10039bd65afdf2f3da2b890f74350a073",
    "389a04bfa0b809c33b147f24104d4a60581578da644282931abfff8d5a248679",
    "ea31698b76076d4a2fef47d1c0dbc9df4539abf072e8bd29cefbf65daddaa6c5",
    "1d52fbf97eaf19935edf21543f27455b7102290edc1d1f143fcb7b235196ddd3",
    "60e5282257e1ebd742589a7122ff52aa7d78ef1821027301e84335e71ac19790"
  ],
  "depth": 5,
  "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20\u0026limit=20"
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
curl 'https://api.blockcypher.com/v1/btc/main/blocks/671142?txstart=1&limit=1'

{
  "hash": "00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
  "height": 671142,
  "chain": "BTC.main",
  "total": 2928942845838,
  "fees": 135268918,
  "size": 1448143,
  "vsize": 998941,
  "ver": 1073725440,
  "time": "2021-02-18T16:01:12Z",
  "received_time": "2021-02-18T16:02:17.341Z",
  "coinbase_addr": "",
  "relayed_by": "68.168.176.51:8333",
  "bits": 386736569,
  "nonce": 3270005482,
  "n_tx": 2025,
  "prev_block": "00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "mrkl_root": "5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
  "txids": [
    "39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b"
  ],
  "depth": 5,
  "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20\u0026limit=20"
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/blocks/671142?txstart=1&limit=1')
  .then(function(d) {console.log(d)});
>{
> "hash": "00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
> "height": 671142,
> "chain": "BTC.main",
> "total": 2928942845838,
> "fees": 135268918,
> "size": 1448143,
> "vsize": 998941,
> "ver": 1073725440,
> "time": "2021-02-18T16:01:12Z",
> "received_time": "2021-02-18T16:02:17.341Z",
> "coinbase_addr": "",
> "relayed_by": "68.168.176.51:8333",
> "bits": 386736569,
> "nonce": 3270005482,
> "n_tx": 2025,
> "prev_block": "00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
> "mrkl_root": "5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
> "txids": [
>   "39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b"
> ],
> "depth": 5,
> "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
> "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
> "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20\u0026limit=20"
>}
```

```ruby
> block_cypher.blockchain_block("671142", {"txstart"=>1,"limit"=>1})
=> {
  "hash"=>"00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db",
  "height"=>671142,
  "chain"=>"BTC.main",
  "total"=>2928942845838,
  "fees"=>135268918,
  "size"=>1448143,
  "vsize"=>998941,
  "ver"=>1073725440,
  "time"=>"2021-02-18T16:01:12Z",
  "received_time"=>"2021-02-18T16:02:17.341Z",
  "coinbase_addr"=>"",
  "relayed_by"=>"68.168.176.51:8333",
  "bits"=>386736569, "nonce"=>3270005482,
  "n_tx"=>2025,
  "prev_block"=>"00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "mrkl_root"=>"5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a",
  "txids"=>["39dec39382157569b809446037c6d5658d490e38907060112c16d8de49aeab0b"],
  "depth"=>5,
  "prev_block_url"=>"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d",
  "tx_url"=>"https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids"=>"https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=20&limit=20"
  }
```

```python
>>> from blockcypher import get_block_overview
>>> get_block_overview('671142', txn_limit=1, txn_offset=1)
{
  'hash': '00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db',
  'height': 671142,
  'chain': 'BTC.main',
  'total': 2928942845838,
  'fees': 135268918,
  'size': 1448143,
  'vsize': 998941,
  'ver': 1073725440,
  'time': datetime.datetime(2021, 2, 18, 16, 1, 12, tzinfo=tzlocal()),
  'received_time': datetime.datetime(2021, 2, 18, 16, 2, 17, 341000, tzinfo=tzlocal()),
  'coinbase_addr': '',
  'relayed_by': '68.168.176.51:8333',
  'bits': 386736569,
  'nonce': 3270005482,
  'n_tx': 2025,
  'prev_block': '00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d',
  'mrkl_root': '5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a',
  'txids': ['ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e'],
  'depth': 5,
  'prev_block_url': 'https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d',
  'tx_url': 'https://api.blockcypher.com/v1/btc/main/txs/',
  'next_txids': 'https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=2&limit=1'
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
	block, err := btc.GetBlock(671142, "", params)
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", block)
}

//Result from `go run`:
//{Hash:00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db Height:671142 Depth:9 Chain:BTC.main Total:{neg:false abs:[2928942845838]} Fees:{neg:false abs:[135268918]} Size:1448143 VirtualSize:998941 Ver:1073725440 Time:2021-02-18 16:01:12 +0000 UTC ReceivedTime:2021-02-18 16:02:17.341 +0000 UTC RelayedBy:68.168.176.51:8333 Bits:386736569 Nonce:3270005482 NumTX:2025 PrevBlock:00000000000000000001245ac74971793689be7924cd0b031302661e806deb0d MerkleRoot:5283c26fb8d64d2284ffda76af6cd584292b76f265932cd121ac16fb475d7a8a TXids:[ce0a8256a459ba14feb48931a2344ff7af3af4f9aa2b5acb795a1b0b9fd8806e] NextTXs:https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000003dc20b868d17121303308f6bba329302e75913f0790db?txstart=2&limit=1}
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
$block = $blockClient->get('671142', $params);

{
  "hash":"000000000000000000058b0392acad50a141c980a0137fa381eed4bb9da2266b",
  "height":671142,
  "chain":"BTC.main",
  "total":288801092067,
  "size": 1337178,
  "vsize": 999378,
  "ver": 939515904,
  "time": "2021-02-16T14:30:47Z",
  "received_time": "2021-02-16T14:30:50.082Z",
  "coinbase_addr": "",
  "relayed_by": "100.19.55.173:8333",
  "bits": 386736569,
  "nonce": 3721541004,
  "n_tx": 3238,
  "prev_block": "00000000000000000007d284fb11be7e6adcc81d05177f05ff5ba6b906f151d2",
  "mrkl_root": "67ebfe25f76df77fe4c46e9474a4bcdaeb71034da6fc3509fdd2c6d0817b9de8",
  "txids": [
    "67f0821545bbfd815d2fc37dd24b945b76dcebb7b78f7dafbe55c5d09418a63e"
  ],
  "depth": 292,
  "prev_block_url": "https://api.blockcypher.com/v1/btc/main/blocks/00000000000000000007d284fb11be7e6adcc81d05177f05ff5ba6b906f151d2",
  "tx_url": "https://api.blockcypher.com/v1/btc/main/txs/",
  "next_txids": "https://api.blockcypher.com/v1/btc/main/blocks/000000000000000000058b0392acad50a141c980a0137fa381eed4bb9da2266b?txstart=20\u0026limit=20"
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
