---
title: How to write a reserve plugin part 2
date: 2022-10-31 11:01:53
tags:
  - hackathon
  - reserve
categories:
  - hackathon
---

## Contructor params of a collateral contract

- fallbackPrice
- chainlinkFeed
- ERC20+rewardERC20
- maxTradeVolume
- oracleTimeout
- targetName
- delayUnitDefault

## What's fallbackPrice?

fallback price is when we try to get the real price but revert then we provide a fallback price.
fallback price is return when function price(bool allowFallback) is called and the param past in is true.
In the price() function we'll call strictPrice() function first once it revert we'll check if the value of allowFallback is true. If false return value is (isFallback-bool,fallbackPrice)
the unit of fallback price if {UoA}

## What's chainlinkFeed ?

Firstly ,it's been created by chainlink.The interface allows your contract run functions on that deployed aggregator contract. For example ,you can use chainlink data feed to connect your smart contract to asset pricing data like the ETH/USD feed.
the unit of feed is {UoA/tok}

## What's ERC20 ?

this is import from openzeppein/contracts/token/ERC20/extensions/IERC20Metadata.sql
IERC20Metadata is inherit IERC20
some function like transfer,totalSuoply,balanceOf,approve,transferFrom all we need to create an ERC20 token.

## What's max trade volume?

the unit of max trade price if {UoA}

## What's oracle timeout?

the number of seconds until a oracle value becomes invalid.Otherwise we have to change the collateral status by calling markStatus().

## What's target name?

the canonical name of this collateral's target unit.

## What's delayUnitDefault?

the number of seconds an oracle can mulfunction.
this value is used when we call markStatus() function.
when collateral status is "IFFY" . smart contract compare blocktime+delayUnitDfault and \_whenDefault and then return the min(Math.min) one?
