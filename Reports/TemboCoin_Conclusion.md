# 1. Summary

[TemboCoin](https://etherscan.io/address/0x1de09690e0d3c75c22cd19acc1aebde46bbc7d25#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [TemboCoin.sol](https://gist.github.com/yuriy77k/47c7f36eecdb3046c82f16f702443fc1)

# 3. Findings

In total, **2 issues** were reported including:

-  2 low severity issues.

No critical security issues were found.

## 3.1. Transfer to Address 0x0 and Burn Mechanism

### Severity: low

### Description

Transfer to address zero in the audited contract is used as basic burn mechanism (check `totalSupply`, `balances[address(0)]` is deducted from the total supply), this mechanism open the doors also for sending tokens by mistake to address 0x0.

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4. Conclusion

The audited contract can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/26321d35eb7bd9654a9fda86293a7131

https://gist.github.com/yuriy77k/e85e7527ccf4b65388a1c3b5ca519f7c

https://gist.github.com/yuriy77k/8995d074ad4e6ce6f9c05dfd9049ccb0
