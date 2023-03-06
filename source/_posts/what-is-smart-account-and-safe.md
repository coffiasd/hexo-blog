---
title: what is smart account and safe
date: 2023-03-06 08:00:53
tags:
  - safe
  - hackathon
categories:
  - hackathon
---

## overview

Safe provide a sdk to create a smart contract account which is a fully customizable acocunt.
In crypto if you give away your private key,your money is gone.You can't take them back.Smarct account is built to prevent those.
Common usecase:

- multiple-signer(2 or more)
- set spending limit(e.g. $100 perday)

Smart Account vs Signing Accounts(EOA)
Difference between two type of accounts
!["smart account"](/images/smart-account.jpg)

## safe {core}

safe {core} consist of 3 parts.

### safe {core} AA SDK

Interact with your application directly.

### safe {core} API

Provide safe account related information.

### safe {core} protocol

Smart contract account standard and programmable.

## Safe AA SDK

### protocol kit

1.Install Dependencies

```shell
yarn add ethers@5.7.2 @safe-global/safe-core-sdk \
  @safe-global/safe-core-sdk-types \
  @safe-global/safe-service-client \
  @safe-global/safe-ethers-lib \
  dotenv
```

2.Create .env config file

```shell
touch .env
export OWNER_1_PRIVATE_KEY='<PRIVATE_KEY>'
export OWNER_2_PRIVATE_KEY='<PRIVATE_KEY>'
export OWNER_3_PRIVATE_KEY='<PRIVATE_KEY>'
```

3.Init

```javascript
import { ethers } from 'ethers'
import EthersAdapter from '@safe-global/safe-ethers-lib'

// https://chainlist.org/?search=goerli&testnets=true
const RPC_URL='https://eth-goerli.public.blastapi.io'
const provider = new ethers.providers.JsonRpcProvider(RPC_URL)

// Initialize signers
const owner1Signer = new ethers.Wallet(process.env.OWNER_1_PRIVATE_KEY!, provider)
const owner2Signer = new ethers.Wallet(process.env.OWNER_2_PRIVATE_KEY!, provider)
const owner3Signer = new ethers.Wallet(process.env.OWNER_3_PRIVATE_KEY!, provider)

const ethAdapterOwner1 = new EthersAdapter({
  ethers,
  signerOrProvider: owner1Signer
})
```

4.Deploy your safe

```javascript
import { SafeAccountConfig } from "@safe-global/safe-core-sdk";

const safeAccountConfig: SafeAccountConfig = {
  owners: [
    await owner1Signer.getAddress(),
    await owner2Signer.getAddress(),
    await owner3Signer.getAddress(),
  ],
  threshold: 2,
  // ... (Optional params)
};

/* This Safe is tied to owner 1 because the factory was initialized with
an adapter that had owner 1 as the signer. */
const safeSdkOwner1 = await safeFactory.deploySafe({ safeAccountConfig });

const safeAddress = safeSdkOwner1.getAddress();

console.log("Your Safe has been deployed:");
console.log(`https://goerli.etherscan.io/address/${safeAddress}`);
console.log(`https://app.safe.global/gor:${safeAddress}`);
```

### auth Kit

The Auth kit creates an Ethereum address and authenticates a blockchain account using an email address, social media account, or traditional crypto wallets like Metamask.

```javascript
await safeAuthKit.signIn();
await safeAuthKit.signOut();
safeAuthKit.getProvider();
safeAuthKit.subscribe(SafeAuthEvents.SIGN_IN, () => {
  console.log("User is authenticated");
});

safeAuthKit.subscribe(SafeAuthEvents.SIGN_OUT, () => {
  console.log("User is not authenticated");
});

const safeAuthKit = await SafeAuthKit.init(SafeAuthProviderType.Web3Auth, {
  ...
  txServiceUrl: 'https://safe-transaction-goerli.safe.global' // Add the corresponding transaction service url depending on the network. Other networks: https://docs.gnosis-safe.io/learn/infrastructure/available-services#safe-transaction-service
  authProviderConfig: { ... }
})
```

### relay kit

The Relay Kit allows users to pay transaction fees (gas fees) using the native blockchain token or ERC-20 tokens. This allows you to pay gas fees using any ERC-20 token in your Safe, even if you don't have ETH.

```shell
yarn add @safe-global/relay-kit
```

```javascript
import { GelatoRelayAdapter } from "@safe-global/relay-kit";

const relayAdapter = new GelatoRelayAdapter();

relayAdapter.relayTransaction({
  target: "0x...", // the Safe address
  encodedTransaction: "0x...", // Encoded Safe transaction data
  chainId: 5,
});
```

```javascript
import {
  GelatoRelayAdapter,
  MetaTransactionOptions,
} from "@safe-global/relay-kit";

const relayAdapter = new GelatoRelayAdapter(GELATO_RELAY_API_KEY);

const options: MetaTransactionOptions = {
  isSponsored: true, // This parameter is mandatory to use the 1Balance method
};
relayAdapter.relayTransaction({
  target: "0x...", // the Safe address
  encodedTransaction: "0x...", // Encoded Safe transaction data
  chainId: 5,
  options,
});
```

### onramp kit

This package is provided for testing purposes only

## Safe API

| Network          | Host                                         |
| ---------------- | -------------------------------------------- |
| Ethereum Mainnet | https://safe-transaction-mainnet.safe.global |
| Goerli           | https://safe-transaction-goerli.safe.global  |

## Safe Protocol

### Modules

Modules are smart contracts that add custom features to Safe contracts. They separate module logic from the Safe's core contract, and are added or removed with confirmation from all owners. Modules are critical for security and emit events when added, removed, or when module transactions succeed or fail. There are many types of modules, including daily spending allowances, recurring transactions, standing orders, and social recovery modules, which can help you recover a Safe if you lose access to owner accounts. Modules can be used in various ways to enhance your Safe's functionality.

### Guards

Transaction guards can make checks before and after a Safe transaction.
