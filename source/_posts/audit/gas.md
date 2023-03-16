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

<details>
  <summary> Save gas with the use of the import statement</summary>
    Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct polluted the source code with an unnecessary object we were not using because we did not need it.
    This was breaking the rule of modularity and modular programming: only import what you need Specific imports with curly braces allow us to apply this rule better.

    Recommendation:
    import {contract1 , contract2} from "filename.sol";

</details>

<details>
  <summary> Sort Solidity operations using short-circuit mode</summary>
    //f(x) is a low gas cost operation 
    //g(y) is a high gas cost operation 
    //Sort operations with different gas costs as follows 
    f(x) || g(y) 
    f(x) && g(y)

</details>

<details>
  <summary>Change for loop behavior by removing add (+1) and ++x is more gas efficient</summary>
  ```javascript
    function buy(uint256 _amount) external payable {
            ...
    -       for (uint48 x = sale_.currentId + 1; x <= newId; x++) {
    +       for (uint48 x = sale_.currentId; x < newId; ++x) {
                nft.mint(msg.sender, x);
            }
            ...
    }
  ```
</details>

- Multiple access to mapping/array should use local variable cache
- Duplicated require should be modifier or function
- Use custom error rather than revert()/require()
- Use calldata instead of memory for read-only variable
- require()/revert() string longer than 32 bytes cost extra gas
- Using Openzeppelin Ownable2Step.sol is gas efficient
- Using UniswapV3 mulDiv function is gas-optimized
- Use nested if and, avoid multiple check combinations
- Avoid using state variable in emit (130 gas)
- Instead of cache a whole object ,try cache single Attributes
- Using int32 for time
- Don't use \_msgSender() if not supporting EIP-2771
- Using > 0 costs more gas than != 0 when used on a uint in a require() statement(version>0.8.13)
- Using bools for storage incurs overhead
- <array>.length should not be looked up in every loop of a for-loop
- Using calldata instead of memory for read-only arguments in external functions saves gas
- Splitting require() statements that use && saves gas
- Use a more recent version of solidity
- require()/revert() strings longer than 32 bytes cost extra gas
- <x> += <y> costs more gas than <x> = <x> + <y> for state variables
- Using storage instead of memory for structs/arrays saves gas
