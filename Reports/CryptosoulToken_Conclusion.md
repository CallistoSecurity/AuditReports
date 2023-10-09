# 1. Summary

[CryptosoulToken](https://etherscan.io/address/0xbb1f24c0c1554b9990222f036b0aad6ee4caec29#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [CryptosoulToken.sol](https://gist.github.com/yuriy77k/3f3d4a78958004e452a7c16e30322270)

# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Token Freezing Logic

### Severity: minor observation

### Description

`giveFreezePermission` function allow contract owner to give freeze permission to addresses, however, when `freezeTokens` function is called the only funds that can be freezed belongs to the msg.sender address, the contract developers should define why an address need a permission to freeze its own tokens.

### Code snippet

https://gist.github.com/yuriy77k/3f3d4a78958004e452a7c16e30322270#file-cryptosoultoken-sol-L135

https://gist.github.com/yuriy77k/3f3d4a78958004e452a7c16e30322270#file-cryptosoultoken-sol-L147

## 3.2. Unfrozen Token Computation

### Severity: minor observation

### Description

The functions `checkIfCanUseTokens` and `getUnfreezedTokens` compute the unfrozen token amount without using safemath library. Following the contract logic an underflow cannot happen but safemath usage is required to avoid any possible issue.

### Code snippet

https://gist.github.com/yuriy77k/3f3d4a78958004e452a7c16e30322270#file-cryptosoultoken-sol-L72

https://gist.github.com/yuriy77k/3f3d4a78958004e452a7c16e30322270#file-cryptosoultoken-sol-L175

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)


# 4. Conclusion

The audited contract is safe.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/3852e19374961417fa7f87f74de7c94f

https://gist.github.com/yuriy77k/8bc007c8b5aa4c7ed09272573be89549

https://gist.github.com/yuriy77k/532b3c21924eec38cee81ce6608facef
