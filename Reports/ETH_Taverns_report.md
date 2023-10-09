# Taverns Security Audit Report

# 1. Summary

[Taverns](https://game.lordless.io/taverns) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Tavern is a rare and specific digital asset in LORDLESS and it is an ERC-721 token. Most of the interactions in the virtual world are dependent on Tavern.

# 2. In scope

Commit hash 69a820341099f88d1937222775bcac9d8499973c.

- [IPower.sol](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/IPower.sol).
- [ITavern.sol](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/ITavern.sol).
- [Power.sol](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/Power.sol).
- [Tavern.sol](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/Tavern.sol).
- [TavernBase.sol](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/TavernBase.sol).

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. The length of the input arrays should be compared

### Severity: low

### Description

Input arrays of functions may have different length by accidentally. This can lead to incorrect sending of funds to many recipients.

### Code snippet

* [_batchBuild()](https://github.com/lordlessio/game-contracts/blob/69a820341099f88d1937222775bcac9d8499973c/contracts/tavern/TavernBase.sol#L58)
* [_batchActivenessUpgrade()](https://github.com/lordlessio/game-contracts/blob/69a820341099f88d1937222775bcac9d8499973c/contracts/tavern/TavernBase.sol#L87)
* [_batchPopularitySetting()](https://github.com/lordlessio/game-contracts/blob/69a820341099f88d1937222775bcac9d8499973c/contracts/tavern/TavernBase.sol#L102)

#### Recommendation

Use something like `require(_tokenIds.length == _popularitys.length)`.

## 3.2. No checking for zero address

### Severity: low

### Description

In the functions `setTavernContract` and `setPowerContract` there are no checking for zero address.

### Code snippet

* [`setTavernContract`](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/Power.sol#L36)
* [`setPowerContract`](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/Tavern.sol#L36)

## 3.3. Owner Privileges

### Severity: owner privileges

### Description
Contract owner allow himself to:

 - Owner can upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

### Code snippet

* [`setTavernContract`](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/Power.sol#L36)
* [`setPowerContract`](https://github.com/lordlessio/game-contracts/blob/master/contracts/tavern/Tavern.sol#L36)


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/6fa70469824d576623a2b54d9ea8f45f

https://gist.github.com/yuriy77k/81aa4579340e7f7c4a4f873a8fac60cb

https://gist.github.com/yuriy77k/1a50c367f311284c34ebd9e8145f8c4e
