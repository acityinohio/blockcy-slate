# Analytics API

Blockchains contain a wealth of data, but much of it is inscrutable without arduous effort. Our Analytics API makes uncovering these patterns easy, enabling you to discover insights efficiently and programmatically.

<aside class="warning">
The Analytics API is in <strong>pre-beta</strong>, and currently only supports Bitcoin Mainnet (/btc/main/analytics). Things may break/it might not be suitable for production applications.
</aside>

The publicly accessible Analytics API is a set of preconfigured tools and pipelines built against our flexible analytics backend. It serves as both a useful tool for one-off analysis and a live demonstration for custom implementations. If you're interested in a custom Analytics solution at enterprise scale---or have any ideas for general purpose analytics queries to benefit the community!---please reach out to [analytics@blockcypher.com](mailto:analytics@blockcypher.com).

The API is asynchronous; here's the high level general workflow:

1. Create an analytics job, based on the *engine* types listed below.
1. Review its status/check to see if it's complete.
1. Fetch the results.

Each engine has its own request parameters---listed below---but all are described in detail on the [JobArgs](#jobargs) object page.

<aside class="notice">
By design, user data is not exposed to our Analytics API. Consequently, you can't query by things like token-specific wallet name, HD wallet name, and user-set metadata; only native blockchain objects are supported.
</aside>

## Analytics Engines and Parameters

The following *engines* can power your analytics jobs. The resource below represents the URL you'd POST to [create an analytics job](#create-analytics-job) based on the engine you're interested in using, along with the required and optional [JobArgs](#jobargs) parameters for searching.

### Paying Addresses

Resource | Required [JobArgs](#jobargs) | Optional [JobArgs](#jobargs)
-------- | ---------------------------- | ----------------------------
/analytics/payingaddresses | **address** | **value_threshold**

Returns the list of addresses (along with their associated transaction hashes) and amounts that have paid the target **address**. Optionally restricted by only returning **value_transfer** (in satoshis) transfers and above.

### Payee Addresses

Resource | Required [JobArgs](#jobargs) | Optional [JobArgs](#jobargs)
-------- | ---------------------------- | ----------------------------
/analytics/payeeaddresses | **address** | **value_threshold**

Returns the list of addresses (along with their associated transaction hashes) and amounts that have been paid by the target **address**. Optionally restricted by only returning **value_transfer** (in satoshis) transfers and above.

### Top Addresses

Resource | Required [JobArgs](#jobargs) | Optional [JobArgs](#jobargs)
-------- | ---------------------------- | ----------------------------
/analytics/topaddresses | | **limit**, **start**, **end**

Returns the most active **limit** number of addresses, optionally within a certain bounded time range denoted by **start** and **end**.

### Address Activity

Resource | Required [JobArgs](#jobargs) | Optional [JobArgs](#jobargs)
-------- | ---------------------------- | ----------------------------
/analytics/addressactivity | **address**, **start**, **end** |

Returns the outputs this address generated within a certain bounded time range denoted by **start** and **end**.

### Address Connectivity

Resource | Required [JobArgs](#jobargs) | Optional [JobArgs](#jobargs)
-------- | ---------------------------- | ----------------------------
/analytics/addressconnectivity | **address**, **degree** | **limit**

N-**degree** address connectivity query. Currently only examines addresses that generate outputs in the same transaction as the target **address**.

### Source Lookup

Resource | Required [JobArgs](#jobargs) | Optional [JobArgs](#jobargs)
-------- | ---------------------------- | ----------------------------
/analytics/sourcelookup | **source** | **start**, **end**, **limit**

Returns transactions relayed by a given **source** (IP address and port) within the given time range denoted by **start** and **end**. Note that the max **limit** is 1000 for this *engine.*

## Create Analytics Job

```shell
# let's see who paid a ton of bones into satoshi bones
curl -sd '{"address" : "1bones5gF1HJeiexQus6UtvhU4EUD4qfj", "value_threshold" : 100000000}' https://api.blockcypher.com/v1/btc/main/analytics/payeeaddresses?token=YOURTOKEN

{
    "token": "YOURTOKEN",
    "analytics_engine": "payeeaddresses",
    "created_at": "2016-07-11T19:33:35.518605498Z",
    "completed_at": "0001-01-01T00:00:00Z",
    "finished": false,
    "started": false,
    "ticket": "50b77697-ff13-4a50-8a2b-cee605bc3000",
    "args": {
        "address": "1bones5gF1HJeiexQus6UtvhU4EUD4qfj",
        "value_threshold": 100000000
    }
}

```

Resource | Method | Request Object | Return Object
-------- | ------ | -------------- | -------------
/analytics/$ENGINENAME | POST | [JobArgs](#jobargs) | [Job](#job)

This creates an analytics job asynchronously, taking in appropriate [JobArgs](#jobargs) based on the *engine* as described above. Returns a [Job](#job); take special note of the **ticket** which is the unique identifier you'll use to get results and query job status.

## Get Analytics Job

```shell
#let's check the status of our job
curl -s https://api.blockcypher.com/v1/btc/main/analytics/job/50b7697-ff13-4a50-8a2b-cee605bc3000?token=YOURTOKEN
{
    "token": "YOURTOKEN",
    "analytics_engine": "payeeaddresses",
    "created_at": "2016-07-11T19:33:35.518605498Z",
    "completed_at": "2016-07-11T19:35:25.539002964Z",
    "finished": true,
    "started": true,
    "ticket": "50b77697-ff13-4a50-8a2b-cee605bc3000",
    "result_path": "https://api.blockcypher.com/v1/btc/main/analytics/job/50b77697-ff13-4a50-8a2b-cee605bc3000/results",
    "args": {
        "address": "1bones5gF1HJeiexQus6UtvhU4EUD4qfj",
        "value_threshold": 100000000
    }
}
```

Resource | Method | Return Object
-------- | ------ | -------------
/analytics/job/$TICKET | GET | [Job](#job)

You can check the status of your job with this endpoint, using the **ticket** returned in the [Job object](#job) from [creating an analytics job](#create-analytics-job). If it's **finished**, it will include a **result_path** with the URL you can follow to get your results.

<aside class="notice">
Depending on the number of analytics jobs in our queue, it may take some time for the job to start. Most jobs won't take more than 10 minutes to run. The status fields <strong>finished,started,</strong> and <strong>result_path</strong> are cleared 24 hours after the job's last update, but the results are kept indefinitely.
</aside>

## Get Analytics Job Results

```shell
curl -s https://api.blockcypher.com/v1/btc/main/analytics/job/50b77697-ff13-4a50-8a2b-cee605bc3000/results
{
    "page": 0,
    "more": true,
    "next_page": "https://api.blockcypher.com/v1/btc/main/analytics/job/50b77697-ff13-4a50-8a2b-cee605bc3000/results?page=1",
    "results": [
        {
            "DstAddr": "1changemCPo732F6oYUyhbyGtFcNVjprq",
            "SrcAddr": "1bones5gF1HJeiexQus6UtvhU4EUD4qfj",
            "TxHash": "0x3b06b1e9d70217d5e02644703fe79f54355b0ea05cd535787f5a6c627f1c
            43ef",
            "Value": 1e+08
        },
        ....
    ]
}
```

Resource | Method | Return Object
-------- | ------ | -------------
/analytics/job/$TICKET/results | GET | [JobResults](#jobresults)

Flag | Type | Effect
---- | ---- | ------
**page** | *integer* | Returns **page** of results.

Returns the results of a **ticket**ed job. The results themselves may be an array of JSON objects or an array of strings, depending on the *engine* used.
