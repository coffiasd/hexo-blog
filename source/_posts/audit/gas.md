---
title: Solidity gas saving tips(to be continued)
date: 2023-03-13 07:19:55
tags:
  - solidity
  - web3 security
  - code4rena
  - gas saving
categories:
  - audit
---

<details>
  <summary>Using delete instead of setting struct 0 saves gas</summary>
    ```javascript
    -   pool.long0ProtocolFees = 0;
    +   delete pool.long0ProtocolFees;
    ```
</details>

<details>
  <summary>Saving on gas costs should be prioritized</summary>
    ```javacript
    +    require(block.timestamp <= deadline, "KYCRegistry: signature expired");

        require(
        !kycState[kycRequirementGroup][user],
        "KYCRegistry: user already verified"
        );
    -    require(block.timestamp <= deadline, "KYCRegistry: signature expired");
        bytes32 structHash = keccak256(
        abi.encode(_APPROVAL_TYPEHASH, kycRequirementGroup, user, deadline)
        );
    ```

</details>

<details>
  <summary>The way to save on gas costs with a for loop</summary>
   ```javascript
    -    for (uint256 i = 0; i < exCallData.length; ++i) {

    +    for (uint256 i = 0; i < exCallData.length;) {
    +      unchecked {
    +        ++i;
    +      }
        }
    }
    ```

</details>

<details>
  <summary>No necessary global variable read</summary>

```javascript
-    doTransferOut(admin, reduceAmount);
+    doTransferOut(payable(msg.sender), reduceAmount);
-    emit ReservesReduced(admin, reduceAmount, totalReservesNew);
+    emit ReservesReduced(msg.sender, reduceAmount, totalReservesNew);

```

</details>

<details>
  <summary>Cache mapping instead of reading multiple times</summary>
  ```javascript
    -    if (fTokenToUnderlyingPrice[fToken] != 0) {
    -      return fTokenToUnderlyingPrice[fToken];
    +    uint256 fToken = fTokenToUnderlyingPrice[fToken];
    +    if(fToken != 0) {
    +      return fToken;   
        }
  ```
</details>

<details>
  <summary>Immutable save more gas</summary>
  ```javascript
    -  address public owner;
    +  address public immutable owner;
  ```
</details>

<details>
  <summary>mapping(address⇒bool) using bool for storage incurs overhead</summary>
  ```javascript
   -  mapping(address => bool) public allowedAsset;
   +  mapping(address => uint256) public allowedAsset;
  ```
</details>

- Multiple access to mapping/array should use local variable cache
- Duplicated require should be modifier or function
- Use custom error rather than revert()/require()
- Use calldata instead of memory for read-only variable
- require()/revert() string longer than 32 bytes cost extra gas
