---
title: Four primitive data type
tags:
  - solidity
categories:
  - solidity
date: 2022-11-11 13:58:12
---

## 这里我们介绍 4 种 solidity 的原始数据类型

- boolean
- uint
- int
- address

### uint

uint 代表了无符号的整形,包含以下的几个范围的变量

- uint8 大小从 0 - 2\*\*8 -1
- uint16 大小从 0 - 2\*\*16 -1
- uint32 大小从 0 - 2\*\*32 -1
- uint64 大小从 0 - 2\*\*64 -1
- uint128 大小从 0 - 2\*\*128 -1
- uint256 大小从 0 - 2\*\*256 -1

声明方式:
uint8 public u8 = 1;
当我们声明 uint 的时候没有指定范围的时候默认为 uint256:
**uint public u = 1; 这里的 u 为 uint256**
在具体的使用过程中我们可以根据范围需求去决定具体使用哪个范围的 uint.

### int

int 代表了有符号的整形,和 uint 相似，只是因为占用了一位来代表符号导致了最终的范围和 uint 不一样.
举个例子:
**int256 的范围从 -2 ** 255 到 2 ** 255 - 1**

### 范围测试

我们可以直接在 <http://remix.ethereum.org/> 上进行测试，得到最终的返回值.

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract Learn{
    uint8 public u = 1;

    constructor(){
        u = 20;
    }

    function testUintRange() public pure returns (uint8){
        // 这个输出是255
        return type(uint8).max;
    }

}
```

### boolean 值

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract Learn{
    bool public defaultBoo = true;

    function testUintRange() public view returns (bool){
        return defaultBoo;
    }

}
```

### address

ETH 使用 hash 函数（keccak-256)来生成地址。在 ETH 或者 solidity 中，一个地址由 20byte 长度的值组成，这个地址对应于公钥的 hash 函数(keccak-256)的最后 20bytes 的值。一个地址总固定是以 ox 开头,因为它是以 16 进制的格式表示的.

```
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract Learn{
    address public a = 0xE2bCb3e6e03B477DA0698e1b4ECf570D06B34Ce7;

    function testAddress() public view returns (address){
        return a;
    }

}
```
