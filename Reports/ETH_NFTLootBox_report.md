# NFTLootBox Security Audit Report

# 1. Summary

[NFTLootBox.com](https://www.nftlootbox.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Logic description by developer:
>
> Users purchase lootboxes using their client seed. the contract doesn't use this seed, but the server (which reads the event emitted) does. Since generating random numbers is very difficult on-chain we calculate winnings server-side. once we've determined the user's winnings, they can redeem those winnings through the redeemBulk function.

# 2. In scope

Commit hash 56ad73a604226e481e78b2dc634001721203ad66

[NFTLootbox.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol)
[lib/Context.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/Context.sol)
[lib/SafeMath.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/SafeMath.sol)
[lib/Ownable.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/Ownable.sol)
[lib/IERC20.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/IERC20.sol)
[lib/IERC1155.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/IERC1155.sol)
[lib/ReentrancyGuard.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/ReentrancyGuard.sol)
[lib/Address.sol](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib/Address.sol)

# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 2 notes.

No critical security issues were found.

## 3.1. Wrong import path

### Severity: compiler error (note)

### Description

The [imported files](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L5-L10) located in the [/lib/](https://github.com/NFTLootBox/contracts/tree/56ad73a604226e481e78b2dc634001721203ad66/contracts/lib) folder, but in the code `/lib/` is missing that cause compiler error.

### Recommendation

Use import like following:
```Solidity
import "./lib/Context.sol";
import "./lib/SafeMath.sol";
import "./lib/Ownable.sol";
import "./lib/IERC20.sol";
import "./lib/IERC1155.sol";
import "./lib/ReentrancyGuard.sol";
```

## 3.2. Zero address checking

### Severity: low

### Description

There is no zero address checking in functions: [setTransferAddress](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L96), [setAuthAddress](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L100), [updateLootbox](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L104)

### Recommendation

Add zero address checking:

```solidity
require( _address != address(0) );

```

## 3.3. Gas wasting

### Severity: note

### Description

The modifier `nonReentrant` is overused that will cause unnecessary gas usage.

### Recommendation

The modifier `nonReentrant` may be removed from functions [setTransferAddress](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L96), [setAuthAddress](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L100), [updateLootbox](https://github.com/NFTLootBox/contracts/blob/56ad73a604226e481e78b2dc634001721203ad66/contracts/NFTLootbox.sol#L104) where no risk of re-entrance.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/MrCrambo/f11c21632ca2b02d92d2e8ede91b6d19
