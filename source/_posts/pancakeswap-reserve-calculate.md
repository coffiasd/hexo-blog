---
title: pancakeswap-reserve-calculate
date: 2023-02-13 10:12:03
tags:
  - pancakeswap
  - defi
categories:
  - solidity
---

## pancakeswap 如何通过 reserve 计算 amountOut 金额

### 无常损失

当你成了一个 AMM 的 LP(流动性提供者)的时候，那你的收益部分就来一字无常损失。
同时无常损失也不是永久性的，价格经过短暂的下跌之后又重新恢复。为什么会在短时间内恢复呢？正式通过一个个搬砖的人监控不同池子中的价格差价进行套利操作后使得不同池子中的价格达到一个平衡。无常损失的核心公式:

$$
K = A * B
$$

假设我们的池子中有两种货币 token0 和 token1,A 代表 token0 在池子中的剩余数量,B 代表 token1 在池子中的剩余数量，而 K 的值在池子创建的时候就固定了。并且在流动性变化的过程中 K 的值不是完全相等的，而是会有细微的偏差但是不会太大。
当价格发生变化的时候,价格我们兑换数量 amountIn 的 token0，为了使得 K 的值是相等的会得到一下的公式

$$
K = (A+amountIn)(B-amountOut)

amountOut = B - K/(A+amountIn)
$$

### 举个例子

下面我们拿一个 pancakeswap 的池子来实际算一下这个 amountOut(我们预估能得到的金额)。
为了测试方便我们拿了 bsc 测试网的池子，包含了 BUSD(0xab1a4d4f1d656d2450692d237fdd6c7f9146e814)和 WBNB(0xae13d989dac2f0debff460ac112a837c89baa7cd)两种货币。
<https://testnet.bscscan.com/address/0x58C6Fc654b3deE6839b65136f61cB9120d96BCc6>
通过 contract 的 ABI 可以看到相关的几个参数:

- kLast
- token0
- token1
- getReserves
  我们也可以直接通过 UI 界面找到相关的参数
  ![alt "ABI"](/images/pancakeswap-read.png)

### 代码实现

这里我通过 python 读取了相关池子中的参数值。

```python
from web3 import Web3
import json

# init w3 provider
# w3 = Web3(Web3.HTTPProvider("https://bsc-dataseed.binance.org"))
w3 = Web3(Web3.HTTPProvider("https://data-seed-prebsc-1-s1.binance.org:8545"))


def fetchPool(poolAddress):
    with open("./abi/pancake.json", 'r') as json_file:
        contract = w3.eth.contract(
            address=poolAddress, abi=json.load(json_file))
        reserves = contract.functions.getReserves().call()
        # arr [reserve0, reserve1, timestamp]
        return reserves

# 费用
fee = 0.0025

arr = fetchPool("0xa96818CA65B57bEc2155Ba5c81a70151f63300CD")
# token0 剩余数量
r0 = arr[0]
# token1 剩余数量
r1 = arr[1]
# K的值
k = r0 * r1
amountIn = 10**16
price1 = r1 - k/(r0+amountIn*(1-fee))
print(price1/10**18)
# output
# 606895316154
```

![alt "ABI"](/images/pancakeswap-read2.png)
实际预估的价格和计算的价格是对的上的。
