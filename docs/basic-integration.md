# Basic integration

## What's covered

This guide shows you how to let users link their BrightIDs to your app so you can check their verification status. It also covers the basics of [sponsoring users](#sponsoring-users).

Other options for integrating BrightID not covered include

-   Verifying community members through the [Discord unique bot](https://github.com/ShenaniganDApp/brightid-discord-bot).
-   Tightly integrating BrightID connection-making into your app using [API functions](https://dev.brightid.org/docs/node-api/) so users don't have to download the BrightID app separately.

## Prerequisites

This guide assumes you have [registered an app and a context](https://dev.brightid.org/#step-1).

## Creating links

### Create a unique identifer (`contextId`)

Each user of your app will need a unique identifier to serve as a `contextId`. This could be a UUID or any other url-escaped string.

### Format the universal link

The link is of the form

    https://app.brightid.org/link-verification/{node_url}/{context}/{context_id}

where

-   `{node_url}` is `http:%2f%2fnode.brightid.org` (url-escaping the slashes is necessary).
-   `{context}` is the context registered for your app with node operators.
-   `{contextId}` is the unique identifier you want to use to identify this user.

### Share the link with the user

Present the link you've constructed to the user identified by the `contextId`. When the user clicks the link, it will open their BrightID app (or ask them to install the BrightID app) and ask if they want to create a link to your app.

After they link, you will be able to use the `contextId` as a parameter in [BrightID Node API](https://dev.brightid.org/docs/node-api/) calls to refer to that user.

### Rendering the link as a QR

While the above works well for presenting the link in a mobile browser, if the user is viewing your app on another device such as a laptop, a QR code rendering of the link is often more convenient. The user can scan it with the BrightID app's QR scanner.

Render the link using the following format

    brightid://link-verification/<node_url>/<context>/<context_id>

using the [values described above](#format-the-universal-link).

[BrightID SDKs](#sdks-and-utilities) are available that can generate both regular links and QR images.

## Querying Verifications

The following query will retrieve all `contextIds` for a `{context}` (or just the count).
```json http
{
  "method": "get",
  "url": "https://app.brightid.org/node/v5/verifications/{context}",
  "query": {
    "count_only": "true"
  },
  "path": {
    "context": "Discord"
  }
}
```

The following query will retrieve the `unique` status of one user referenced by their `{contextId}`

```json http
{
  "method": "get",
  "url": "https://app.brightid.org/node/v5/verifications/{context}/{contextId}",
  "query": {
    "signed": null,
    "timestamp": null
  },
  "path": {
    "context": "clr.fund",
    "contextId": "0xd714dd60e22bbb1cbafd0e40de5cfa7bbdd3f3c8"
  }
}
```

### Parameters to request node signatures
Having nodes sign the query result allows users to submit the signed verification to a smart contract (such as one generated from one of [these templates](https://github.com/BrightID/BrightID-SmartContract)) or another decentralized service.

If the optional `signed` parameter is present (allowed values: `eth` or `nacl`), a message consisting of the context and the list of contextIds will be signed by a private key belonging to the node. It will add the following elements to the returned object.
* `sig` (the signed message)
* `address` (in the case of `eth`) 
* **OR**`publicKey` (in the case of `nacl`)

#### `timestamp` parameter
If the optional `timestamp` parameter is present (allowed values:`seconds` or `milliseconds`), a `timestamp` element will be added to the returned object.

## Sponsoring users
A user must be "sponsored" before your app can [query their verification](#querying-verifications) status. This is done by purchasing sponsorships and assigning them to your app.

[An SDK](#sdks-and-utilities) will help you create and send a "sponsor" operation to a BrightID node for a user (identified by the `contextId`).

### Purchasing sponsorships
You can purchase sponsorships from the [sponsorship dashboard](https://sp.brightid.org) and assign them to a [registered app](https://dev.brightid.org/#step-1). You can also find out how to purchase [subs (discounted sponsorships)](https://medium.com/brightid/what-value-do-subscriptions-subs-have-for-applications-49b7602aa228) at the sponsorship dashboard.

### Already sponsored
If a user was already sponsored by another app, you don't have to sponsor them again. If you attempt to sponsor an already sponsored user, nothing will happen--another sponsorship won't be spent.

### Checking your app's unused sponsorship balance
The following query will return your app's `unusedSponsorships` count.

```json http
{
  "method": "get",
  "url": "https://app.brightid.org/node/v5/apps/{app}",
  "path": {
    "app": "clr.fund"
  }
}
```
## SDKs and Utilities
* [Javascript](https://www.npmjs.com/package/brightid_sdk)
* [Python](https://github.com/PooyaFekri/python-brightid)
* [Test app to test linking, verifying, and sponsoring](https://github.com/acolytec3/brightid_test_app) [Hosted here](https://acolytec3.github.io/brightid_test_app/)
* [TweetNaCl.js Online Tool for testing creating and verifying signatures](https://tweetnacl.js.org/#/sign)

