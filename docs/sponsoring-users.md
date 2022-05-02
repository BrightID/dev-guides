# Sponsoring Users

## Adding sponsorships to your app

These are the steps to add to your app's available BrightID sponsorships as seen in the [BrightID app dashboard](https://apps.brightid.org/).

### Obtain Dai
Dai is better for buying Sp and Subs--especially on IDChain.

### Buy Sp or Subs
Sp is the short name for Sponsorship tokens. Subs are another way to obtain Sp, [explained below](#Using-Subs).

Links to buy Sp and Subs on mainnet and IDChain are on the [Sponsorship Dashboard](https://sp.brightid.org/).

Interacting with Sp and Subs is much cheaper on IDChain, but prices and availability of Sp and Subs may differ between mainnet and IDChain.

### Obtain Eidi
The gas token on IDChain is called Eidi and free supply is available from the [Eidi faucet](https://idchain.one/begin).

### Bridge assets
If Subs or Sp were purchased on mainnet, bridge them. If either Subs or Sp will be purchased on IDChain, bridge Dai. Use the [IDChain omnibridge](https://omni.idchain.one).

### Convert Sp or Subs (if necessary)
Mainnet Subs or Sp bridged in the previous step need to be converted to IdSp or IdSubs. This can be done from the [sponsorship dashboard](https://sp.brightid.org) after switching networks to IDChain.

![](https://i.imgur.com/xr1G4h6.png)

### Using Sp
1. Assign Sp (on Mainnet) or IDSp (on IDchain) using the "assign" button on the [sponsorship dashboard](https://sp.brightid.org). Select your app from the drop down menu.

### Using Subs
Subs are a stream of sponsorships (Sp), with new Sp available to be claimed immediately and every month thereafter for 6 years at a rate that increases every year. Use the "activate" and "claim" buttons to convert Subs into Sp.

See the [sponsorship dashboard instructions](https://www.brightid.org/sponsorships) for more information.

## Sponsoring users from your code

To sponsor a user, you will need the `appUserID` that you assigned to them in the linking step. See the [creating links section of the basic integration guide]().

### Using an SDK
Call the SDK's `sponsor(appUserID)` function. 

### Using a smart contract
Emit an event called `Sponsor` from a smart contract.

`emit Sponsor(appUserID);`

The smart contract address and network should be registered with the BrightID app registry, so BrightID nodes use to know where to listen for events.