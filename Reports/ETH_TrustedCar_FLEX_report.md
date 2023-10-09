# Security Audit Report

# 1. Summary

[TrustedCar FLEX Token](https://etherscan.io/address/0x9b4f37adaeee05fbb26d66d530207bee4ac130b3#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [FlexToken.sol](https://gist.github.com/yuriy77k/417fd338c45a30591eaa3b034281ab7e).

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

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

## 3.2. Discrepancy with the ERC20 standard

### Severity: minor observation

### Description

In FlexToken.sol, according to the ERC20 standard, the variable decimals should be declared as uint8.

### Code snippet

https://gist.github.com/yuriy77k/417fd338c45a30591eaa3b034281ab7e#file-flextoken-sol-L271

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issue was found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/9a45891d4016d5ff97c41f8258351252

https://gist.github.com/yuriy77k/567a8c6defd4e309435bdefa50fc747a

https://gist.github.com/yuriy77k/9040c61639415b2363d8c440bcc1f93f
