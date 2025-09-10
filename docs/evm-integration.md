# EVM integration

BrightID supports clients uploading signed verifications to a smart contract that can then create a registry of verified users.

Each app manages its own registry. An "app" in BrightID can also represent a network or a platform.

This involves deploying a smart contract for the app  based on a BrightID smart contract template. This is useful if an app needs to check if someone is unique from within a smart contract rather than using code which can call `/verifications` on a BrightID node. A user’s client program initially uses the `/verifications` endpoint with the `signed=eth` and `timestamp=seconds` parameters to prepare the verification to send to the smart contract’s `verify` function.

## Example code

Please see the [github repo](https://github.com/BrightID/BrightID-SmartContract) for templates that can be deployed per app as needed.

There is [also a branch with example code](https://github.com/BrightID/BrightID-SmartContract/tree/soulbound) for use with the BrightID-Node-Soulbound API.
