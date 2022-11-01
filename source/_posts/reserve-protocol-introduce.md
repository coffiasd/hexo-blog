---
title: Reserve protocol introduce
date: 2022-10-28 12:10:07
tags:
  - hackathon
  - reserve
categories:
  - hackathon
---

## All you need to know for writing a reserve protocol collateral plugins quick start

core plugin depends on two plugin types:

- Assets / Collateral (contracts/plugins/assets)
- Trading (contract/plugins/trading) not discussed here

### Collateral is erc20 token + below:

- if ERC20 back ?
- refresh()
- status() SOUND/IFFY/DISABLED
- rate exchange

### Monetary units

- Unit of Account
- Target unit
  RToken maintaining stability or appreciation against its target unit
- Reference unit

### e.g. of 3 different tokens

- Collateral : cUSDC
- Refence : USDC
- Target : USD

### Units definition

- {UoA} unit of account recommand USD here
- {tok} whole token
- {ref} whole reference token
- {target} whole target unit
- {ref/tok} refPerTok()
- {target/ref} targetPerRef()
- {UoA/target} pricePerTarget()

### Basket definition

- Prime basket
  this is set by governance , on changes through successful governance proposals. it consists of an array of triples
  <collateral token,target unit,tartget amount>
  e.g. : <cUSDC,USD,0.1>
- Reference basket
  <collateral token,reference unit,reference amount>
  e.g. : <aDAI,DAI,0.33>
- Collateral bascket
  <collateral token,token amount>
  e.g.: <cUSDC,0.29>

### IAsset interface

![alt interface](/images/reserve.jpg)

### Accounting units

before we create a collateral plugin we have to choose 3 accounting units

### Collateral unit

its just erc20 token

### Reference unit

to be ask , what's a unit that this collateral token will always be worth the same value or more of .

### Target unit

target unit has to do with a concept called the target basket. what's prime basket ?
A example of linear combination of target units:

- 1 USD
- 0.5 USD + 0.55 EURO
- 0.5 USD + 0.35 EURO + 0.00001BTC

### Unit of account

we can assume UoA == USD , because for now USD price are king.

### Important properties for collateral plugins

**Collateral plugins should be permisionless and should be able to used by any number if RTokens**
**Token balance can't be rebasing**

### refresh() should never be revert

refresh is been called before any transactions , it return exchange rates.
if occur an important error , refresh should change the state to **DISABLED**

### strictPrice() price(bool) status()

### the IFFY status should be temporary

### Collateral must default if refPerTok() falls

### Defaulted Collateral must stay defaulted

if status() ever returns disabled , then it must always return disabled in the nearly future

### Token rewards should be claimable

- rewardERC20()
- getClaimCalldata()

### Smaller Constraints

The value of the following methods should never change:

- isColleteral()
- targetName()
- erc20()
- rewardERC20()
- erc20Deciamls()

### Function-by-function walkthrough

- strictPrice() {UoA/tok}
- price(bool) {UoA/tok}
  if revert depends on the bool value true or false
- refPerTok() {ref/tok}
- targetPerRef() {target/ref}
- pricePerTarget(){UoA/tartget}
