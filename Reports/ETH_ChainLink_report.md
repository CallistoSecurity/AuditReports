# Security Audit Report

# 1. Summary

[ChainLink Token](https://etherscan.io/address/0x514910771af9ca656af840dff83e8264ecf986ca#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token description:

	Symbol      : LINK
	Name        : ChainLink Token
	Total supply: 1,000,000,000
	Decimals    : 18 
	Standard    : ERC677

# 2. In scope

- [LinkToken.sol](https://gist.github.com/yuriy77k/c3a70d212a7f9ecda715252e45073158)

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

## 3.2. No zero address checking

### Severity: low

### Description

In functions `transfer`(Line 81) and `transferFrom`(Line 118) there are no zero address checking.

### Code snippet

https://gist.github.com/yuriy77k/c3a70d212a7f9ecda715252e45073158#file-linktoken-sol-L81

https://gist.github.com/yuriy77k/c3a70d212a7f9ecda715252e45073158#file-linktoken-sol-L118

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/18f0b9562bfba4210550f251d91fe2ca

https://gist.github.com/yuriy77k/6ea910cccecf1c44f93b0551a474db51

https://gist.github.com/yuriy77k/6a6833c14f6a688eb142a00fd50630d9