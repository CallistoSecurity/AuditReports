# Airswap Security Audit Report

# 1. Summary

[Airswap](https://github.com/airswap/contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 04b71ad419ef645e8bd9b50e9998819b7a450616.

- [AirSwapToken.sol](https://github.com/airswap/contracts/blob/master/contracts/AirSwapToken.sol).
- [Exchange.sol](https://github.com/airswap/contracts/blob/master/contracts/Exchange.sol).
- [Migrations.sol](https://github.com/airswap/contracts/blob/master/contracts/Migrations.sol).

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

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

## 3.2. Owner Privileges

### Severity: owner privileges

### Description

Contract owner allow himself to pause/unpause transfer/transferFrom.

### Code snippet

https://github.com/airswap/contracts/blob/04b71ad419ef645e8bd9b50e9998819b7a450616/contracts/lib/Pausable.sol#L21


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/905181d824e4345254d585a5ee0a7678

https://gist.github.com/yuriy77k/ae19a33b19da449f409e088097d467b5

https://gist.github.com/yuriy77k/aff5fda81293578bec43ad72576450a1
