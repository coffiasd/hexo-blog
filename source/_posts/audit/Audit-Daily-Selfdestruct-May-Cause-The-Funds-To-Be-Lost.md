---
title: Audit Daily Selfdestruct May Cause The Funds To Be Lost
date: 2023-03-08 10:40:50
tags:
  - solidity
  - web3 security
categories:
  - audit
---

### [H] Audit Daily Selfdestruct May Cause The Funds To Be Lost

#### Code

[https://github.com](https://github.com/code-423n4/2022-12-escher/blob/5d8be6aa0e8634fdb2f328b99076b0d05fefab73/src/minters/FixedPrice.sol#L110)

```javascript
function buy() external payable {
    _end();
}

function _end(Sale memory _sale) internal {
    emit End(_sale);
    ISaleFactory(factory).feeReceiver().transfer(address(this).balance / 20);
    selfdestruct(_sale.saleReceiver);
}
```

### Impact

After the contract is destroyed,the subsequent execution of the contract's function **buy()** is going on.That causes the msg.value token to be lost in the contract forever.

### Proof of concept

> Note:When there is no code at the address, the transaction will succeed, and the msg.value will be stored in the contract.

Let's say Alice and bob are invoking the contact simultaneously. The transactions are sent to the mempool. Alice is finished executes her transaction when bob is still waiting for his result. And then the contract is destroyed.
Finally, bob finished his transaction and sent his token to this contract. This way bob's token is lost and locked forever in this empty contract.

### Recommended mitigation steps

Instead of using self-destruct, we could modify the state to represent the contract has completed the process.We could modify the code like this:

```javascript
function _end(Sale memory _sale) internal {
        ISaleFactory(factory).feeReceiver().transfer(address(this).balance / 20);
-       selfdestruct(_sale.saleReceiver);
+       sale.finalId = sale.currentId
+       sale.saleReceiver.transfer(address(this).balance);
}
```
