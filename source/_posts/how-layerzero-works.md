---
title: how-layerzero-works
date: 2024-08-05 12:46:12
tags:
  - layerzero
categories:
  - solidity
---

## how layerzero works
### flowchart
![](https://raw.githubusercontent.com/coffiasd/images/main/202408051543348.png)


## main compoent
### LayerZero Endpoint
The LayerZero Endpoint is an immutable smart contract that implements a standardized interface for Omnichain Applications (OApps) to manage security configurations and seamlessly send and receive messages.

### Message Library
Each MessageLib is an immutable verification library that OApp owners can configure their application to use. The protocol enforces the contract owner's unique OApp Configuration before sending and receiving messages.

### Message Packet
The Message Packet standardizes the format and size of messages that are sent between different blockchains:
```solidity
struct Packet {
    uint64 nonce; // the nonce of the message in the pathway
    uint32 srcEid; // the source endpoint ID
    address sender; // the sender address
    uint32 dstEid; // the destination endpoint ID
    bytes32 receiver; // the receiving address
    bytes32 guid; // a global unique identifier
    bytes message; // the message payload
}
```
### Security Stack (DVNs)
Security Stack (DVNs) is used to check the `payloadHash` emitted for message integrity.
There are two number here:
- required Decentralized Verifier Networks(DVNs)
- optional Decentralized Verifier Networks(DVNs)


### Executors
Executors is used to invoke target chain lzReceive function in the Endpoint contract once the message payload is verified.

## Methods
### _lzSend
the function your application must implement to send an omnichain message.

### _lzReceive
the function to receive an omnichain message

### _lzCompose
Since each composable call is created as a separate message packet via lzCompose, this pattern can be extended for as many steps as your application needs (B1 -> B2 -> B3, etc).

### Message Execution Options
LayerZero provides robust Message Execution Options, which allow you to specify arbitrary logic as part of the message transaction, such as the gas amount and msg.value the Executor pays for message delivery, the order of message execution, or dropping an amount of gas to a destination address.

### quote
estimate of how much gas a message will cost to be sent and received

## Integration Checklist
<https://docs.layerzero.network/v2/developers/evm/troubleshooting/integration-checklist>

## resources
<https://docs.layerzero.network/v2/developers/evm/overview>
<https://github.com/LayerZero-Labs/LayerZero-v2/tree/main/packages/layerzero-v2/evm/oapp/contracts>