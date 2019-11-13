# Wallet API

The Wallet API allows you to group multiple addresses under a single name. It only holds public address information and never requires any private keys.

<aside class="notice">
Don't be confused: this Wallet API has nothing to do with private key management. The best way to think of the Wallet API is a "Set of Public Addresses to Query Together" API, but that name refused to fit into any of our marketing materials.
</aside>

A normal Wallet can be created, deleted, and have addresses added and removed. The Wallet itself can have any custom name as long as it does not start with the standard address prefix (1 or 3 for Bitcoin).

## Hierarchical Deterministic (HD) Wallets

We also offer support for HD Wallets, which make it easy to manage multiple addresses under a single name. All HD wallet addresses are derived from a single seed. Please see [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) for more background on HD wallets.

HD Wallets can be created, deleted, and have new addresses generated. However, unlike normal Wallets, addresses cannot be removed.

When creating a wallet, one can optionally include one or more "subchain" indexes. These subchains can later be referenced when generating new addresses or sending txs. If none are provided in wallet creation, the wallet will derive & use addresses straight from the given extended pubkey. If no index is given when using the wallet with other APIs, it defaults to using the wallet's first (sub) chain.

In BIP32 notation, the wallet layout is m/0, m/1, ... and m/**i**/0, m/**i**/1, ... for each subchain **i** if the wallet has subchains. For example, the path of the fourth address generated is m/3 for a non-subchain wallet. The path of the fourth address at subchain index two is m/2/3. Note that this is different from the default BIP32 wallet layout.

