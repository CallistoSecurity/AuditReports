# 1. Summary

[Cryptoprofile](https://etherscan.io/address/0x06a6fc23e6ec8a2b2aeeefd70d772dc3d6b45010#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Cryptoprofile.sol](https://gist.github.com/yuriy77k/82bb1d7446d3255db8d22b4ca4715d48).

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```
## 3.2. Deprecated method.

### Severity: minor observation

### Description

The function () payable { revert(); } was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

# 4. Conclusion

The review did not show any critical issues, some of low severity issues were found.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0f09d11ef8576de8e80721a0b21ddd59

https://gist.github.com/yuriy77k/59ebae66d7b38c21e86196685cf31ec5

https://gist.github.com/yuriy77k/2c7a380594015738343777407f3b7722
