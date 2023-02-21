---
title: make a swap using pancake smart router in solidity contract
date: 2023-02-21 13:31:26
tags:
  - solidity
  - pancakeswap
  - conrtact
  - smart rouetr
  - web3
categories:
  - solidity
---

## overview

In this article i gonna show you how to make a swap using pancakeswap smart router in solidity contract.Here wo go.In this example i swap BUSD token to CAKE.Here is the address of each token:

- BUSD : <https://testnet.bscscan.com/address/0x78867BbEeF44f2326bF8DDd1941a4439382EF2A7>
- CAKE : <https://testnet.bscscan.com/address/0xfa60d973f7642b748046464e165a65b7323b0dee>
  Click the link you'll find them in bscscan testnet. If you don't hold those tokens, nerver mind claim some from bsc faucet by clicking [faucet](https://testnet.bnbchain.org/faucet-smart) website.

## soldity

Let's go dive into the deep.Firstly we need set a swap amount, eg:0.01 BUSD, an wed need a min out amount as well.The last parameter is path which means the path of swap tokens.When we swap busd to cake we can find a pool consist of busd and cake。So the path is simple.For some other tokens maybe the path is a litte bit longer.We have to use two or three or more pools to get the token we want.In the pancakeswap front-end page , it returns a path before we make the swap.In the solidity code we have to do it ourself.We have to get the optimal path before we make the swap.Let's dive into the pancakeswap smart router code.

```javascript
interface PancakeRouter {
    function swapExactTokensForTokens(uint256 amountIn,uint256 amountOutMin,address[] calldata path,address to,uint256 deadline) external;
    function swapExactTokensForTokensSupportingFeeOnTransferTokens(uint256 amountIn,uint256 amountOutMin,address[] calldata path,address to,uint256 deadline) external;
    function factory() external pure returns (address);
}
```

There are so many swap function we can use to help use swap tokens.In this demo we use **swapExactTokensForTokens** . As you see there is a function named **swapExactTokensForTokensSupportingFeeOnTransferTokens**. We need to know what's the difference between this two functions.

```javascript
function swapExactTokensForTokensSupportingFeeOnTransferTokens(
    uint amountIn,
    uint amountOutMin,
    address[] calldata path,
    address to,
    uint deadline
) external virtual override ensure(deadline) {
    // transfer token from sender address to pool[0]
    TransferHelper.safeTransferFrom(
        path[0], msg.sender, PancakeLibrary.pairFor(factory, path[0], path[1]), amountIn
    );

    // the balance of des token that receiver hold.
    uint balanceBefore = IERC20(path[path.length - 1]).balanceOf(to);

    // make the sawp.
    _swapSupportingFeeOnTransferTokens(path, to);

    // make sure the out token is bigger than the set amountOutMin value.
    require(
        IERC20(path[path.length - 1]).balanceOf(to).sub(balanceBefore) >= amountOutMin,
        'PancakeRouter: INSUFFICIENT_OUTPUT_AMOUNT'
    );
}
```

```javascript
    function swapExactTokensForTokens(
        uint amountIn,
        uint amountOutMin,
        address[] calldata path,
        address to,
        uint deadline
    ) external virtual override ensure(deadline) returns (uint[] memory amounts) {
        // get the out amount using PancakeLibrary via amountIn.
        amounts = PancakeLibrary.getAmountsOut(factory, amountIn, path);

        // after calculate all result make sure the out amount is bigger than amountOutMin.
        require(amounts[amounts.length - 1] >= amountOutMin, 'PancakeRouter: INSUFFICIENT_OUTPUT_AMOUNT');

        //transfer code.
        TransferHelper.safeTransferFrom(
            path[0], msg.sender, PancakeLibrary.pairFor(factory, path[0], path[1]), amounts[0]
        );
        _swap(amounts, path, to);
    }
```

After compare the above two functions we know the difference.In the **swapExactTokensForTokens** function the exact transfer amount is set by PancakeLibrary getAmountsOut. And in this article we use **swapExactTokensForTokensSupportingFeeOnTransferTokens**.
Here is the entire code from github gist : [code](https://gist.github.com/coffiasd/825ac5ba1b1bafdc0ea799b7ba35d4a5)

## deploy our code

Deploy code:

```javascript
const hre = require("hardhat");

async function main() {
  const Swap = await hre.ethers.getContractFactory("PancakeSwap");
  const swap = await Swap.deploy();
  await swap.deployed();
  console.log(swap.address);
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

```shell
npx hardhat run script/scriptName.js --network YOUR_NETWORK_NAME.
```

## test swap

```javascript
const hre = require("hardhat");

async function main() {
  const Bundle = await hre.ethers.getContractFactory("PancakeSwap");
  const bundle = Bundle.attach(YOUR_DEPLOYED_CONTRACT_ADDRESS);
  const amountIn = hre.ethers.utils.parseEther("0.01");
  const amountOutMin = 0;
  const tokenSwapPath = [TOKEN1_ADDRESS, TOKEN2_ADDRESS];
  let ret = await bundle.SimpleSwap(amountIn, amountOutMin, tokenSwapPath);
  console.log(ret);
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```
