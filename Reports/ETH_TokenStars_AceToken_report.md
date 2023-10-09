# Security Audit Report

# 1. Summary

[TokenStars Ace Token](https://github.com/token-stars/ace-token/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash e6fa09094151024234cae5a1e09ef84609ba1fd5.

- [AceToken.sol](https://github.com/token-stars/ace-token/blob/master/contracts/AceToken.sol).
- [Migrations.sol](https://github.com/token-stars/ace-token/blob/master/contracts/Migrations.sol).
- [StarTokenInterface.sol](https://github.com/token-stars/ace-token/blob/master/contracts/StarTokenInterface.sol).

# 3. Findings

In total, **6 issues** were reported including:

- 4 low severity issues.

- 2 minor observation.

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

## 3.2. Precision issue

### Severity: low

### Description

Solidity operates only with integers. Thus, variables multiplication should be done before the division in order to reduce the rounding errors.

### Code snippet

https://github.com/token-stars/ace-token/blob/e6fa09094151024234cae5a1e09ef84609ba1fd5/contracts/AceToken.sol#L113

### Recommendation

We recommend changing the order of division and multiplication.

## 3.3. Owner's Privileges

### Severity: low

### Description

The contract owner allow himself to pause functions of contract (`transfer`, `transferFrom`).

### Code snippet

https://github.com/token-stars/ace-token/blob/e6fa09094151024234cae5a1e09ef84609ba1fd5/contracts/AceToken.sol#L87-L101

## 3.4. Zero address checking

### Severity: low

### Description

In function `mint` there is no zero address checking and tokens could be minted to zero address.

### Code snippet

https://github.com/token-stars/ace-token/blob/e6fa09094151024234cae5a1e09ef84609ba1fd5/contracts/AceToken.sol#L109

### Recommendation

Add zero address checking
```solidity
require(to != address(0));
```

## 3.5. Discrepancy with the ERC20 standard

### Severity: minor observation

### Description

In AceToken.sol, according to the ERC20 standard, the variable decimals should be declared as uint8.

### Code snippet

https://github.com/token-stars/ace-token/blob/e6fa09094151024234cae5a1e09ef84609ba1fd5/contracts/AceToken.sol#L35

## 3.6. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.11. It is suggested to use the latest version and fix all compiler warnings that arise. 
Compiler version should be fixed to avoid any potential discrepancies in smart contract behavior caused by different versions of compiler.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1a675d83ff574bd7c28465eb4f2620ce

https://gist.github.com/yuriy77k/fd6706e6c288eebf90d27ca5cb3786c3

https://gist.github.com/yuriy77k/b06bb734c8135086b0b1254b7b6a35d3
