# Security Audit Report

# 1. Summary

[ZRX Token](https://etherscan.io/address/0xe41d2489571d322189246dafa5ebde1f4699f498#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : ZRX
	Name        : 0x Protocol Token
	Total supply: 1,000,000,000
	Decimals    : 18 
	Standard    : ERC20

# 2. In scope

- [ZRXToken.sol](https://gist.github.com/yuriy77k/9e3f16ce8289f6fafa64c9fee13dfd1f)

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

## 3.2. ERC-20 Compliance

### Severity: medium

### Description

- Following [EIP-20](https://eips.ethereum.org/EIPS/eip-20) specifications `transfer` should throw when the `msg.sender` doesn't have enough fund. 
- Same as previously following the specifications `transferFrom` should throw and not return false if the `_from` address doesn't have enough of fund or if the allowed value isn't enough to cover the transaction `_value`.

### Code snippet

https://gist.github.com/yuriy77k/9e3f16ce8289f6fafa64c9fee13dfd1f#file-zrxtoken-sol-L60

https://gist.github.com/yuriy77k/9e3f16ce8289f6fafa64c9fee13dfd1f#file-zrxtoken-sol-L70

## 3.3. Unlimited Allowance

### Severity: low

### Description

Even if the likelihood of such issue to represent a risk for users is very low, the reimplemented `transferFrom` with unlimited allowance to an address is not complaint with ERC-20 standard. Users should be aware of it.

### Code snippet

https://gist.github.com/yuriy77k/9e3f16ce8289f6fafa64c9fee13dfd1f#file-zrxtoken-sol-L108

## 3.4. No zero address checking

### Severity: low

### Description

In functions `transfer` and `transferFrom` there are no zero address checking.

### Code snippet

https://gist.github.com/yuriy77k/9e3f16ce8289f6fafa64c9fee13dfd1f#file-zrxtoken-sol-L60

https://gist.github.com/yuriy77k/9e3f16ce8289f6fafa64c9fee13dfd1f#file-zrxtoken-sol-L70

# 4. Conclusion

The audited code has some ERC20 compliance issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/3a07536517aef3a02b64024db39f5407

https://gist.github.com/yuriy77k/471b6aeffdc8bc2c5c6a406dbe656ec7

https://gist.github.com/yuriy77k/dfa68fb6651cd142bc1ae85c681dc58c