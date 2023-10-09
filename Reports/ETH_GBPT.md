# 1. Summary

[GBPT](https://etherscan.io/address/0xb3742ca2e6e8e9f707886fd37bb3ba8159dd85d3#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ GreatBritishPoundToken.sol](https://gist.github.com/yuriy77k/f6ccd69e766b929c2e282f0f53737955)

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Token Transfer to 0x0 address

### Severity: low

### Description

The implemented token uses transfer to address 0x0 as basic burn mechanism, however accidental tokens loss issue can be applied.

### Code snippet

https://gist.github.com/yuriy77k/f6ccd69e766b929c2e282f0f53737955#file-greatbritishpoundtoken-sol-L126#L128

https://gist.github.com/yuriy77k/f6ccd69e766b929c2e282f0f53737955#file-greatbritishpoundtoken-sol-L144#L149

## 3.3. Deprecated method.

### Severity: minor observation

### Description

The `function () payable { revert(); }` was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

### Code snippet

https://gist.github.com/yuriy77k/f6ccd69e766b929c2e282f0f53737955#file-greatbritishpoundtoken-sol-L210-L212

# 4. Conclusion

The audited contract can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/70f6e90f0c7f0e27ca05aac0a8e3f742

https://gist.github.com/yuriy77k/d8b6c3ec53264554e687c0e375b91937

https://gist.github.com/yuriy77k/f6527040c1f285ca4da1819397be4e56