If you want to use [BIP32 default wallet layout](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#The_default_wallet_layout) you can submit the extended public key of m/0' (which can only be derived from your master private key) with subchain indexes = [0, 1]. Subchain index 0 represents the external chain (of account 0) and will discover all k keypairs that look like: m/0'/0/k. Subchain index 1 represents the internal chain (of account 0) and will discover all k keypairs in m/0'/1/k.

If you want to use [BIP 44](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) layout (for BTC), you can submit the extended public key of m/44'/0'/0'. (which can only be derived from your master private key) with subchain indexes = [0, 1]. Subchain index 0 represents the external chain (of account 0) and will discover all k keypairs in m/44'/0'/0'/0/k. Subchain index 1 represents the internal chain (of account 0) and will discover all k keypairs in m/44'/0'/0'/1/k.

If you want to use [BIP 84](https://github.com/bitcoin/bips/blob/master/bip-0084.mediawiki) layout (for BTC), you can submit the extended public key of m/84'/0'/0'. (which can only be derived from your master private key) with subchain indexes = [0, 1]. Subchain index 0 represents the external chain (of account 0) and will discover all k keypairs in m/84'/0'/0'/0/k. Subchain index 1 represents the internal chain (of account 0) and will discover all k keypairs in m/84'/0'/0'/1/k.

If an address ahead of current addresses listed in an HD Wallet receives a transaction, it will be added, along with any addresses between the new address and the last used one. We limit looking ahead to 20 addresses; if more than 20 addresses are skipped for the next payment to an HD Wallet, it won't be detected/added to the HD Wallet.

<aside class="notice">
When creating a BIP84 HD wallet, be sure to submit the extended public key with the correct prefix. For example <code>zpub</code> for Bitcoin Mainnet, <code>vpub</code> for Bitcoin Testnet, <code>vpub</code> for BlockCypher Testnet...
</aside>

## Using Wallets

Both HD Wallets and normal Wallets can be leveraged by the [Address API](#address-api), just by using their $NAME instead of $ADDRESS. They can also be used with [Events](#events-and-hooks) and with the [Transaction API](#transaction-api). In general, using a wallet instead of an address in an API will have the effect of [batching the set of addresses](#batching) contained in the wallet.

<aside class="notice">
You are required to authenticate with your <a href="#rate-limits-and-tokens">user token</a> when using a Wallet or HD Wallet in any API endpoint. You can register <a href="http://accounts.blockcypher.com/">for a token here.</a>
</aside>

The following code examples should be considered serially; that is to say, the results will appear as if each API call were done sequentially. Also, $NAME is a *string* representing the name of your wallet, for example:

`alice`

As you'll see in the examples, if you're using HD Wallets, take care to use the appropriate resource (e.g. `/wallets/hd` instead of `/wallets` ).

<aside class="warning">
HD Wallets and Wallets share the same namespace under a particular coin/chain. For example, under a single token, you cannot have both an HD Wallet and normal Wallet named "alice" on Bitcoin Main.
</aside>

## Create Wallet Endpoint

```shell
# normal wallet
curl -d '{"name": "alice","addresses": ["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"]}' https://api.blockcypher.com/v1/btc/main/wallets?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "alice",
"addresses": [
	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
]}

# hd wallet
curl -d '{"name": "bob", "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8"}' https://api.blockcypher.com/v1/btc/main/wallets/hd?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "bob",
"hd": true,
"extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
"chains": [
	{"chain_addresses": [
		{
			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
			"path": "m/0"
		},
		{
			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
			"path": "m/1"
		},
		{
			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
			"path": "m/2"
		}
	]}
]}

# hd wallet BIP84
curl -d '{"name": "charlie", "extended_public_key": "vpub5bJX9NkmSDP33H5PxJXmehqyV7aDaVWxudzDW3kP4c5Z5KFEaUNtJ9CLebEiEssZJyZa7AE9zR5Q7nqPAmk6xgRpRvXRMKj6WPfBGBi3fR"}' https://api.blockcypher.com/v1/btc/test3/wallets/hd?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "charlie",
"hd": true,
"extended_public_key": "vpub5bJX9NkmSDP33H5PxJXmehqyV7aDaVWxudvzDW3kP4c5Z5KFEaUNtJ9CLebEiEssZJyZa7AE9zR5Q7nqPAmk6xgRpRvXRMKj6WPfBGBi3fR",
"chains": [
	{"chain_addresses": [
        {
          "address": "tb1q4a9vav7vq4gp7ca9emddr66dvk099vdhh5srhp",
          "path": "m/0"
        }
    ]}
]}

# hd wallet with subchains
curl -d '{"name": "catheryne", "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8", "subchain_indexes": [1, 3]}' https://api.blockcypher.com/v1/btc/main/wallets/hd?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "catheryne",
"hd": true,
"extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
"subchain_indexes": [ 1, 3 ],
"chains": [
	{
		"index": 1,
		"chain_addresses": []
	},
	{
		"index": 3,
		"chain_addresses": []
	}
]}
```

```javascript
//normal wallet
var data = {"name": "alice","addresses": ["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"]};
$.post('https://api.blockcypher.com/v1/btc/main/wallets?token=USERTOKEN', JSON.stringify(data))
  .then(function(d) {console.log(d)});
> {"token": "USERTOKEN",
> "name": "alice",
> "addresses": [
> 	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
> ]}

//hd wallet
var data = {"name": "bob", "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8" };
$.post('https://api.blockcypher.com/v1/btc/main/wallets/hd?token=USERTOKEN', JSON.stringify(data))
  .then(function(d) {console.log(d)});
> {"token": "YOURTOKEN",
> "name": "bob",
> "hd": true,
> "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
> "chains": [
> 	{"chain_addresses": [
> 		{
> 			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
> 			"path": "m/0"
> 		},
> 		{
> 			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
> 			"path": "m/1"
> 		},
> 		{
> 			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
> 			"path": "m/2"
> 		}
> 	]}
> ]}
```

```ruby
> block_cypher.wallet_create("alice",["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"])
=> {"token"=>"YOURTOKEN", "name"=>"alice", "addresses"=>["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"]}

# TODO: ruby hd wallet example
```

```python
# Regular wallet
>>> from blockcypher import create_wallet_from_address
>>> create_wallet_from_address(wallet_name='alice', address='1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e', api_key='YOUR_TOKEN')
{
    "addresses": [
        "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
    ], 
    "name": "alice", 
    "token": "YOUR_TOKEN"
}

# HD wallet
>>> from blockcypher import create_hd_wallet
>>> create_hd_wallet(wallet_name='bob', xpubkey='xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8', api_key='YOUR_TOKEN')
{
    "chains": [
        {
            "chain_addresses": [
                {
                    "address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc", 
                    "path": "m/0"
                }, 
                {
                    "address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT", 
                    "path": "m/1"
                }, 
                {
                    "address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb", 
                    "path": "m/2"
                }
            ]
        }
    ], 
    "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8", 
    "hd": True, 
    "name": "bob", 
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
	btc := gobcy.API{"YOURTOKEN", "btc", "main"}
	//normal wallet
	alice, err := btc.CreateWallet(gobcy.Wallet{"alice", []string{"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"}})
	//hdwallet
	bob, err := btc.CreateHDWallet(gobcy.HDWallet{Name: "bob", ExtPubKey: "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8"})
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Normal Wallet:%+v\nHDWallet: %+v\n", alice, bob)
}

//Result from `go run`:
//Normal Wallet:{Name:alice Addresses:[1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e]}
//HDWallet: {Name:bob ExtPubKey:xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8 SubchainIndexes:[] Chains:[{ChainAddr:[{Address:1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc Path:m/0 Public:} {Address:1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT Path:m/1 Public:} {Address:1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb Path:m/2 Public:}] Index:0}]}
```

```php
<?php
// normal wallet
// Run on console:
// php -f .\sample\wallet-api\CreateWalletEndpoint.php

// Create a new instance of Wallet object
$wallet = new Wallet();
$wallet->setName('alice');
$wallet->setAddresses(array(
    "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
));

$walletClient = new WalletClient($apiContext);
$createdWallet = $walletClient->create($wallet);

{
  "token":"c0afcccdde5081d6429de37d16166ead",
  "name":"alice",
  "addresses":[
    "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
  ]
}

// hd wallet
// Run on console:
// php -f .\sample\wallet-api\CreateHDWalletEndpoint.php

// Create a new instance of HDWallet object
$wallet = new HDWallet();
$wallet->setName('bob');
$wallet->setExtendedPublicKey('xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8');

$walletClient = new HDWalletClient($apiContext);
$createdWallet = $walletClient->create($wallet);

{"token": "c0afcccdde5081d6429de37d16166ead",
"name": "bob",
"hd": true,
"extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
"chains": [
	{"chain_addresses": [
		{
			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
			"path": "m/0"
		},
		{
			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
			"path": "m/1"
		},
		{
			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
			"path": "m/2"
		}
	]}
]}
```

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/wallets | POST | [Wallet](#wallet) | [Wallet](#wallet)
/wallets/hd | POST | [HDWallet](#hdwallet) | [HDWallet](#hdwallet)

This endpoint allows you to create a new wallet, by POSTing a partially filled out [Wallet](#wallet) or [HDWallet](#hdwallet) object, depending on the endpoint. 

For normal wallets, at minimum, you must include the **name** attribute and at least one public address in the **addresses** array.

For HD wallets, at minimum, you must include the **name** and the **extended_public_key** attributes. The encoding of the key is [documented here.](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#serialization-format) You can optionally include **subchain_indexes** to initialize the wallet with one or more subchains. If not given, the wallet will derive address straight from the given extended pubkey. See [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki#The_default_wallet_layout) for more info.

If successful, it will return the same [Wallet](#wallet) or [HDWallet](#hdwallet) object you requested, appended with your user token.

<aside class="warning">
If the named wallet already exists under your token, attempting to create a new wallet will return an error. As mentioned above, Wallets and HDWallets share the same namespace, so an error will be returned if you attempt to create a named HD Wallet that shares a name with a normal Wallet under your token.
</aside>

## List Wallets Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/wallets?token=YOURTOKEN
{
"wallet_names": [
		"alice",
		"bob",
		"catheryne"
	]
}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/wallets?token=YOURTOKEN')
  .then(function(d) {console.log(d)});
> {
> "wallet_names": [
> 		"alice"
> 		"bob",
> 		"catheryne"
> 	]
> }
```

```ruby
# TODO: ruby examples
```

```python
>>> from blockcypher import list_wallet_names
>>> list_wallet_names('YOUR_TOKEN')
{
    "wallet_names": [
        "alice",
        "bob",
        "catheryne"
    ]
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
	walletNames, err := btc.ListWallets()
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Wallets:%v\n", walletNames)
}

//Result from `go run`:
//Wallets: [alice catheryne bob]
```

```php
<?php
//TODO: php examples
```

Resource | Method | Return Object
-------- | ------ | -------------
/wallets | GET | {"wallet_names":$NAMEARRAY}

This endpoint returns a string array ($NAMEARRAY) of active wallet names (both normal and HD) under the token you queried. You can then query detailed information on individual wallets (via their names) by leveraging the [Get Wallet Endpoint.](#get-wallet-endpoint)

<aside class="notice">
If you're building a client-side application where your users might have access to your token, and your security model requires maintaining wallet name privacy, we can blacklist this endpoint for you. Just reach out to <a href="mailto:support@blockcypher.com">support@blockcypher.com</a> to let us know.
</aside>

## Get Wallet Endpoint

```shell
# normal wallet
curl https://api.blockcypher.com/v1/btc/main/wallets/alice?token=YOURTOKEN

{"token":"YOURTOKEN",
"name":"alice",
"addresses": [ "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"]}

# hd wallet
curl https://api.blockcypher.com/v1/btc/main/wallets/hd/bob?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "bob",
"hd": true,
"extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
"chains": [{
	"chain_addresses": [
		{
			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
			"path": "m/0"
		},
		{
			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
			"path": "m/1"
		},
		{
			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
			"path": "m/2"
		}
	]}
]}
```

```javascript
$.get('https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=YOURTOKEN')
  .then(function(d) {console.log(d)});
> {
> "token":"YOURTOKEN",
> "name":"alice",
> "addresses": [
> 	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
> ]}

//hd wallet
$.get('https://api.blockcypher.com/v1/btc/main/wallets/hd/bob?token=USERTOKEN')
  .then(function(d) {console.log(d)});
> {"token": "YOURTOKEN",
> "name": "bob",
> "hd": true,
> "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
> "chains": [
> 	{"chain_addresses": [
> 		{
> 			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
> 			"path": "m/0"
> 		},
> 		{
> 			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
> 			"path": "m/1"
> 		},
> 		{
> 			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
> 			"path": "m/2"
> 		}
> 	]}
> ]}
```

```ruby
> block_cypher.wallet_get("alice")
=> {"token"=>"YOURTOKEN", "name"=>"alice", "addresses"=>["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"]}

//TODO: hd wallet example
```

```python
# Regular wallet
>>> from blockcypher import get_wallet_addresses
>>> get_wallet_addresses(wallet_name='alice', api_key='YOUR_TOKEN')
{
    "addresses": [
        "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
    ], 
    "name": "alice", 
    "token": "YOUR_TOKEN"
}

# HD wallet
>>> from blockcypher import get_wallet_addresses
>>> get_wallet_addresses(wallet_name='bob', api_key='YOUR_TOKEN', is_hd_wallet=True)
{
    "chains": [
        {
            "chain_addresses": [
                {
                    "address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc", 
                    "path": "m/0"
                }, 
                {
                    "address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT", 
                    "path": "m/1"
                }, 
                {
                    "address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb", 
                    "path": "m/2"
                }
            ]
        }
    ], 
    "extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8", 
    "hd": True, 
    "name": "bob", 
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
	btc := gobcy.API{"YOURTOKEN", "btc", "main"}
	//normal wallet
	alice, err := btc.GetWallet("alice")
	//hdwallet
	bob, err := btc.GetHDWallet("bob")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Normal Wallet: %v\nHDWallet: %v\n", alice, bob)
}

//Result from `go run`:
//Normal Wallet: {alice [1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e]}
//HDWallet: {bob xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8 [] [{[{1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc m/0 } {1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT m/1 } {1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb m/2 }] 0}]}
```

```php
<?php
// normal wallet
// Run on console:
// php -f .\sample\wallet-api\GetWalletEndpoint.php

$walletClient = new WalletClient($apiContext);
$wallet = $walletClient->get('alice');

{
  "token":"c0afcccdde5081d6429de37d16166ead",
  "name":"alice",
  "addresses":[
    "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
  ]
}

// hd wallet
// Run on console:
// php -f .\sample\wallet-api\GetHDWalletEndpoint.php

$walletClient = new HDWalletClient($apiContext);
$wallet = $walletClient->get('bob');

{"token": "c0afcccdde5081d6429de37d16166ead",
"name": "bob",
"hd": true,
"extended_public_key": "xpub661MyMwAqRbcFtXgS5sYJABqqG9YLmC4Q1Rdap9gSE8NqtwybGhePY2gZ29ESFjqJoCu1Rupje8YtGqsefD265TMg7usUDFdp6W1EGMcet8",
"chains": [
	{"chain_addresses": [
		{
			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
			"path": "m/0"
		},
		{
			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
			"path": "m/1"
		},
		{
			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
			"path": "m/2"
		}
	]}
]}
```

Resource | Method | Return Object
-------- | ------ | -------------
/wallets/$NAME | GET | [Wallet](#wallet)
/wallets/hd/$NAME | GET | [HDWallet](#hdwallet)

This endpoint returns a [Wallet](#wallet) or [HDWallet](#hdwallet) based on its $NAME.

## Add Addresses to Wallet Endpoint

```shell
curl -d '{"addresses": ["13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"]}' https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "alice",
"addresses": [
	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e",
	"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
]}
```

```javascript
var data = {"addresses": ["13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"]};
$.post('https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=USERTOKEN', JSON.stringify(data))
  .then(function(d) {console.log(d)});
> {
>   "token": "USERTOKEN",
>   "name": "alice",
>   "addresses": [
>   	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e",
>   	"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
> ]}
```

```ruby
> block_cypher.wallet_add_addr("alice",["13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"])
=> {"token"=>"YOURTOKEN", "name"=>"alice", "addresses"=>["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e", "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"]}
```

```python
>>> from blockcypher import add_address_to_wallet
>>> add_address_to_wallet(wallet_name='alice', address='13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j', api_key='YOUR_TOKEN')
{
    "addresses": [
        "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e", 
        "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
    ], 
    "name": "alice", 
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
	btc := gobcy.API{"YOURTOKEN", "btc", "main"}
	//normal wallet
	alice, err := btc.AddAddrWallet("alice", []string{"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"}, false)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("%+v\n", alice)
}

//Result from `go run`:
//{Name:alice Addresses:[13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j 1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e]}
```

```php
<?php
// Run on console:
// php -f .\sample\wallet-api\AddAddressesToWalletEndpoint.php

$walletClient = new WalletClient($apiContext);
$addressList = AddressList::fromAddressesArray(array(
    "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
));
$wallet = $walletClient->addAddresses('alice', $addressList);

{
  "token":"c0afcccdde5081d6429de37d16166ead",
  "name":"alice",
  "addresses":[
    "1jr1rHMthQVMNSYswB9ExSvYn339fWMzn",
    "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
  ]
}
```

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/wallets/$NAME/addresses | POST | [Wallet](#wallet) | [Wallet](#wallet)

Flag | Type | Effect
---- | ---- | ------
**omitWalletAddresses** | *bool* | If **omitWalletAddresses** is *true*, the response will omit address information (useful to speed up the API call for larger wallets).

This endpoint allows you to add public addresses to the $NAME wallet, by POSTing a partially filled out [Wallet](#wallet) object. You only need to include the additional addresses in a new **addresses** array in the object. If successful, it will return the newly modified [Wallet](#wallet), including an up-to-date, complete listing of addresses.

<aside class="notice">
You cannot add custom addresses to HD Wallets, since they are all derived from your extended public key. You can always generate new addresses client-side, or using the <a href="#generate-address-in-wallet-endpoint">Generate Address in Wallet Endpoint below.</a>
</aside>

## Get Wallet Addresses Endpoint

```shell
#normal wallet
curl https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=YOURTOKEN

{"addresses": [
	"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j",
	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
]}

#hd wallet
curl https://api.blockcypher.com/v1/btc/main/wallets/hd/bob/addresses?token=YOURTOKEN

{"chains": [
	{"chain_addresses": [
		{
			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
			"path": "m/0"
		},
		{
			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
			"path": "m/1"
		},
		{
			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
			"path": "m/2"
		}]
	}
]}
```

```javascript
//normal wallet
$.get('https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=USERTOKEN')
  .then(function(d) {console.log(d)});
> {
> "addresses": [
> 	"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j",
> 	"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
> ]}

//hd wallet
$.get('https://api.blockcypher.com/v1/btc/main/wallets/hd/bob/addresses?token=USERTOKEN')
  .then(function(d) {console.log(d)});
> {"chains": [
> 	{"chain_addresses": [
> 		{
> 			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
> 			"path": "m/0"
> 		},
> 		{
> 			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
> 			"path": "m/1"
> 		},
> 		{
> 			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
> 			"path": "m/2"
> 		}]
> 	}
> ]}
```

```ruby
> block_cypher.wallet_get_addr("alice")
=> {"addresses"=>["13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j", "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"]}

# TODO: ruby hd example
```

```python
# Regular wallet
>>> from blockcypher import get_wallet_addresses
>>> get_wallet_addresses(wallet_name='alice', api_key='YOUR_TOKEN')
{
    "addresses": [
        "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j", 
        "1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"
    ]
}

# HD wallet
>>> from blockcypher import get_wallet_addresses
>>> get_wallet_addresses(wallet_name='alice', api_key='YOUR_TOKEN', is_hd_wallet=True)
{
    "chains": [
        {
            "chain_addresses": [
                {
                    "address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc", 
                    "path": "m/0"
                }, 
                {
                    "address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT", 
                    "path": "m/1"
                }, 
                {
                    "address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb", 
                    "path": "m/2"
                }
            ]
        }
    ]
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
	//normal wallet
	alice, err := btc.GetAddrWallet("alice", nil)
	//hdwallet
	bob, err := btc.GetAddrHDWallet("bob", nil)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Normal Wallet: %+v\nHD Wallet: %+v\n", alice, bob)
}

//Result from `go run`:
//Normal Wallet: [13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j 1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e]
//HD Wallet: {Name: ExtPubKey: SubchainIndexes:[] Chains:[{ChainAddr:[{Address:1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc Path:m/0 Public:} {Address:1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT Path:m/1 Public:} {Address:1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb Path:m/2 Public:}] Index:0}]}
```

```php
<?php
// normal wallet
// Run on console:
// php -f .\sample\wallet-api\WalletAddressesEndpoint.php

$walletClient = new WalletClient($apiContext);
$addressList = $walletClient->getWalletAddresses('alice');

{"addresses":[
    "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j",
    "1jr1rHMthQVMNSYswB9ExSvYn339fWMzn"
]}

// hd wallet
// Run on console:
// php -f .\sample\wallet-api\GetHDWalletAddressesEndpoint.php

$walletClient = new HDWalletClient($apiContext);
$addressList = $walletClient->getWalletAddresses('bob');

{"chains": [
	{"chain_addresses": [
		{
			"address": "1FHz8bpEE5qUZ9XhfjzAbCCwo5bT1HMNAc",
			"path": "m/0"
		},
		{
			"address": "1J8QDN1u7iDMbJktbqXPSrAqruNjkmRFmT",
			"path": "m/1"
		},
		{
			"address": "1MWNKnYfE2LVdvAzFUioF3F3JXFpRfDCQb",
			"path": "m/2"
		}]
	}
]}
```

Resource | Method | Return Object
-------- | ------ | -------------
/wallets/$NAME/addresses | GET | [Wallet](#wallet)
/wallets/hd/$NAME/addresses | GET | [HDChain](#hdchain)

Flag | Type | Effect
---- | ---- | ------
**used** | *bool* | Returns only used addresses if set to *true* and only unused if *false*. **Only valid on HD wallets.**
**zerobalance** | *bool* | Returns only addresses with zero balance if set to *true* and only addresses with non-zero balance if *false*. **Only valid on HD wallets.**


This endpoint returns a list of the addresses associated with the $NAME wallet. It returns the addresses in a partially filled out [Wallet](#wallet) which you'll find under the **addresses** attribute. For HD wallets it returns an [HDChain](#hd-chain) object.

## Remove Addresses from Wallet Endpoint

```shell
#pipe to grep to check status code
$ curl -X DELETE -Is https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=USERTOKEN&address=1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e | grep "HTTP/1.1"

HTTP/1.1 204 OK
```

```javascript
$.ajax({
  url: "https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses?token=USERTOKEN&address=1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e",
  method: "DELETE"
})
```

```ruby
> block_cypher.wallet_delete_addr("alice",["1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"])
=> nil
```

```python
>>> from blockcypher import remove_address_from_wallet
>>> remove_address_from_wallet(wallet_name='alice', address='1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e', api_key='YOUR_TOKEN')
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
	//normal wallet only
	err := btc.DeleteAddrWallet("alice", []string{"1JcX75oraJEmzXXHpDjRctw3BX6qDmFM8e"})
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Printf("Address deleted")
	}
}

//Result from `go run`:
//Address deleted
```

```php
<?php
// Run on console:
// php -f .\sample\wallet-api\RemoveAddressesFromWalletEndpoint.php

$walletClient = new WalletClient($apiContext);
// List of addresses to be removed from the wallet
$addressList = AddressList::fromAddressesArray(array(
    "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
));
$wallet = $walletClient->removeAddresses('alice', $addressList);

{
  "token":"c0afcccdde5081d6429de37d16166ead",
  "name":"alice",
  "addresses":[
    "1jr1rHMthQVMNSYswB9ExSvYn339fWMzn"
  ]
}
```

Resource | Method | Return Object
-------- | ------ | -------------
/wallets/$NAME/addresses?address=$ADDRESS | DELETE | *nil*

This endpoint allows you to delete an $ADDRESS associated with the $NAME wallet. As a reminder, you [can batch](#batching) multiple addresses by appending them with semicolons within the $ADDRESS URL parameter. If successful, it will return an HTTP 204 status code with no return object.

<aside class="notice">
You cannot remove addresses from HD Wallets.
</aside>

## Generate Address in Wallet Endpoint

```shell
curl -X POST https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses/generate?token=YOURTOKEN

{"token": "YOURTOKEN",
"name": "alice",
"addresses": [
	"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j",
	"14LcPtRSGjYb1s8kfxsVDbXvA7VYCmoFho"
],
"private": "6238efeb679d75ec3b1a43e76cc0ed33abdf56e30bb5bb95e4793134a7958609",
"public": "03e4f273521a30373a639f60da836f2308a5d53853ec18f903dd235c73e6e26e4a",
"address": "14LcPtRSGjYb1s8kfxsVDbXvA7VYCmoFho",
"wif": "KzWeDL7sysRay7pZUm6hQQLaDVjmN1jUZzeZuq6ru5FtN1RhPrLX"}
```

```javascript
$.post('https://api.blockcypher.com/v1/btc/main/wallets/alice/addresses/generate?token=USERTOKEN')
  .then(function(d) {console.log(d)});
> {
>   "token": "USERTOKEN",
>   "name": "alice",
>   "addresses": [
>   	"13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j",
>   	"14LcPtRSGjYb1s8kfxsVDbXvA7VYCmoFho"
>   ],
>   "private": "6238efeb679d75ec3b1a43e76cc0ed33abdf56e30bb5bb95e4793134a7958609",
>   "public": "03e4f273521a30373a639f60da836f2308a5d53853ec18f903dd235c73e6e26e4a",
>   "address": "14LcPtRSGjYb1s8kfxsVDbXvA7VYCmoFho",
>   "wif": "KzWeDL7sysRay7pZUm6hQQLaDVjmN1jUZzeZuq6ru5FtN1RhPrLX"
> }
```

```ruby
> block_cypher.wallet_gen_addr("alice")
=> {"token"=>"YOURTOKEN",
 "name"=>"alice",
 "addresses"=>["13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j", "1Bq7QRwfa36p5DGSe6UBy4PYmsEtQhpqC8"],
 "private"=>"f371f0a43f46a09ec2351fb1fc23450167d6031dbadef56f7174dc1e0285fed9",
 "public"=>"037318b43fc83f3af24b5d88c8e4e33ebbdea62c3a3f6eb3a9830e2bb1f6682108",
 "address"=>"1Bq7QRwfa36p5DGSe6UBy4PYmsEtQhpqC8",
 "wif"=>"L5NwFyFdHqfBYhaQDttym6b3tXeaKaH7cepgAJunWrkKY24NUCjb"}
```

```python
# Please use a python library such as https://github.com/blockcypher/blockcypher-python to securely generate an address client-side
```

```go
package main

import (
	"fmt"

	"github.com/blockcypher/gobcy"
)

func main() {
	btc := gobcy.API{"YOURTOKEN", "btc", "main"}
	//normal wallet only
	alice, addr, err := btc.GenAddrWallet("alice")
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Wallet: %+v\nNew Address: %+v\n", alice, addr)
}

//Result from `go run`:
//Wallet: {Name:alice Addresses:[13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j 14KLgDRAEHdk5ggRUDcqeZNfJHu2cCoU5D]}
//New Address: {Address:14KLgDRAEHdk5ggRUDcqeZNfJHu2cCoU5D Private:b8202b67710cef1990ca3dfe04e2bf5e3484e6d6a4725c46e86b7f8b3e4b3f20 Public:0226a990df828225f1fbb608313a4a08db75ddfbf73a5f9a5ed9da8d38c89923b2 Wif:L3PdKr1QRFD8uxfXrkTVUrd6LznocDpwgY1JQsFCpYTrzT4do3KJ PubKeys:[] ScriptType:}
```

```php
<?php
// Run on console:
// php -f .\sample\wallet-api\GenerateAddressInWalletEndpoint.php

$walletClient = new WalletClient($apiContext);
$walletGenerateAddressResponse = $walletClient->generateAddress('alice');

{
  "token":"c0afcccdde5081d6429de37d16166ead",
  "name":"alice",
  "addresses":[
    "1jr1rHMthQVMNSYswB9ExSvYn339fWMzn",
    "13cj1QtfW61kQHoqXm3khVRYPJrgQiRM6j"
  ],
  "private":"0d42eb9fa80301aa3a5c4e91d2a5c892b4fb99b10bc1f0d636d46ecba32d37e6",
  "public":"030b4780c12784456291191318b231dcce64c93f8eb211fa78eb7e95663009e414",
  "address":"1GuPv187EEUi3BDypdpMS7cmMuaVpWy6PM",
  "wif":"KwfVLLsRSgSC6pkDLkEmMn8fH5VFceaYT9B58Cu8QHZtZzJvnpYZ"
}
```

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/wallets/$NAME/addresses/generate | POST | *nil* | [Wallet](#wallet) + [AddressKeychain](#addresskeychain)

Flag | Type | Effect
---- | ---- | ------
**bech32** | *bool* | Whether or not to generate a p2wpkh bech32 address. Default is false.

This endpoint allows you to generate a new address associated with the $NAME wallet, similar to the [Generate Address Endpoint](#generate-address-endpoint). If successful, it will returned the newly modified [Wallet](#wallet) composed with an [AddressKeychain](#addresskeychain).

Only works for regular Wallets; for HD Wallets, use the [Derive Address](#derive-address-in-wallet-endpoint) endpoint specified below.

## Derive Address in Wallet Endpoint

```shell
curl -X POST https://api.blockcypher.com/v1/btc/main/wallets/hd/bob/addresses/derive?token=YOURTOKEN

{
"chains": [
	{"chain_addresses": [
		{
			"address": "19urzZsoBNsomSfj68PUUGLXDz261tK5Jo",
			"public": "02c85080e00080aa933f93a2718bba9f01fd6fdc8e4712a155849f5ba588666471",
			"path": "m/3"
		}
	]}
]}

#with subchain_index
curl -X POST 'https://api.blockcypher.com/v1/btc/main/wallets/hd/catheryne/addresses/derive?token=YOURTOKEN&subchain_index=1'

{
"chains": [
	{"index":1,
	"chain_addresses": [
    {
      "address": "1NwEtFZ6Td7cpKaJtYoeryS6avP2TUkSMh",
      "public": "029b393153a1ec68c7af3a98e88aecede3a409f27e698c090540098611c79e05b0",
      "path": "m/1/0"
    }
  ]}
]}
```

```javascript
//todo: javascript hd derive examples
```

```ruby
# todo: ruby hd derive examples
```

```python
>>> from blockcypher import derive_hd_address
>>> derive_hd_address(api_key='YOUR_TOKEN', wallet_name='bob', coin_symbol='btc')
{
    "chains": [
        {
            "chain_addresses": [
                {
                    "address": "19urzZsoBNsomSfj68PUUGLXDz261tK5Jo", 
                    "path": "m/3", 
                    "public": "02c85080e00080aa933f93a2718bba9f01fd6fdc8e4712a155849f5ba588666471"
                }
            ]
        }
    ]
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
	//hdwallet only
	bob, err := btc.DeriveAddrHDWallet("bob", map[string]string{"count": "1"})
	if err != nil {
		fmt.Println(err)
	}
	fmt.Printf("Parital HD Wallet: %+v\n", bob)
}

//Result from `go run`:
//Partial HD Wallet: {Name: ExtPubKey: SubchainIndexes:[] Chains:[{ChainAddr:[{Address:19urzZsoBNsomSfj68PUUGLXDz261tK5Jo Path:m/3 Public:02c85080e00080aa933f93a2718bba9f01fd6fdc8e4712a155849f5ba588666471}] Index:0}]}
```

```php
// Run on console:
// php -f .\sample\wallet-api\GenerateAddressInHDWalletEndpoint.php

$walletClient = new HDWalletClient($apiContext);
$hdWalletGenerateAddressResponse = $walletClient->generateAddress('bob');

// with subchain_index
// Run on console:
// php -f .\sample\wallet-api\GenerateAddressInHDWalletWithSubchainIndexEndpoint.php

$walletClient = new HDWalletClient($apiContexts['BTC.main']);
$params = array('subchain_index' => 1);
$hdWalletGenerateAddressResponse = $walletClient->deriveAddress('bob', $params);
```

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/wallets/hd/$NAME/addresses/derive | POST | *nil* | Partial [HDWallet](#hdwallet)

Flag | Type | Effect
---- | ---- | ------
**count** | *integer* | Number of addresses to derive. If not set, default is one address.
**subchain_index** | *integer* | Derives address(es) on this specific subchain. If not set, address will be generated on the first chain in the HD wallet.

This endpoint allows you to derive a new address (or multiple addresses) associated with the $NAME HD Wallet. If successful, it will return an [HDWallet](#hdwallet) **but only with the newly derived address(es)** represented in its **chains** field to limit the data transmitted; for the full address list after derivation, you can follow up this API call with the [Get Wallet Addresses Endpoint.](#get-wallet-addresses-endpoint)

Only works for HD Wallets; for regular wallets, use the [Generate Address](#generate-address-in-wallet-endpoint) in Wallet endpoint specified above.

## Delete Wallet Endpoint

```shell
# normal wallet, pipe to grep to check status code
curl -X DELETE -Is https://api.blockcypher.com/v1/btc/main/wallets/alice?token=YOURTOKEN | grep "HTTP/1.1"

HTTP/1.1 204 OK

# hd wallet
curl -X DELETE -Is https://api.blockcypher.com/v1/btc/main/wallets/hd/bob?token=YOURTOKEN | grep "HTTP/1.1"

HTTP/1.1 204 OK
```

```javascript
//normal wallet
$.ajax({
  url: "https://api.blockcypher.com/v1/btc/main/wallets/alice?token=USERTOKEN",
  method: "DELETE"
});

//hd wallet
$.ajax({
  url: "https://api.blockcypher.com/v1/btc/main/wallets/hd/bob?token=USERTOKEN",
  method: "DELETE"
});
```

```ruby
> block_cypher.wallet_delete("alice")
=> nil

# TODO: Ruby hd wallet example
```

```python
# Regular wallet
>>> from blockcypher import delete_wallet
>>> delete_wallet(wallet_name='alice', api_key='YOUR_TOKEN')
True

# HD wallet
>>> from blockcypher import delete_wallet
>>> delete_wallet(wallet_name='bob', api_key='YOUR_TOKEN', is_hd_wallet=True)
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
	//wallet
	err := btc.DeleteWallet("alice")
	//hdwallet
	err = btc.DeleteHDWallet("bob")
	if err != nil {
		fmt.Println(err)
	} else {
		fmt.Println("Alice and Bob Deleted")
	}
}

//Result from `go run`:
//Alice and Bob Deleted
```

```php
<?php
// normal wallet
// Run on console:
// php -f .\sample\wallet-api\DeleteWalletEndpoint.php

$walletClient = new WalletClient($apiContext);
$result = $walletClient->delete('alice');

// hd wallet
// Run on console:
// php -f .\sample\wallet-api\DeleteHDWalletEndpoint.php

$walletClient = new HDWalletClient($apiContext);
$result = $walletClient->delete('bob');
```

Resource | Method | Return Object
-------- | ------ | -------------
/wallets/$NAME | DELETE | *nil*
/wallets/hd/$NAME | DELETE | *nil*

This endpoint deletes the Wallet or HD Wallet with $NAME. If successful, it will return an HTTP 204 status code with no return object.

