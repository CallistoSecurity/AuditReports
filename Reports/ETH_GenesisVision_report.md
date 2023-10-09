# Genesis Vision Security Audit Report

# 1. Summary

[Genesis Vision](https://github.com/GenesisVision/platform-contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 4eca90915580f209e86db8270c4f53d6e645712b.
- [Models.sol](https://github.com/GenesisVision/platform-contracts/blob/master/contracts/libs/Models.sol).
- [GenesisVisionPlatform.sol](https://github.com/GenesisVision/platform-contracts/blob/master/contracts/GenesisVisionPlatform.sol).
- [ManagerToken.sol](https://github.com/GenesisVision/platform-contracts/blob/master/contracts/ManagerToken.sol).
- [Migrations.sol](https://github.com/GenesisVision/platform-contracts/blob/master/contracts/Migrations.sol).
- [TradingHistoryStorage.sol](https://github.com/GenesisVision/platform-contracts/blob/master/contracts/TradingHistoryStorage.sol).

# 3. Findings

In total, **6 issues** were reported including:

- 3 low severity issues.

- 2 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Raise total supply

### Severity: owner privileges

### Description

Contract owner allow himself to raise TotalSupply and he will get added amount.

### Code snippet

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/ManagerToken.sol#L30

## 3.3. Required check for an 0x0 address

### Severity: low

### Description

It is possible to remain out of contract control by accidentally calling function without parameter.

### Code snippet

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/GenesisVisionPlatform.sol#L39

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/TradingHistoryStorage.sol#L30

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/GenesisVisionPlatform.sol#L85

## 3.4. Discrepancy with the ERC20 standard

### Severity: note

### Description

In ManagerToken.sol, according to the ERC20 standard, the variable decimals should be declared as uint8.

### Code snippet

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/ManagerToken.sol#L10

## 3.5. Missing event call.

### Severity: low

### Description
According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

### Code snippet

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/ManagerToken.sol#L26

### 3.6. Overflow

### Severity: notes

### Description

In the ManagerToken.sol, the developers didn't use SafeMath to prevent overflow by the owner.

### Code snippet

https://github.com/GenesisVision/platform-contracts/blob/4eca90915580f209e86db8270c4f53d6e645712b/contracts/ManagerToken.sol#L29


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/113a182dbe2742936d548b8217d59482

https://gist.github.com/yuriy77k/f51540e6dd89019c991c85fbe829163d

https://gist.github.com/yuriy77k/3be27844e8b3a408b16454926db88c08
