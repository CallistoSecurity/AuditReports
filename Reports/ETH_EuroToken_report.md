# Security Audit Report

# 1. Summary

[Euro Token](https://github.com/SocialRemit/SOL/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Deployed at https://etherscan.io/address/0x7a0e91c4204355e0a6bbf746dc0b7e32dfefdecf#code

# 2. In scope

- [SREUR.sol](https://github.com/SocialRemit/SOL/blob/master/SREUR.sol) github commit hash 662ec938265d2756943f306266e378f4de46ae88.

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

`decreaseAllowance` throw in case if the value to be substracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

https://github.com/RideSolo/SOL/blob/master/SREUR.sol#L236#L249


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b1d4fbfd1c28ebb5feb372b8fc1836d4

https://gist.github.com/yuriy77k/2db527a987ddeee93e348ff886594be1

https://gist.github.com/yuriy77k/21aa160a08acef777e12850bc57a9e3b
