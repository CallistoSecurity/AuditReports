# 1. Summary

[TechX](https://etherscan.io/address/0x3500381c4707aee4c517a91e856e231cb48bf301#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://etherscan.io/address/0x3500381c4707aee4c517a91e856e231cb48bf301#code

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known Issues of ERC20 Standard

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Extra checking

### Severity: minor observation

### Description

Extra checking in 63, 109, 110 lines. SafeMath library checks it anyway.
Solidity automatically throws when dividing by zero in 14 line. There is no case in which this doesn't hold.

### Code snippet

https://gist.github.com/yuriy77k/0b860f1970ad357f254b0c5945c6cf2c#file-techx-sol-L63

https://gist.github.com/yuriy77k/0b860f1970ad357f254b0c5945c6cf2c#file-techx-sol-L109

https://gist.github.com/yuriy77k/0b860f1970ad357f254b0c5945c6cf2c#file-techx-sol-L110

### Recommendation

Those lines may be deleted.

# 4. Conclusion

The token contract is safe to be deployed, developers should consider ERC20 known issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/14a79d7bcd64bfb2c1f4c1da7719f7ee

https://gist.github.com/yuriy77k/2445a84febd8956cd8f6aeda304501b1

https://gist.github.com/yuriy77k/edc9116ebcbe32f7dd13759b7069b347
