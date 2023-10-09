# FreeToolBox Security Audit Report

# 1. Summary

[FreeToolBox](https://etherscan.io/address/0x1E71034C89dD191ACcB27dC35f18a3d8b6f91311#code) smart contract security audit report performed by [Yuriy](https://github.com/yuriy77k/)

# 2. In scope

https://etherscan.io/address/0x1E71034C89dD191ACcB27dC35f18a3d8b6f91311#code

# 3. Findings

In total, **5 issues** were reported including:

- 2 low severity issues.

- 1 notes.

- 2 owner's privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description


1. The approve function of ERC-20 is vulnerable. Using front-running attack one can spend approved tokens before change of allowance value. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Decrease Allowance

### Severity: low

### Description

The function `decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

Row 408-410


## 3.3. Prefer external to public visibility level

### Severity: note

### Description

A function with public visibility modifier that is not called internally. Changing visibility level to external increases code readability. Moreover, in many cases functions with external visibility modifier spend less gas comparing to functions with public visibility modifier.

## 3.4. Owner's privileges.

### Severity: note

### Description

1. Owner can Pause/Unpause Token's functions.

2. Owner can Lock specified address from tokens transferring.

# 4. Conclusion

Only low severity issues were found during the audit.
