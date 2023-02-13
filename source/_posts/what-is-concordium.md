---
title: what-is-concordium
date: 2023-02-13 13:34:17
tags:
  - hackathon
  - concordium
categories:
  - hackathon
---

## concordium

### 概览

Concordium 区块链是由科学支持的合规性区块链，可实现私人和公共交易。
它提供高吞吐量、快速交易和可预测的费用,允许企业、开发商和贸易商利用区块链的真正力量和潜力。在您开始使用 Concordium 区块链之前，最好了解一些关于我们独特的区块链的基本概念

### 身份

#### 关于身份

账号和身份在 Concordium 平台上紧密关联。为了能够持有、发送、接收 CCD 或者成为 Concordium 平台上的 baker,你必须要有一个账号和身份。这和你是否使用 Concordium 硬件钱包,还是安卓钱包，还是桌面客户端亦无关。
在你开始使用 Concordium 平台之前，身份验证者需要验证你在真实世界的身份信息。这个验证操作在你创建账号的时候进行。
身份由身份验证者提供。目前已经有注册了一些身份验证者并且他们的联系方式都是公开在平台区块链上的。平台基金会持续维护这份名单。

#### 身份属性

每个身份信息包含了一些加密值，和一些用户选择的属性，例如国籍或者居住的国家。这些信息由身份验证者认证。这些加密值由公钥和私钥，身份验证者的签名，还有一些创建账号所需的秘钥值所组成的。
你可以控制哪些属性可以对外公开。你也可以选择完全不透露任何信息保持匿名，这种方式比较推荐。

#### 获得身份

你选择可以在桌面钱包、硬件钱包、安卓客户端、网页钱包中的其中一种来创建你的身份。身份创建是一个链下的操作。如果你在移动设备上创建你的身份，我们强烈推荐你使用安卓客户端。
![alt ""](images/concordium-identity-creation.png)

### 账号

#### 关于账号

Concordium 上的账号属于一个或者多个凭证持有者。一个账号由链上和链下两部分组成。

链上:

- 凭证
- 公开余额
- 隐藏余额
- 账号序列号
- 公钥

链下：

- 私钥
- 创建账号的身份
- 解密秘钥用于私密转账
  Concordium 提供了多种和链上账号交互的方式。线下部分的账号可以在不用的设备之间转账。同一个账号可以在不同的设备中同时使用。链上的账号通过账户地址 32-bytes 标识。eg:3ZFGxLtnUUSJGW2WqjMh1DDjxyq5rnytCwkSqxFTpsWSFdQnNn

  在某些情况下你可能需要对外暴露你的国籍，由于用户可以关联多个账号，某些账号暴露了国籍信息，其它账号匿名了国籍信息，当你需要的时候你可以使用这个暴露了国籍信息的账号，并且让其它的账号保持匿名。如图所示:
  ![alt ""](/images/concordium-account-creation.png)

### 钱包

Concordium 有各种平台的钱包

- Web 插件钱包
- 安卓客户端钱包
- 硬件钱包

为了使用方便我这边选择了 web 端的浏览器插件钱包

![alt "wallet"](/images/concordium-account-creation.png))

在测试网创建一个身份
![alt "wallet2"](/images/concord-wallet2.png)

在创建了身份之后，我们可以通过身份去创建账号。
![alt "account"](images/concordium-account.png)

### 使用 ID

Concordium 的浏览器钱包提供了一些证明，当用户遇到需要一些认证的场景时，列如证明用户的年龄是否达到一定年龄时，或者用户地区的一些认证。这个钱包的所有者这个时候可以选择是否像应用提供这些信息。应用会构建一个声明列表，以请求对应的属性的零知识证明列表，而不会透露声明的真实内容。

![alt "use id"](images/concordium-use-id.png)

对于 dapp 在使用这些证明的时候需要遵守一些规则。

- 对使用的属性的数量上市没有限制的。
- 一个属性一次只能用于一个证明

可以显示的属性有：

- First name
- Last name
- Sex
- Date of birth
- Country of residence
- Country of nationality
- ID document type
- ID document number
- ID document issuer
- ID valid from
- ID valid to
- National ID number
- Tax ID number

要求用户暴露属性的格式

```json
[
  {
    "type": "RevealAttribute",
    "attributeTag": "countryOfResidence"
  }
]
```

范围证明的格式

```json
{
  "challenge": "a54bc4116655d247fa625d98f768d4d81e55ffe26ac6bab259bad5895d49ae00",
  "statement": [
    {
      "type": "AttributeInRange",
      "attributeTag": "dob",
      "lower": "19571212",
      "upper": "19971212"
    }
  ]
}
```

证明是否包含在成员中

```json
{
  "challenge": "6c7d69b121d4ce829392d9f2b16395708a458f6183caa20e9074e7283e377418",
  "statement": [
    {
      "type": "AttributeInSet",
      "attributeTag": "nationality",
      "set": ["DK", "FI", "IS", "NO", "SE"]
    }
  ]
}
```

### 资源

- [1].blockchain explorer <https://ccdscan.io/>
- [2].js SDK <https://github.com/Concordium/concordium-node-sdk-js>
- [3].developer <https://developer.concordium.software/en/mainnet/net/guides/developer-page.html>
- [4].website <https://developer.concordium.software/en/mainnet/index.html>
