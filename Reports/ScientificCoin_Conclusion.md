# [ScientificCoin](https://etherscan.io/address/0x8c211128f8d232935afd80543e442f894a4355b7#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The token contract is safe and can be deployed.

# 2. Low severity issues:

## 2.1. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 2.2. No checking for zero address. 

### Description

In [`setController`](https://gist.github.com/yuriy77k/e0b122aeb0e5181725d37f83be8e3455#file-scientificcoin-sol-L125) function there is no zero address checking.

### Recommendation

Add checking for zero address.
```solidity
require(to != address(0));
```

# Revealing audit reports:

https://gist.github.com/yuriy77k/b99bba1a066dd3031bb7bcd48459593e

https://gist.github.com/yuriy77k/b5c7793e113a977e9129ee438d80303b

https://gist.github.com/yuriy77k/5191c5371b037bfdbcd6ef345e320357
