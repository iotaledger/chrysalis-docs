# FAQ


## Which libraries are available for Chrysalis?

At this time, there are a handful of libraries available for Chrysalis which are outlined below:

A low level library called iota.rs which is a client library meant to connect to a IOTA node for core interactions with the Tangle. It is written in Rust and there are currently two bindings for Node.js and Python allowing you to use this library from those languages as well. 

For value transactions, there is the wallet.rs library which provides a stateful way to manage IOTA coins for one or multiple accounts. It is also written in Rust and there are currently two bindings for Node.js and Python. 

Additionally, there is also a native C ([iota.c](https://github.com/iotaledger/iota.c)) and an alternative, native javascript ([iota.js](https://github.com/iotaledger/iota.js/tree/chrysalis)) client library. 

## What is Dust protection and how does it work?

Since IOTA is feeless and has the ability to send microtransactions, attackers could use this to spam the network with very low value transactions, which we call dust. To avoid this we only allow microtransaction below 1Mi of IOTA tokens to another address if you already have at least 1Mi on that address.

You can read more about Dust Protection [in the RFC here](https://github.com/iotaledger/protocol-rfcs/pull/32).

## What's happening with Coordicide?

This release called Chrysalis still depends on the Coordinator/Compass to run and issue milestones. The next big release after Chrysalis will be Coordicide where we get rid of that dependency. This release is currently in a research/testing phase and will be the main priority after the Chrysalis release.

## When is a transaction on the network considered final/irreversible?

Confirmation times on the new network are on average around 10 seconds. Once a transaction is set to confirm it’s final transaction, you don’t have block confirmations like with blockchain.

## Hornet or Bee? Which node software should I use?

You can pick either Bee (Rust based) or Hornet (Go based). We currently recommend Hornet since it’s the more complete version of the node software, Bee is still missing some optional features you might wish to use.

## What are spent addresses and why are they dangerous?
- In the IOTA 1.0 network, IOTA used Winternitz One Time Signatures (W-OTS) - think of these as, more or less, an authenticator and validator for a transaction. These keys and signatures are highly secure against malicious attacks for signing transactions. But, on the downside, by signing a transaction, W-OTS reveals parts of a private key for the specific address tokens are being spent from. 
- With W-OTS every time a signature is signed to spend tokens from a particular address, any remaining tokens need to be moved onto a new address to prevent malicious actors from brute-forcing (trial-and-error guessing) the remaining parts of the private key for the address. That’s the main reason why this signature scheme is considered to be a “one-time signature”. 
- So after the Chrysalis update, we are using the Ed25519, based on the EdDSA, scheme instead of W-OTS. The advantage is that the new scheme addresses all of the issues that W-OTS originally had, where Ed25519 verifies both single-signature and batch verification (taking care of the left over/remaining tokens) very quickly as well as faster key generation and smaller signatures (very secure). 



## What is bundle mining?
-  If you have spent addresses, it means you accidentally received funds to an address that was already spent from and these funds are not safe to send again due to W-OTS. 
-  To secure your spent addresses during the migration, Firefly will try to find a new bundle that reveals the least amount of additional private key parts compared to previous signs.
- This process will take 10 minutes per spent address and upon completion you will be presented with a risk calculation (very high, high, medium, low, very low). It is recommended that you repeats the process if it returns a bundle with medium risk or higher, particularly for significant sums of IOTA. You have the option to select which addresses you want to mine for and again which you want to rerun the process for.