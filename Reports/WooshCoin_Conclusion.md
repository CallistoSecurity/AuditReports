# [WooshCoin](https://etherscan.io/address/0xb5351d809062cf8e2238bbd2c54af905fb55e42d#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The Token contract is safe to be deployed.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

No Medium severity issues

# 4. Low severity issues:

## 4.1. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)


# 5. Minor observation:

## 5.1. Extra checking at transfer functions. 

### Description

In functions [`transfer`](https://gist.github.com/yuriy77k/33e2c2308a44b4f833385a8d72eac1b9#file-wooshcoin-sol-L101) and [`transferFrom`](https://gist.github.com/yuriy77k/33e2c2308a44b4f833385a8d72eac1b9#file-wooshcoin-sol-L123) there are extra checking at lines [103](https://gist.github.com/yuriy77k/33e2c2308a44b4f833385a8d72eac1b9#file-wooshcoin-sol-L103), [125](https://gist.github.com/yuriy77k/33e2c2308a44b4f833385a8d72eac1b9#file-wooshcoin-sol-L125), [126](https://gist.github.com/yuriy77k/33e2c2308a44b4f833385a8d72eac1b9#file-wooshcoin-sol-L126). SafeMath library will check it automatically.

# Revealing audit reports:

https://gist.github.com/yuriy77k/91031e3249ccbe07cc23ef06f52cbf03

https://gist.github.com/yuriy77k/03c1a9df48f5eadb5d7b135e7057e8ba

https://gist.github.com/yuriy77k/d868406f2c0d1c5f604b2a635a578f2d

