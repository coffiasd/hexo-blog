---
title: how-to-become-smart-contract-audit
date: 2023-02-15 23:00:03
tags:
  - solidity
  - auditor
categories:
  - solidity
---

## 如何成为一名智能合约审计员?

## 怎么样才能成为出色的审计员?

### 学习编程

我希望你已经学习过编程，任何语言都可以。如果你没有学过编程，那么你首选得学会如何编程因为审计代码需要能够阅读和理解代码。在我看来成为一名开发人员是前置条件，否则你将会花费太多的时间来试图理解语法和它的语义。这就像你在不认识文字的情况下，试图去理解尼采的文章。所以我们必须先学会编程。学习编程确实是最花费时间的，后面学习安全方面的知识反而会快一点。
如果你没有编程经验，做好心理准备这会话费你数年的时间学习之后你的审计才会变得有用。我最开始使用的是 JavaScript，这是一门非常适合新手入门的语言。如果后面你发现你不适合成为一名审计人员，那么你也可以转行去做前端，或者是后端开发亦或是合约开发人员。Solidity 和 JavaScript 的语法是有些相似的。

### 学习 ETH blockchain 和 solidity 基础

当你看到这里的时候我认为你已经知道如何去编程但是还不了解以太坊和 solidity。学习一门语言最好的方式是多练习，关阅读程序的语法之类的信息是没有办法真正学会的。最好的学习 solidity 和以太坊的方式是解决 CTFs。

> CTFs(Capture the flag/wars): 存在漏洞的代码的安全挑战，你需要编写智能合约来利用改漏洞。
> 我个人经常使用的 3 个 CTFs，来帮助学习

- Damn Vulnerable DeFi <https://cmichel.io/damn-vulnerable-de-fi-solutions/>
- Ethernaut <https://cmichel.io/ethernaut-solutions/>
- Capture The Ether <https://cmichel.io/capture-the-ether-solutions/>
  Ethernaut 和 Capture The Ether 经常有重叠的部分，并且一些漏洞只适用于老版本的 solidity。需要注意的是你在现在的代码中可能不会看到这些个漏洞。

> 这些 CTFs 最初都是创建在以太坊测试网，我们很难从中得到任何 eth 资金，并且这个开发的过程也是很麻烦的。我推荐使用现在测试框架和分支来解决这些挑战,这部分内容在<https://cmichel.io/ethernaut-solutions/>有描述。

也有很多比较难一点的 CTFs，比如说 Paradigm’s CTF<https://cmichel.io/paradigm-ctf-2021-solutions/>。在这些比赛中获得比较好的排名对求职非常有帮助，特别在你不是很出名的情况下。

### 熟悉常用的智能合约

在你的整个审计生涯中你会看到很多类似的合约、模式、甚至是算法。熟悉这些合约很有必要，而且你必须了解他的运行原理和细微的差别。

- token：
  最常使用的 token 标准是 EIP20 用于同质化货币，EIP721 用于非同质化货币(NFT)。还有许多别的标准但是这两个是你在入门的时候最需要知道的。对于理解最初的 ERC20 标准改进了很多是很重要的，并且很多 token 是没有遵守 EIP20 标准的（就像 USDT）。你也必须明白不同的货币有不同位数的小数点，它们被解析为具有小数精度的浮点数，eg: 1e18 TOKENS (= 10\*\*18 TOKENS) ~ 1.0 TOKENS,对于一个有 18 位数的货币来说。你会遇到很多像其中一些计算的货币是错误的小数点位数的错误。
- Proxies：
  以太坊的合约是不可以升级的。如果你想更新代码的话，那么你必须要重新部署一份新的合约。但是这意味着你存储在合约中的变量也会随之丢失。所以 proxies 代理用于分隔存储数据和逻辑。代理有很多的实现方式，可以先去了解一下 OpenZeppelin Proxy。你就会明白 delegatecall 对构建代理的重要性。

- MasterChef：
  MasterChef 是一个质押合约，按照用户质押在流动池中的 token 数和质押时间来收取利润(time\*stakeAmount)。这个合约已经有多个分支，但是它们还是如此重要的原因是这个回报算法出现在很多个场景中。Paradigm 称之为 Billion-dollar algorithm。你需要明白它们的运行逻辑和为什么需要它们。
- Compound：
  我曾经说说 Compound 是所有去中心化借贷协议的基础。你也需要了解它因为很多 defi 项目和借贷协议交互的方式是相同的。它的代码对比 aave 会更加清晰，并且文档也更加容易理解。
- UniswapV2：
  虽然 uniswap 已经发布到 v3 版本，但是 uniswap v2 显然更加简单节省 gas 费，并且仍然是理解 AMM 的基础。你还需要了解 LP token 的运行机制。

### 学习金融基础知识

会有这么一段时间当你审计一段 defi 项目的时候，这个项目使用了很多传统金融条款，但是你可能不太了解。当你看到这些条款的时候，它们可能会引申出更多你不清楚的条款。所以学习一些金融基础课程对于审计代码也是有所帮助的。
