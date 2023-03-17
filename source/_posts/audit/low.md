---
title: low level vulnerablity
date: 2023-03-17 11:05:32
tags:
  - solidity
  - web3 security
  - code4rena
  - gas saving
categories:
  - audit
---

<details>
  <summary>Use .call instead of .transfer to send ether</summary>
  .transfer will relay 2300 gas and .call will relay all the gas. If the receive/fallback function from the recipient proxy contract has complex logic, using .transfer will fail, causing integration issues.
</details>

<details>
  <summary>Unbounded loop</summary>
    ```javscript
        function claimGovFees() public {
        address[] memory assets = bondNFT.getAssets();

        for (uint i=0; i < assets.length; i++) {
            uint balanceBefore = IERC20(assets[i]).balanceOf(address(this));
            IGovNFT(govNFT).claim(assets[i]);
            uint balanceAfter = IERC20(assets[i]).balanceOf(address(this));
            IERC20(assets[i]).approve(address(bondNFT), type(uint256).max);
            bondNFT.distribute(assets[i], balanceAfter - balanceBefore);
            }
        }
    ```

</details>

<details>
  <summary>Use time units directly</summary>
    ```javascript
    uint constant private DAY = 24 * 60 * 60;
    ```
</details>

<details>
  <summary>Chainlink price feed is not sufficiently validated and can return stale price</summary>
    ```javascript
      (uint80 roundId, int256 assetChainlinkPriceInt, , uint256 updatedAt, uint80 answeredInRound) = IPrice(_chainlinkFeed).latestRoundData();
      require(answeredInRound >= roundId, "price is stale");
      require(updatedAt > 0, "round is incomplete");
    ```

</details>

- Use the safe variant and ERC721.mint
- Add an event for critical parameter changes
- Declare interfaces on separate files
- Constants should be upper case
- Replace constant private with private constant
