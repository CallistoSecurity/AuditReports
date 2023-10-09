# Security Audit Report

# 1. Summary

[dClinic Token](https://github.com/dclinicpteltd) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [VIC - dClinic](https://github.com/dclinicpteltd/smartContract/blob/master/VIC%20-%20dClinic) github commit 8111a327156f62a52614f726f676d7f0a275fda9.

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

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

## 3.2. Decrease Allowance

### Severity: low

### Description

`decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it, the function might throw and give more chances for the spender to take the rest of the allowed value.

### Code snippet

https://github.com/dclinicpteltd/smartContract/blob/master/VIC%20-%20dClinic#L256

### Recommendation

Check if the value allowed is higher or equal to the value to be subtracted if not then set the allowed value to zero.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/e9e41e54670b8b781ce77a6011926092

https://gist.github.com/yuriy77k/08a9b09bfe034bffd493bb571800a69d

https://gist.github.com/yuriy77k/cdc5d5164141247d22bba84fef0c3233
