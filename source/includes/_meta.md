# Metadata API

Cryptocurrency addresses, transactions, and blocks are extremely powerful, but the labels they employ can be...cryptic. That's why we have a Metadata API, allowing both public and private key-value storage against addresses, transactions, and blocks.

## Private vs Public Metadata

The Metadata API supports both public and private key-value storage. In both cases, setting metadata requires use of a token (if you haven't already, you can [register for one here](https://accounts.blockcypher.com/)). **Public metadata is immutable**; once set, it cannot be modified or deleted. If you accidentally set public metadata and need it deleted, [contact us](mailto:support@blockcypher.com). Also, as implied by the name, it's openly accessible to the whole world---regardless of whether they have a token or not. Consequently, private metadata is associated and only accessible with your user token. The methods for interacting with metadata are outlined below.

## Get Metadata Endpoint

```shell
# Get Public Metadata (set by you or anyone else)
curl https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN

{"name":"silkroad","owner":"dpr"}

# Get Private Metadata (none set yet, returns empty)
curl https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN&private=true

{}
```

```javascript
//todo: JS examples
```

```ruby
#todo: ruby examples
```

```python
>>> from blockcypher import get_metadata

# Get Private Metadata (none set yet, returns empty)
>>> get_metadata(address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN', private=True)
{}

# Get Public Metadata (set by you or anyone else)
>>> get_metadata(address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN')
{
    "name": "silkroad",
    "owner": "dpr"
}

# Note: get_metadata also accepts 'tx_hash' and 'block_hash' as arguments (instead of 'address')
```

```php
<?php
//todo: php examples
```

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS/meta | GET | {"$KEY":"$VALUE",...}
/txs/$TXHASH/meta | GET | {"$KEY":"$VALUE",...}
/blocks/$BLOCKHASH/meta | GET | {"$KEY":"$VALUE",...}

Flag | Type | Effect
---- | ---- | ------
**private** | *bool* | If **private** is *true*, it will return privately stored metadata under your token. Otherwise, returns public metadata. If not set, it defaults to *false*, returning public data.

This endpoint retrieves metadata associated with a given $ADDRESS, $TXHASH, or $BLOCKHASH.

<aside class="notice">
Remember to set the <b>private</b> flag if you're querying private metadata underneath your token.
</aside>

## Put Metadata Endpoint

```shell
# Put Private Metadata, piping to grep to check 204 status code
curl -X PUT -d '{"satoshi":"nakamoto", "alice":"bob"}' -is 'https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN&private=true' | grep "HTTP/1.1"

HTTP/1.1 204 No Content

# Get Metadata Again
curl https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN&private=true

{"satoshi":"nakamoto", "alice":"bob"}

# Modify/Add Private Metadata
curl -X PUT -d '{"satoshi":"moto", "charlie":"tango"}' -is 'https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN&private=true' | grep "HTTP/1.1"

HTTP/1.1 204 No Content

# Get Metadata Again
curl https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN&private=true

{"satoshi":"moto", "alice":"bob", "charlie":"tango"}
```

```javascript
//todo: JS examples
```

```ruby
#todo: ruby examples
```

```python
>>> from blockcypher import put_metadata

# Put Private Metadata
>>> put_metadata(metadata_dict={"satoshi":"nakamoto", "alice":"bob"}, address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN', private=True)
True

# Get Metadata Again
>>> get_metadata(address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN', private=True)
{
    "alice": "bob",
    "satoshi": "nakamoto"
}

# Modify/Add Private Metadata
>>> put_metadata(metadata_dict={"satoshi":"moto", "charlie":"tango"}, address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN', private=True)
True

# Get Metadata Again
>>> get_metadata(address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN', private=True)

{
    "alice": "bob",
    "charlie": "tango",
    "satoshi": "moto"
}

# Note: put_metadata also accepts 'tx_hash' and 'block_hash' as arguments (instead of 'address')
```

```php
<?php
//todo: php examples
```

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/addrs/$ADDRESS/meta | PUT | {"$KEY":"$VALUE",...} | *nil*
/txs/$TXHASH/meta | PUT | {"$KEY":"$VALUE",...} | *nil*
/blocks/$BLOCKHASH/meta | PUT | {"$KEY":"$VALUE",...} | *nil*

Flag | Type | Effect
---- | ---- | ------
**private** | *bool* | If **private** is *true*, it will set/update privately stored metadata under your token. Otherwise, sets public metadata. If not set, it defaults to *false*, setting public metadata.

This endpoint sets or updates metadata (of the form {"$KEY1:$VALUE1,$KEY2:$VALUE2,...}") associated with a given $ADDRESS, $TXHASH, or $BLOCKHASH. If another key with the same name already exists under your private metadata store, supplying a new value will replace it. New key-value pairs will be added without replacing prexisting key-value pairs. If successful, it will return an HTTP Status Code 204 with no return object.

<aside class="warning">
If you set public metadata, <b>you cannot modify or delete it.</b> It's set there forever, and viewable to the world. If you accidentally set public metadata and need it deleted, <a href="mailto:support@blockcypher.com">contact us</a>.
</aside>

## Delete Metadata Endpoint

```shell
# Delete Private Metadata, pipes to grep to check for 204 status code
curl -X DELETE -Is https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN | grep "HTTP/1.1"

HTTP/1.1 204 No Content

# Get Private Metadata Again to Confirm Deletion
curl https://api.blockcypher.com/v1/btc/main/addrs/1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp/meta?token=YOURTOKEN&private=true

{}
```

```javascript
//todo: JS examples
```

```ruby
#todo: ruby examples
```

```python
>>> from blockcypher import delete_metadata
>>> delete_metadata(address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN')
True

# Get Private Metadata Again to Confirm Deletion
>>> get_metadata(address='1rundZJCMJhUiWQNFS5uT3BvisBuLxkAp', api_key='YOUR_TOKEN', private=True)
{}

# Note: delete_metadata also accepts 'tx_hash' and 'block_hash' as arguments (instead of 'address')
```

```php
<?php
//todo: php examples
```

Resource | Method | Return Object
-------- | ------ | -------------
/addrs/$ADDRESS/meta | DELETE | *nil*
/txs/$TXHASH/meta | DELETE | *nil*
/blocks/$BLOCKHASH/meta | DELETE | *nil*

This endpoint deletes all **private** metadata associated with a given $ADDRESS, $TXHASH, or $BLOCKHASH and your token.

<aside class="warning">
As a last friendly reminder, if you set public metadata, <b>you cannot delete it.</b> It's set there forever, and viewable to the world. This endpoint will only delete your <b>private</b> metadata. If you accidentally set public metadata and need it deleted, <a href="mailto:support@blockcypher.com">contact us</a>.
</aside>
