# Basic integration

## What's covered

This guide shows you how to let users link their BrightIDs to your app so you can check their verification status.

Other options for integrating BrightID not covered include

- Verifying community members through the [Discord unique bot](https://github.com/ShenaniganDApp/brightid-discord-bot). This is the quickest path to BrightID integration.
- Tightly integrating BrightID connection-making into your app using [API functions](https://stoplight.brightid.io/docs/node-api/) so users don't have to download the BrightID app separately.

## Prerequisites

This guide assumes you have [registered an app](https://stoplight.brightid.io/#step-1).

### Soulbound or Blind Signatures?

Your app can use either Soulbound (API v5) or Blind Signatures (API v6) based on your requirements. Both API versions will be supported indefinitely.

> Soulbound (API V5)

Choose the Soulbound API if you want to be able to track each user by multiple unique identifiers registered over time. This could allow someone to recover their account on multiple devices, or using multiple (including yet to be linked) accounts or addresses, such as Ethereum addresses, and preserve their account history. This is approach is useful if accounts are meant to keep a long-lived record of activity. It can be used to enforce permanent bans.

> Blind Signatures (API V6)

Choose the Blind Signatures API if you want unlinkability between multiple accounts in your app, while still limiting a user to one account at a time. Blind Signature apps use expiration periods. A user is limited to one account per expiration period, but no one can link a user's history or activity between previous expiration periods. A user that loses access to an account simply makes a new one in the next expiration period.

## SDKs and Utilities

Much of what's covered in this guide can be quickly generated using an SDK.

- [Javascript - Blind Signatures (API v6)](https://github.com/BrightID/brightid-javascript-sdk)
- [Javascript - Soulbound (API v5)](https://github.com/BrightID/brightid-javascript-sdk/tree/v5-soulbound)
- [Python](https://github.com/BrightID/brightid-python-sdk)
- Soulbound (API v5) Test app to test linking, verifying, and sponsoring
    - [Source](https://github.com/acolytec3/brightid_test_app)
    - [Hosted here](https://acolytec3.github.io/brightid_test_app/)
- [TweetNaCl.js online tool to test creating and verifying signatures](https://tweetnacl.js.org/#/sign)

## Creating links

### Create a unique identifer (`appUserId`)

Each user of your app will need a unique identifier to serve as a `appUserId`. This could be a UUID or any other url-escaped string.

<!-- theme: warning -->

> ### Warning
>
> If Ethereum addresses are used as appUserId (contextId), use the [checksummed format](https://ethsum.netlify.app/).

### Format the universal link

SDKs can be used to generate the links needed. The link format will be slightly different depending on whether you're using Soulbound (API v5) or Blind Signatures (API v6).

#### Blind Signature (API V6)

The link is of the form

```
https://app.brightid.org/link-verification/{app}/{app_user_id}
```

where

- `{app}` is the unique app name registered with node operators.
- `{app_user_id}` is the unique identifier you want to use to identify this user.

#### Soulbound (API V5)

The link is of the form

```
https://app.brightid.org/link-verification/{node_url}/{context}/{context_id}
```

where

- `{node_url}` is `http:%2f%2fnode.brightid.org` (url-escaping the slashes is necessary).
- `{context}` is the context registered for your app with node operators.
- `{context_id}` is the unique identifier you want to use to identify this user.

### Share the link with the user

Present the link you've constructed to the user identified by the `appUserId` or `contextId`. When the user clicks the link, it will open their BrightID app (or ask them to install the BrightID app) and ask if they want to create a link to your app.

After they link, you will be able to use the `appUserId` or `contextId` as a parameter in [BrightID Node API](https://stoplight.brightid.io/docs/node-api/) calls to refer to that user.

### Rendering the link as a QR

While the above works well for presenting the link in a mobile browser, if the user is viewing your app on another device such as a laptop, a QR code rendering of the link is often more convenient. The user can scan it with the BrightID app's QR scanner.

Render the link using one of the following formats

> Blind Sigs (API v6)
```
brightid://link-verification/<app>/<app_user_id>
```

> Soulbound (API v5)
```
brightid://link-verification/<node_url>/<context>/<context_id>
```

using the [values described above](#format-the-universal-link).

[BrightID SDKs](#sdks-and-utilities) are available that can generate both regular links and QR images.

## Querying Verifications

### One user

The following query will retrieve the `unique` status of one user referenced by their `{appUserId}` or `{contextId}`

> Blind Signatures (API V6)
```json http
{
  "method": "get",
  "url": "https://app.brightid.org/node/v6/verifications/{app}/{appUserId}",
  "query": {
    "signed": null,
    "timestamp": null
  },
  "path": {
    "app": "clrfund-arbitrum",
    "appUserId": "0xdC0046B52e2E38AEe2271B6171ebb65cCD337518"
  }
}
```

> Soulbound (API V5)
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
    "contextId": "0xdC0046B52e2E38AEe2271B6171ebb65cCD337518"
  }
}
```

### All verifications

> Soulbound (API V5) only
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

### Parameters to request node signatures

Having nodes sign the query result allows users to submit the signed verification to a smart contract (See [the EVM integration guide](evm-integration.md).) or another decentralized service.

If the optional `signed` parameter is present (allowed values: `eth` or `nacl`), a message consisting of the context and the list of contextIds will be signed by a private key belonging to the node. It will add the following elements to the returned object.

- `sig` (the signed message)
- `address` (in the case of `eth`)
- **OR**`publicKey` (in the case of `nacl`)

#### `timestamp` parameter

If the optional `timestamp` parameter is present (allowed values:`seconds` or `milliseconds`), a `timestamp` element will be added to the returned object.

## Sponsoring users

A user must be "sponsored" before your app can [query their verification](#querying-verifications) status. This is done by purchasing sponsorships and assigning them to your app.

[An SDK](#sdks-and-utilities) will help you create and send a "sponsor" operation to a BrightID node for a user (identified by the `contextId`).

### Purchasing sponsorships

You can purchase sponsorships from the [sponsorship dashboard](https://sp.brightid.org) and assign them to a [registered app](https://stoplight.brightid.io/#step-1). You can also find out how to purchase [subs (discounted sponsorships)](https://medium.com/brightid/what-value-do-subscriptions-subs-have-for-applications-49b7602aa228) at the sponsorship dashboard.

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
