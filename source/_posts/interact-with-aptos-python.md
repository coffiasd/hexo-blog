---
title: interact-with-aptos-python
date: 2023-02-09 10:04:32
tags:
  - aptos
categories:
  - aptos
---

## 安装 python SDK

### pip

```
pip install aptos-sdk
```

### 源码安装

```
git clone https://github.com/aptos-labs/aptos-core
cd aptos-core/ecosystem/python/sdk
python setup.py install --user
```

### embedding

```
cd /path/to/python/project
cp -r /path/to/aptos-core/ecosystem/python/sdk/aptos-sdk aptos-sdk
```

## 账号创建 && 转账

<https://gist.github.com/coffiasd/89a812c66ab10785bc8abd7dc6bc1ca4>

```
python main.py
=== Addresses ===
Alice: 0x6a377fbf8e07576d8b11b7104a9100d6ecadfc9a6a3d809105b3822e84f170b6
Bob: 0x04807454e47d0c4e733ccec4de02cfc8d0cfb28e2c822db60e4bbffb5d0d821c

=== Initial Balances ===
Alice: 100000000
Bob: 0

=== Intermediate Balances ===
Alice: 99944800
Bob: 1000

=== Final Balances ===
Alice: 99889600
Bob: 2000
```
