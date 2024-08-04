---
title: pyth-docs
date: 2024-08-04 16:25:46
tags:
  - pyth
categories:
  - solidity
---

## How Pyth Works

![](https://raw.githubusercontent.com/coffiasd/images/main/202408041734938.png)

### flowchart
- price data pushlisher submit data to pyth's oracel program
- pyth's oracel program combines all price data and compute the final price based on it's own algorithm
- pyth transfer price data from pythnet to mutiple target chains eg,:evm,bnb,arbiturm
- hermes(web service) listens to price update.
- price consumer get the latest price from hermes(web service),verify the lastest price data on chain via oracel program on each chain,the program then return the price once price data is valid

## Pyth Methods On EVM Table

| Method                      | Need check publishTime | Most recent price |
| --------------------------- | ---------------------- | ----------------- |
| getPrice                    | ✗                      | ✓                 |
| getPriceUnsafe              | ✓                      | ✗                 |
| getPriceNoOlderThan         | ✗                      | ✗                 |
| getEmaPrice                 | ✗                      | ✗                 |
| getEmaPriceUnsafe           | ✓                      | ✗                 |
| getEmaPriceNoOlderThan      | ✗                      | ✗                 |
| getUpdateFee                | ✗                      | ✗                 |
| getValidTimePeriod          | ✗                      | ✗                 |
| parsePriceFeedUpdate        | ✗                      | ✗                 |
| parsePriceFeeUpdateUnique   | ✗                      | ✗                 |
| updatePriceFeeds            | ✗                      | ✓                 |
| updatePriceFeedsIfNecessary | ✗                      | ✗                 |

## Pyth Methods On EVM

### getPrice
arguments:
- price feed id

getPrice() revert due to the price has not been update within getValidTimePeriod() seconds which is typically 1 minute.
```shell
EVM call reverted with exception:

StalePrice()
```

let's take eth/usd as example, the return price is `291769250000`, and the price is `2917.69250000` usd.
The only argument is price feed id which is a 32-byte id written as a hexadecimal string.For eth/usd it's `0xff61491a931112ddf1bd8147cd1b641375f79f5825126d665480874634fd0ace`. You can get the full list from <https://pyth.network/developers/price-feed-ids> page.
```shell
EVM call succeeded with result:

{
  price: {
    price: 291769250000,
    conf: 166231345,
    expo: -8,
    publishTime: 1722775361,
  },
}
```

### getPriceUnsafe
arguments:
- price feed id
  
The input arguments and return data format for getPriceUnsafe() are identical to those of the getPrice() function. However, the key distinction lies in their behavior: getPriceUnsafe() **may return a price from an arbitrary point in the past**, potentially outdated. Consequently, the responsibility falls on the caller or price consumer to ensure the returned data meets their recency requirements. This involves comparing the current block.timestamp with the returned publishTime to verify that the price update is sufficiently recent for their specific use case.

### getPriceNoOlderThan
arguments:
- price feed id
- age

This function reverts with a PriceFeedNotFound error if the requested feed id has never received a price update. Calling **updatePriceFeed** to fix this issue.

### getEmaPrice
arguments:
- price feed id

Get the latest exponentially-weighted moving average (EMA) price and confidence interval for the requested price feed id.
```shell
EVM call succeeded with result:

{
  price: {
    price: 6088503200000,
    conf: 2786383200,
    expo: -8,
    publishTime: 1722780106,
  },
}
```

### getEmaPriceUnsafe
arguments:
- price feed id
**This function may return a price from arbitrarily far in the past**
Get the latest exponentially-weighted moving average (EMA) price and confidence interval for the requested price feed id.

### getEmaPriceNoOlderTham
arguments:
- price feed id
- age
Get the latest exponentially-weighted moving average (EMA) price and confidence interval for the requested price feed id
The caller provides an age argument that specifies how old the price can be.The call can be revert due to `StalePriceError`.

### getUpdateFee
arguments:
- updateData

Note that `updataData` is retrieved from `Hermes` REST API.

```shell
EVM call succeeded with result:

{
  feeAmount: 1,
}
```

### getValidTimePeriod
Get the default valid time period in seconds.
Above some getting function revert if current on-chain price is older than this period.
```shell
EVM call succeeded with result:

{
  validTimePeriod: 60,
}
```

### parsePriceFeedUpdates
arguments:
- updateData* hex[]
- priceIds* bytes32[]
- minPublishTime* uint64
- maxPublishTime* uint64
- fee* wei

Parse updateData and return the price feeds for the given priceIds within, if they are all published between minPublishTime and maxPublishTime (minPublishTime <= publishTime <= maxPublishTime).
Please note that this function not returned the most recent price,instead it returned a price for fixed time(minPublishTime <= publishTime <= maxPublishTime).
If caller need a most recent price they should call updatePriceFeeds followed by getPrice or one of its variants.

**Unlike updatePriceFeeds, calling this function will not update the on-chain price**

### parsePriceFeedUpdatesUnique.
arguments:
- updateData* hex[]
- priceIds* bytes32[]
- minPublishTime* uint64
- maxPublishTime* uint64
- fee* wei
  
the price update is the earliest update after the minPublishTime.

condition:prevPublishTime < minPublishTime <= publishTime <= maxPublishTime

### updatePriceFeed
arguments:
- updateData* hex[]
- fee* wei
  
**Update the on-chain price feeds using the provided updateData**
Caller first retrieve a latest price from `Hermes` REST API,and then invoke this function to update the on-chain price if this price is more recent than on-chain price,otherwise the provided update will be ignored.

### updatePriceFeedsIfNecessary
arguments:
- updateData* hex[]
- priceIds* bytes32[]
- publishTimes* uint64[]
- fee* wei

Update the on-chain price feeds using the provided updateData if the on-chain data is not sufficiently fresh. The caller provides two matched arrays, priceIds and publishTimes. This function applies the update if there exists an index i such that priceIds[i]'s last publishTime is before than publishTimes[i]. Callers should typically pass publishTimes[i] to be equal to the publishTime of the corresponding price id in updateData. If this condition is not satisfied, the call will revert with a NoFreshUpdate error.

## Resources
PythOracle:if price.expo is less than 0, wrong prices will be recorded <https://github.com/sherlock-audit/2023-07-perennial-judging/issues/56>

Using stale price in Pyth Network <https://solodit.xyz/issues/m-07-using-stale-price-in-pyth-network-pashov-none-astrolab-markdown>