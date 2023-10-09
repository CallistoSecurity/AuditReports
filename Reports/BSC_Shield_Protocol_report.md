# Shield Protocol Security Audit Report

# 1. Summary

[Shield Protocol](https://github.com/shield2protocol/smart-contract/blob/main/BinanceSmartChain_SHIELD.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope
Commit hash f5c1a5d77c158f84889b65a4070f7b80c3cf6898
[BinanceSmartChain_SHIELD.sol](https://github.com/shield2protocol/smart-contract/blob/f5c1a5d77c158f84889b65a4070f7b80c3cf6898/BinanceSmartChain_SHIELD.sol)

# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 2 notes.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Unnecessary getter functions

### Severity: note

### Description

The variables [_decimals, _symbol, _name](https://github.com/shield2protocol/smart-contract/blob/f5c1a5d77c158f84889b65a4070f7b80c3cf6898/BinanceSmartChain_SHIELD.sol#L311-L313) declared as `public` so compiler create getter function for them. But for those variables already there are getter function [decimals(), symbol(), name()](https://github.com/shield2protocol/smart-contract/blob/f5c1a5d77c158f84889b65a4070f7b80c3cf6898/BinanceSmartChain_SHIELD.sol#L331-L345).
This issue caused higher gas consumption during deployment, but does not impact on contract works.

### Recommendation

Variable `_decimals`, `_symbol`, `_name` have to be declared as `private`.

## 3.3. Wrong returns type

### Severity: note

### Description

The function [decimals()](https://github.com/shield2protocol/smart-contract/blob/f5c1a5d77c158f84889b65a4070f7b80c3cf6898/BinanceSmartChain_SHIELD.sol#L331) returns type `uint256` but variable [_decimals](https://github.com/shield2protocol/smart-contract/blob/f5c1a5d77c158f84889b65a4070f7b80c3cf6898/BinanceSmartChain_SHIELD.sol#L311) has type `uint8`.

### Recommendation

The function `decimals()` has to returns `uint8`.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issus was found during the audit. 
The pointed notes don't impact on contract works but show the how it has to be by **good practice**.

# 5. Revealing audit reports

https://gist.github.com/danbogd/c3d990fc32ccd1623e2a9898fd636904

https://gist.github.com/MrCrambo/830356e6648f5b7f5f192f4820d625a9

