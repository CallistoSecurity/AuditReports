# Spirit Orb Pets Security Audit Report

# 1. Summary

[Spirit Orb Pets](https://www.spiritorbpets.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> The smart contract is 4 different contracts that interact with each other to make a digital pet game. It is also extendable which opens the possibility for growth in the future. The first two are NFTs, the second of which has stats. The 3rd is an ERC20 token that generates tokens over time and mints to authorized contracts (which can be frozen). The 4th is the first of a few planned contracts that make the NFTs interactable and able to earn the ERC20 token.

- Website: https://www.spiritorbpets.com/
- Twitter: https://www.twitter.com/SpiritOrbPets
- Discord: https://discord.gg/SpiritOrbPets

# 2. In scope

- v0 Pets: https://ropsten.etherscan.io/address/0xde1Dee12267fc762493d35A7E10941bB60591bc7#code
- v1 Pets: https://ropsten.etherscan.io/address/0x7cab257033FA4720220DBd5Ee4f2Dd3AB8af4F5E#code
- Pet Care: https://ropsten.etherscan.io/address/0xcbb4E96e845BB6274E5047381065539CCf80cfa3#code
- Care Token: https://ropsten.etherscan.io/address/0x4c00176d5021b465Fdc1D11d83B67B8F3636Fb6A#code

## 2.1. Excludes

The rules of the game are excluded from the audit due to the lack of their description.

# 3. Findings

In total, **3 issues** were reported, including:

- 1 high severity issues.

- 0 medium severity issues.

- 2 low severity issues.

In total, **2 notes** were reported, including:

- 2 notes.


## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Function isApproved() gas usage 

### Severity: note

### Description

In the function [isApproved(address addr)](https://ropsten.etherscan.io/address/0x7cab257033FA4720220DBd5Ee4f2Dd3AB8af4F5E#code#F1#L100) used loop to find address in the list of approved contracts. The more contracts will be in the list the more gas will use this function. 

### Recommendation

Using `mapping(address => bool) public isApproved;` will be more gas effective.

## 3.3. Approved minters notes

### Severity: note

### Description

1. In the function [isApproved(address addr)](https://ropsten.etherscan.io/address/0x4c00176d5021b465Fdc1D11d83B67B8F3636Fb6A#code#F1#L94) used loop to find address in the list of approved minters. The more addresses will be in the list the more gas will use this function. Using `mapping(address => bool) public isApproved;` will be more gas effective.

2. In the function [freezeApprovedMinters()](https://ropsten.etherscan.io/address/0x4c00176d5021b465Fdc1D11d83B67B8F3636Fb6A#code#F1#L87) the variable `_freezeApprovedMintersList` assigned to `true` to disallow add or remove minter addresses but this is not checked in the functions [addApprovedContract()](https://ropsten.etherscan.io/address/0x4c00176d5021b465Fdc1D11d83B67B8F3636Fb6A#code#F1#L74) and [removeApprovedContract()](https://ropsten.etherscan.io/address/0x4c00176d5021b465Fdc1D11d83B67B8F3636Fb6A#code#F1#L78)


## 3.4. Anybody can burn Care Tokens from any account

### Severity: high

### Description

The function [burn(address account, uint256 amount)](https://ropsten.etherscan.io/address/0x4c00176d5021b465Fdc1D11d83B67B8F3636Fb6A#code#F1#L57) allow anybody to burn any amount of tokens from any account. 

### Recommendation

According contract logic this function should work like `burnFrom` therefore `allowance` must be checked before burn tokens.
```Solidity
  function burn(address account, uint256 amount) external {
    uint256 currentAllowance = _allowances[account][_msgSender()];
    require(currentAllowance >= amount, "ERC20: burn amount exceeds allowance");
    unchecked {
        _approve(account, _msgSender(), currentAllowance - amount);
    }
    super._burn(account, amount);
  }
```

## 3.5. Care tokens allow to feed pet only fot integer numbers of days

### Severity: low

### Description

In the function [feedPet()](https://ropsten.etherscan.io/address/0xcbb4E96e845BB6274E5047381065539CCf80cfa3#code#F1#L222) amount of `careTokensToPay` divide by 5 before multiply by `1 days`. Since Solidity does not support float point numbers the result will be only integer number of days (1,2,3). I.e. If user send 9 care tokens to feed only 5 tokens will be used and 4 will not be counted. 

### Recommendation

Use multiplication before division: `uint64 newFeedTime = cdFeed + uint64(careTokensToPay * 1 days / 5);`.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues (at least high severity) must be fixed prior to the usage of this contract.

Contract contain cheat function fro testing purpose. Do not forget to remove it in release version.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
