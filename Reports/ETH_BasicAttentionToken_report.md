# Security Audit Report

# 1. Summary

[Basic Attention Token (BAT)](https://etherscan.io/address/0x0d8775f648430679a709e98d2b0cb6250d2887ef#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BAToken.sol](https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol)

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

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

## 3.2. ERC20 Compliance — transfer should `throw`

### Severity: medium

### Description


From ERC-20 specification:
> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

But in this implementation of `transfer` and `transferFrom` it just returns `false`. This can lead to serious consequences. Because checking the return value of this function is rare.
For example, external contract may use this token contract as:
```solidity
BatToken.transferFrom(recipient, this, value);
points[recipient] += value;
```
In this case recipient can get any value of points, but he may not have enough money and the code will succeed.

### Code snippet

https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol-L54

https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol-L66

## 3.3. ERC20 Compliance — zero-value transfers rejecting

### Severity: low

### Description

EIP20 says that:
> Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.
But in this contract, function `transfer` and `transferFrom` has a condition:
```solidity
if (balances[msg.sender] >= _value && _value > 0) {
    // ...
}
```

### Code snippet

https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol-L48

https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol-L59

## 3.4. ERC20 Compliance — event missing

### Severity: low

### Description

1. According to ERC20 standard when coins are minted a `Transfer` event should be emitted.

2. The `createTokens` function also should emit the `Transfer` event.

### Code snippet

https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol-L127

https://gist.github.com/yuriy77k/9f8fcc78745cf81e3aff92da724b3d5c#file-batoken-sol-L145


# 4. Conclusion

The audited smart contract has some issues with ERC20 Compliance that could cause losing the money in a particular situation. We recommend fixing these issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/3ed8070ea3428c085ff2b41d015f92ba

https://gist.github.com/yuriy77k/24e1f3af18913586b8cbddc5338eb68f

https://gist.github.com/yuriy77k/f3ea10397d674838538698e1249048ea

