# Confidence Factor

One of the things that makes BlockCypher's API uniquely powerful is our ability to accurately predict the likelihood of an attempted double-spend succeeding against a transaction; this percentage probability is optionally included within the **confidence** attribute on any [unconfirmed transaction](#tx) returned by the [Transaction Hash Endpoint](#transaction-hash-endpoint) when using the **includeConfidence** flag, in addition to the explicit [Transaction Confidence Endpoint](#transaction-confidence-endpoint) detailed below.

So what does that **confidence** attribute actually mean? In simpler terms, if an unconfirmed transaction returns a **confidence** factor of 99.9%, then our data says there's a 0.1% chance that an attempted double-spend will succeed. By design, we're conservative. Even when we return 90% confidence, the likelihood of a successful double-spend is significantly lower than 10%.

As you may have gathered from the frequency of links to this feature in these docs, we're exceptionally proud of the work we've done to build this feature and provide it to our users. And that pride extends to our own confidence in our confidence product. We've leveraged this product internally on a major feature: our [live block explorer](https://live.blockcypher.com/) visually shows confidence intervals on unconfirmed transactions directly. Check out a [live view of bitcoin](https://live.blockcypher.com/btc/) and click on one of the most recent unconfirmed transactions. Make sure you pick a very recent transaction: our confidence interval rapidly approaches 99% so quickly that you might miss it!

### How does it work?

There are a host of factors that go into our confidence calculation, but you can broadly organize them into two categories. The *shape* of transactions refers to aspects of transactions that *don't* vary with time (time-invariant) in our model. The *behavior* of transactions refers to aspects of transactions that *do* vary with time (time-variant) in our model.

On the *shape* side of our model, we ask a number of questions about a transaction: How is it structured? What are its inputs and outputs? What's its signature type? Broadly speaking, without knowing anything about the network, does it look like this transaction will be confirmed soon?

The *behavior* part of our model primarily focuses on how a transaction propagates through the network. In order to model this correctly, you need both a great connection to the network and a lot of past data, which we have. As BlockCypher pools resources for many users, we're always connected to a statistically significant number of nodes on the network---we target anywhere between 10 to 20% of the active nodes on any given blockchain---and offer connections in return. By monitoring transaction propagation, the number of nodes that have received it, and how *quickly* they received it, we can calculate its probability to be the "winning" transaction at any given point in time, if a double-spend was attempted. (As an aside, you can always see if a double spend was attempted by checking the **double_spend** and **double_spend_tx** attributes on an [unconfirmed transaction](#tx))

The result is a robust and reliable metric for judging unconfirmed transaction confidence, especially when used in concert with [webhooks and websockets.](#events-and-hooks) Our work here is based on [several public research results](https://eprint.iacr.org/2012/248.pdf) in addition to our own in-depth follow-up research, [which you can read about here](http://blog.blockcypher.com/?p=51). We are constantly improving our confidence model, and we always publish our findings when we do.

<aside class="notice">
While we stand by our work behind our confidence research, please note that these measures are ultimately mitigation strategies if you need fast transaction confirmations. There's always an appreciable risk when accepting unconfirmed transactions, and there's no truly bulletproof way to secure unconfirmed transactions. Double spending attacks, while extremely unlikely, can still occur. Blockchain confirmations ultimately provide the highest level of security, especially when dealing with higher value addresses.
</aside>

## Transaction Confidence Endpoint

```shell
curl https://api.blockcypher.com/v1/btc/main/txs/43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9/confidence

{
"age_millis": 12725,
"receive_count": 666,
"confidence": 0.9901509730004237,
"txhash": "43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9",
"txurl": "https://api.blockcypher.com/v1/btc/main/txs/43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9"
}
```

```ruby
> block_cypher.tx_confidence("4153fddf43c5fd6aa7834bda643619e6dd7d8bf55206bcedb216296881a07830")
=> {"age_millis"=>114659,
 "receive_count"=>783,
 "confidence"=>0.9999808966243114,
 "txhash"=>"4153fddf43c5fd6aa7834bda643619e6dd7d8bf55206bcedb216296881a07830",
 "txurl"=>"https://api.blockcypher.com/v1/btc/main/txs/4153fddf43c5fd6aa7834bda643619e6dd7d8bf55206bcedb216296881a07830"}
```

```python
>>> from blockcypher import get_transaction_details
>>> get_transaction_details('43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9', confidence_only=True))
{
    "age_millis": 12725,
    "confidence": 0.9901509730004237,
    "receive_count": 666,
    "txhash": "43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9", 
    "txurl": "https://api.blockcypher.com/v1/btc/main/txs/43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9"
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
  //querying a recent TX hash detected on BTC network
  conf, err := btc.GetTXConf("bb01beea75683be16b5d59dd3e084d167f41a6866b6880b3070eefff392fdd2a")
  if err != nil {
    fmt.Println(err)
  }
  fmt.Printf("%+v\n", conf)
}

//Result from `go run`:
//{Age:21112 ReceiveCount:756 Confidence:0.9995223691726662 TXHash:bb01beea75683be16b5d59dd3e084d167f41a6866b6880b3070eefff392fdd2a}
```

```php
<?php
// Run on console:
// php -f .\sample\confidence-factor\TransactionConfidenceEndpoint.php

$txClient = new TXClient($apiContext);
$txConfidence = $txClient->getConfidence('43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9');

{
  "age_millis":2188284017,
  "receive_count":-1,
  "confidence":1,
  "txhash":"43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9",
  "txurl":"https://api.blockcypher.com/v1/btc/main/txs/43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9"
}
```

Resource | Method | Return Object
-------- | ------ | -------------
/txs/$TXHASH/confidence | GET | [TXConfidence](#txconfidence)

TXHASH is a *string* representing the hex-encoded transaction hash you're interested in querying, for example:

`43fa951e1bea87c282f6725cf8bdc08bb48761396c3af8dd5a41a085ab62acc9`

The returned [TXConfidence](#txconfidence) object contains the all-important confidence percentage, receive count and more.

You can find an unconfirmed transaction hash from our block explorer [here](https://live.blockcypher.com/btc/latest-unconfirmed-tx).
