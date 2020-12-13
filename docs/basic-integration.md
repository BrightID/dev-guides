# Basic integration

## What's covered
This guide shows you how to let users link their BrightIDs to your app so you can check their verification status.

Other options for integrating BrightID not covered include
* Verifying community members through the [Discord unique bot](https://github.com/ShenaniganDApp/brightid-discord-bot).
* Tightly integrating BrightID connection-making into your app using [API functions](../../../node-api) so users don't have to download the BrightID app separately.

## Prerequisites
This guide assumes you have a registered app and context; if not, please [contact a BrightID node operator](https://dev.brightid.org/#step-1).

## Creating links
### Create a unique identifer (`contextId`)
Each user of your app will need a unique identifier to serve as a `contextId`. This could be a UUID or any other url-escaped string.
### Format the universal link
The link is of the form
```
https://app.brightid.org/link-verification/{node_url}/{context}/{context_id}
```
where
* `{node_url}` is `http:%2f%2fnode.brightid.org` (url-escaping the slashes is necessary).
* `{context}` is the context registered for your app with node operators.
* `{contextId}` is the [unique identifier](#create-a-unique-identifier-contextid) you want to use to identify this user.
### Share the link with the user
Present the link you've constructed to the user identified by the `contextId`. When the user clicks the link, it will open their BrightID app (or ask them to install the BrightID app) and ask if they want to create a link to your app.

After they link, you will be able to use the `contextId` as a parameter in [BrightID Node API](../../../node-api) calls to refer to that user.
### Rendering the link as a QR
While the above works well for presenting the link in a mobile browser, if the user is viewing your app on another device such as a laptop, a QR code rendering of the link is often more convenient. The user can scan it with the BrightID app's QR scanner.

Render the link using the following format
```
brightid://link-verification/<node_url>/<context>/<context_id>
```
using the [values described above](#format-the-universal-link).

BrightID SDKs are available that can generate both regular links and QR images.

![](../assets/images/neurons-white-bg.png)