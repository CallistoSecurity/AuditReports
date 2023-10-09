# Security Audit Report

# 1. Summary

[IOSToken](https://etherscan.io/address/0xfa1a856cfa3409cfa145fa4e20eb270df3eb21ab#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : IOST
	Name        : IOSToken
	Total supply: 21,000,000,000
	Decimals    : 18 
	Standard    : ERC20

# 2. In scope

- [IOSToken.sol](https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756)

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

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

## 3.2 Non-compliance with ERC-20 Token Standard.

### Severity: medium

### Description

ERC-20 Token [Standard specifies](https://eips.ethereum.org/EIPS/eip-20) for functions `transfer` and `transferFrom`:

The function SHOULD throw if the `_from` account balance does not have enough tokens to spend.
But in this implementation it just returns false. This can lead to serious consequences. Because checking the return value of this function is rare.

### Code snippet

https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756#file-iostoken-sol-L60

https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756#file-iostoken-sol-L70

https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756#file-iostoken-sol-L108

## 3.3. Transfer to `address(0)`

### Severity: low

### Description

Transfer to `address(0)` is allowed when using  `transfer` or `transferFrom` functions. 

### Code snippet

https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756#file-iostoken-sol-L60

https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756#file-iostoken-sol-L70

### Recommendation

Add zero address checking

```solidity
require( _to != address(0) );

```

## 3.4. Unlimited Allowance

### Severity: low

### Description

Even if the likelihood of such issue to represent a risk for users is very low, the reimplemented `transferFrom` with unlimited allowance to an address is not complaint with ERC-20 standard. Users should be aware of it.

### Code snippet

https://gist.github.com/yuriy77k/164e096306a2b117b68b4cf9cca9f756#file-iostoken-sol-L108

# 4. Conclusion

The audited smart contract has some issues with ERC20 Compliance that could cause losing the money in a particular situation. We recommend fixing these issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ce4b26e2d0076584717ba0d083095d4e

https://gist.github.com/yuriy77k/9ac93e4cb81d884b85edb355ab7948e1

https://gist.github.com/yuriy77k/78729a1cad92e731b3ab77e884e9deee
