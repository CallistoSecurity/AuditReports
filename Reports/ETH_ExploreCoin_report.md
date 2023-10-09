# ExploreCoin Security Audit Report

# 1. Summary

[ExploreCoin](https://etherscan.io/address/0x2802faad8a0f9595c1660c436c7e51d9ce97b1e9#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ExploreCoin](https://etherscan.io/address/0x2802faad8a0f9595c1660c436c7e51d9ce97b1e9#code).

# 3. Findings

In total, **4 issues** were reported including:

- 3 low severity issues.

- 1 notes.

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

[line 211](https://gist.github.com/RideSolo/72e9a54ef16c6f3c615e98dc26ab2ef5#file-explorecoin-sol-L211)

## 3.3. Zero address checking required

#### Severity: low

#### Description

In `constructor` at line 306 there have to be zero address checking for `feeReceiver` and `tokenOwnerAddress`.

### Code snippet

[line 306](https://gist.github.com/RideSolo/72e9a54ef16c6f3c615e98dc26ab2ef5#file-explorecoin-sol-L306)

## 3.4. EPL Token is not Burnable

### Severity: note

### Description

Functions `_burn` and `_burnFrom` are internal and unused.

### Code snippet

* [line 251](https://etherscan.io/address/0x2802faad8a0f9595c1660c436c7e51d9ce97b1e9#code)

* [line 281](https://etherscan.io/address/0x2802faad8a0f9595c1660c436c7e51d9ce97b1e9#code)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/18e58f1d87b9cf05db92b89b7512b87f

https://gist.github.com/yuriy77k/0e650f3827e33abcd9f45a68e71f03e4

https://gist.github.com/yuriy77k/13fb50504be43071bd55615ee38be669
