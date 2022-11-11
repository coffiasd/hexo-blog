---
title: What is ERC20
tags:
  - solidity
categories:
  - solidity
date: 2022-11-11 19:22:57
---

# what is ERC20 ?

<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol>

## storage variables in ERC20

```
mapping(address => uint256) private _balances;
mapping(address => mapping(address => uint256)) private _allowances;
uint256 private _totalSupply;

string private _name;
string private _symbol;
```

- \_balances:
  store the balance of each address
- \_allowances:
  used inside function \_approve(),approve fixd amount token to spender address.
  ```
  _allowances[owner][spender] = amount
  ```
- \_totalSupply(e.g.: 10000000000000000000000000000)
- \_name(e.g.: Matic Token)
- symbol(e.g.: MATIC)

## transfer

```
function transfer(address to, uint256 amount) public virtual override returns (bool) {
        address owner = _msgSender();
        _transfer(owner, to, amount);
        return true;
}
```

## \_transfer

内部函数需要被重写

```
function _transfer(
        address from,
        address to,
        uint256 amount
    ) internal virtual {
        //校验from和to的地址都非零值
        require(from != address(0), "ERC20: transfer from the zero address");
        require(to != address(0), "ERC20: transfer to the zero address");

        //virtual函数,需要被重写
        _beforeTokenTransfer(from, to, amount);

        //校验from的余额必须大于转账的amount
        uint256 fromBalance = _balances[from];
        require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");

        //unchecked 不需要考虑溢出
        unchecked {
            _balances[from] = fromBalance - amount;
            // Overflow not possible: the sum of all balances is capped by totalSupply, and the sum is preserved by
            // decrementing then incrementing.
            _balances[to] += amount;
        }

        emit Transfer(from, to, amount);

        //virtual函数,需要被重写
        _afterTokenTransfer(from, to, amount);
    }
```
