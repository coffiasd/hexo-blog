---
title: What is ERC20
tags:
  - solidity
categories:
  - solidity
date: 2022-11-11 19:22:57
---

# what is ERC20 ?

<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol>

## ERC20 中的 state 变量

```
mapping(address => uint256) private _balances;
mapping(address => mapping(address => uint256)) private _allowances;
uint256 private _totalSupply;

string private _name;
string private _symbol;
```

- \_balances:
  储存每个地址的余额
- \_allowances:
  在\_approve()函数中使用，授权给目标地址固定的提款额度
- \_totalSupply(e.g.: 10000000000000000000000000000)
- \_name(e.g.: Matic Token)
- symbol(e.g.: MATIC)

## \_appove

![alt "appove"](/images/erc20-1.png)

## transfer

public 属性且可以被重写,简单的调用了共用方法获得了 msg.sender 的地址调用了下面的\_transfer 函数
![alt "transfer"](/images/erc20-2.png)

## \_transfer

内部函数可以被重写
![alt "_transfer"](/images/erc20-3.png)

## allowance

![alt "allowance"](/images/erc20-4.png)

## transferFrom

![alt "transferFrom"](/images/erc20-5.png)
