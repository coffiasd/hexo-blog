---
title: union finance high vulnerable on sherlock audit
date: 2023-03-04 19:08:56
tags:
  - solidity
  - vulnerable
  - audit
categories:
  - solidity
  - audit
---

## union finance high vulnerable on sherlock audit

### Link

[Full Code Url](https://github.com/sherlock-audit/2023-02-union/blob/main/union-v2-contracts/contracts/user/UserManager.sol#L838-L870)

```solidity
 stakers[vouch.staker].lockedCoinAge +=
 (block.number - _max(lastWithdrawRewards, uint256(vouch.lastUpdated))) *
                uint256(vouch.locked);
```

### Summary

Voucher can be counted arbitrary many times in staker's lockedCoinAge. If a voucher has maximized its trust then its locked is added to the lockedCoinAge each time fully as its lastUpdated is kept intact. This provides a surface to grow lockedCoinAge as big as an attacker wants, increasing it by current_block_difference \* vouch.locked on each transaction.

### Vulnerability Detail

Code Detail:

```solidity
uint256 lastWithdrawRewards = getLastWithdrawRewards[vouch.staker];
stakers[vouch.staker].lockedCoinAge +=
    (block.number - _max(lastWithdrawRewards, uint256(vouch.lastUpdated))) *
    uint256(vouch.locked);
if (lock) {
    // Look up the staker and determine how much unlock stake they
    // have available for the borrower to borrow. If there is 0
    // then continue to the next voucher in the array
    uint96 stakerLocked = stakers[vouch.staker].locked;
    uint96 stakerStakedAmount = stakers[vouch.staker].stakedAmount;
    uint96 availableStake = stakerStakedAmount - stakerLocked;
    uint96 lockAmount = _min(availableStake, vouch.trust - vouch.locked);
    if (lockAmount == 0) continue;
    // Calculate the amount to add to the lock then
    // add the extra amount to lock to the stakers locked amount
    // and also update the vouches locked amount and lastUpdated block
    innerAmount = _min(remaining, lockAmount);
    stakers[vouch.staker].locked = stakerLocked + innerAmount;
    vouch.locked += innerAmount;
    vouch.lastUpdated = uint64(block.number);
} else {
    // Look up how much this vouch has locked. If it is 0 then
    // continue to the next voucher. Then calculate the amount to
    // unlock which is the min of the vouches lock and what is
    // remaining to unlock
    uint96 locked = vouch.locked;
    if (locked == 0) continue;
    innerAmount = _min(locked, remaining);
    // Update the stored locked values and last updated block
    stakers[vouch.staker].locked -= innerAmount;
    vouch.locked -= innerAmount;
    vouch.lastUpdated = uint64(block.number);
}
```

Above code is invoked where user called borrow() function with amount>minBorrow:
[code](https://github.com/sherlock-audit/2023-02-union/blob/main/union-v2-contracts/contracts/market/UToken.sol#L525-L568)

```solidity
function borrow(address to, uint256 amount) external override onlyMember(msg.sender) whenNotPaused nonReentrant {
    IAssetManager assetManagerContract = IAssetManager(assetManager);
    if (amount < minBorrow) revert AmountLessMinBorrow();
    if (amount > getRemainingDebtCeiling()) revert AmountExceedGlobalMax();

    ...

    // Call update locked on the userManager to lock this borrowers stakers. This function
    // will revert if the account does not have enough vouchers to cover the borrow amount. ie
    // the borrower is trying to borrow more than is able to be underwritten
    IUserManager(userManager).updateLocked(msg.sender, (actualAmount + fee).toUint96(), true);
```

```solidity
uint96 lockAmount = _min(availableStake, vouch.trust - vouch.locked);
```

When vouch.trust == vouch.locked the value of lockAmount goes to zero. And the loop continued.Thus the value of lastUpdated doesn't updated.

Suppose Bob the staker has a vouch with trust maxxed, i.e. vouch.trust = vouch.locked = 10k DAI. He can setup a second borrower being his own account, some minimal trust, then can run min borrow many, many times, gaining huge stakers[vouch.staker].lockedCoinAge as vouch.lastUpdated aren't updated and lockedCoinAge grows with a positive some_number_of_blocks \* 10k DAI number each time Bob borrows 1 DAI via his second borrower.

### Recommendation

```solidity
            uint256 lastWithdrawRewards = getLastWithdrawRewards[vouch.staker];
            stakers[vouch.staker].lockedCoinAge +=
                (block.number - _max(lastWithdrawRewards, uint256(vouch.lastUpdated))) *
                uint256(vouch.locked);
+           vouch.lastUpdated = uint64(block.number);
 if (lock) {
            ...
-           vouch.lastUpdated = uint64(block.number);
            } else {
                ...
-           vouch.lastUpdated = uint64(block.number);
            }

```
