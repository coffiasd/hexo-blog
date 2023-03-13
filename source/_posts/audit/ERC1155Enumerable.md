---
title: ERC1155Enumerable Implement
date: 2023-03-13 09:46:42
tags:
  - solidity
  - web3 security
  - code4rena
categories:
  - audit
---

When minting and burning tokens,the ERC1155Enumerable implementation does not correctly update the following states:

- uint256[] private \_allTokens;
- mapping(uint256 => uint256) private \_allTokensIndex;
- mapping(address => uint256) internal \_currentIndex;

In particular:

- the \_allTokens array length (and therefore the totalSupply()) always increases (never decreases)
- the \_allTokensIndex[id] always increases
- the \_curentIndex[from] always increases

```javascript

contract HelperERC1155 is ERC1155Enumerable, ERC1155Holder {
    constructor() ERC1155("Test") {
    }
    function mint(uint256 id, uint256 amount) external {
        _mint(msg.sender, id, amount, bytes(""));
    }
    function burn(uint256 id, uint256 amount) external {
        _burn(msg.sender, id, amount);
    }
    function currentIndex(address owner) external view returns (uint256) {
        return _currentIndex[owner];
    }
    function allTokensIndex(uint256 id) external view returns (uint256) {
        return _allTokensIndex[id];
    }
    function allTokens(uint256 idx) external view returns (uint256) {
        return _allTokens[idx];
    }
    function idTotalSupply(uint256 id) external view returns (uint256) {
        return _idTotalSupply[id];
    }
}

```
