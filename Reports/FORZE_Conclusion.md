# [FORZE](https://etherscan.io/address/0x74bb7e3bddfea47128f64b485881330d3c00d1e0) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The Token contract is safe to use.

# 2. Low severity issues:

## 2.1. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)


# 3. Minor observation:

## 3.1. Old ERC20 Version

### Description

Forze Token contract is newly deployed using `v0.4.24+commit.e67f0147` on the [ethereum blockchain](https://etherscan.io/address/0x74bb7e3bddfea47128f64b485881330d3c00d1e0#code) , but the code itself accept previous solidity version from 0.4.8.
it uses deprecated keywords like `throw` and do not set specifiers explicitly, etc ... .
Contract developers should check the latest versions of ERC20 standard.

## 3.2. Extra checking

### Description

Extra checking in [80](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L80), [81](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L81), [100-102](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L100-L102),[111](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L111), [120](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L120), [129](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L129) lines. SafeMath library checks it anyway.
Solidity automatically throws when dividing by zero in [14](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L14) line. There is no case in which [this](https://gist.github.com/yuriy77k/4f5f9148280105cddc636b1d93dbec37#file-forze-sol-L16) doesn't hold.

### Recommendation

Those lines may be deleted.


# Revealing audit reports:

https://gist.github.com/yuriy77k/07bde0084d1f5056413ea486b07f40c6

https://gist.github.com/yuriy77k/3aec946411848b5ede92373386998edc

https://gist.github.com/yuriy77k/c5acfd9e042afe85072af760ba13df2d
