# 1. Summary

[iAM Token](https://etherscan.io/address/0x6025fb154b7c30e13657d5304dafdb55b194e5dd#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [iam.sol](https://etherscan.io/address/0x6025fb154b7c30e13657d5304dafdb55b194e5dd#code)

# 3. Findings

**1 issue** was reported:

- 1 low severity issue.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 4. Conclusion

The audited contract is safe and can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/a0c1bded0c23f4bbcbebe8d3c02b80cb

https://gist.github.com/yuriy77k/9ae7ff886193bc7048f759a784f1b5da

https://gist.github.com/yuriy77k/cf8e874dd76942b0d7f2eadde25a3488
