---
title: What is solidity unchecked
tags:
  - solidity
categories:
  - solidity
date: 2022-11-11 20:34:37
---

## unchecked 的官网定义

unchecked 在 solidity 0.8 版本中加入，先看下官网的定义

- Arithmetic operations revert on underflow and overflow. You can use unchecked { ... } to use the previous wrapping behaviour.
- Checks for overflow are very common, so we made them the default to increase readability of code, even if it comes at a slight increase of gas costs.
  因为检查溢出的情况非常普遍，所以即使会增加 gas 费用我们还是将检查溢出设置为默认情况。但是在 0.8 版本之后你可以在函数中添加 unchecked{...}块来使这部分代码不需要再考虑溢出的情况。

接下来我们使用一个例子看下具体的区别

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Counter {
    uint8 public count = 0;

    function increment() external returns(uint8) {
        count --;

        return count;
    }
}
```

![alt "unchecked"](/images/unchecked-1.png)
溢出之后直接报错

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract Counter {
    uint8 public count = 0;

    function increment() external returns(uint8) {
        unchecked{
            count --;
        }

        return count;
    }
}
```

![alt "unchecked"](/images/unchecked-2.png)
允许溢出 count 的值变为 type(int8).max
