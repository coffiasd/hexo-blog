---
title: balancer-v2-vault
date: 2023-02-10 16:28:33
tags:
  - solidity
  - web3
  - defi
  - flashloan
categories:
  - solidity
---

## 概览

本文主要介绍了在 balancer v2 上实现借贷并在一个交易中归还所借到的 token。

## 环境

- node
- hardhat(solidity 0.8.0)

## Balancer v2 介绍

Balancer 是一个多链部署（Ethereum、Polygon、Arbitrum），基于 AMM（自动做市商）模型的 Dex。
Balancer 于 2020 年正式上线 V1 版本，并于 2021 年 5 月升级为 Balancer V2，目前市面上主要使用的都是基于 V2 的产品。
Balancer Vault 是一个安全的、非监管的数字资产保管库，可以用来将数字资产存储到 Balancer 池中，进行高效分散。Balancer Vault 是一种特殊的智能合约，可以在没有任何门槛的情况下，保护您的数字资产不受恶意攻击。此外，它还可以提供具有开放式架构和不断改进的即时更新能力的强大安全性功能，可以有效防止欺诈和非法交易。

## Show me the code

### solidity

```solidity
...
function makeFlashLoan(
    IERC20[] memory tokens,
    uint256[] memory amounts,
    bytes memory userData
) external {
    vault.flashLoan(this, tokens, amounts, userData);
    emit eventLog("making flash...");
}
```

完整代码：
<https://gist.github.com/coffiasd/440c42e5a0211d096080ad5b1b50b4ae>

### 部署代码

```javascript
const hre = require("hardhat");

async function main() {
  const Swap = await hre.ethers.getContractFactory("Swap");
  const swap = await Swap.deploy();
  await swap.deployed();
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

### 测试代码

```javascript
const hre = require("hardhat");

async function main() {
  const Bundle = await hre.ethers.getContractFactory("Swap");
  const bundle = Bundle.attach("替换成部署完的合约地址");
  console.log(bundle.address);
  const tokenIn = "0xB4FBF271143F4FBf7B91A5ded31805e42b2208d6"; //weth
  let ret = await bundle.makeFlashLoan(
    [tokenIn],
    [hre.ethers.utils.parseEther("1")],
    []
  );
  console.log(ret);
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

### hardhat.config.js 配置

```shell
require("dotenv").config();
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-etherscan");

module.exports = {
  solidity: "0.8.0",
  networks: {
    goerli: {
      url: [process.env.GOERLI_ENDPOINT],
      accounts: [process.env.PRIVATE_KEY],
    }
  }
};
```

## 运行代码

### 运行部署代码

```shell
npx hardhat run .\scripts\swap.js --network goerli
```

### 运行测试代码

```shell
npx hardhat run .\scripts\test.js --network goerli
```

## 输出

![alt ""](/images/balancer.png)
