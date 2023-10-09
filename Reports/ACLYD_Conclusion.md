# [Aclyd](https://etherscan.io/address/0x34b4af7c75342f01c072fa780443575be5e20df1#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

No critical issue, bug fixing is necessary. It is highly recommended to complete a bug bounty before use.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

## 3.1. Token Transfer to Address 0x0

### Description

ACLYD Token do not require the `to` address to be non null before `transfer`. Accidental token loss to address 0x0 can be applicable.

The version of ERC20 used in this contract, use a basic burn mechanism where anyone can send tokens to 0x0 address to burn them. However, this mechanism leads to the above mentioned issue.

### Code snippet

https://gist.github.com/RideSolo/4f79d8eac58dada3c0084f5bc35ae160#file-aclyd-sol-L146#L151

https://gist.github.com/RideSolo/4f79d8eac58dada3c0084f5bc35ae160#file-aclyd-sol-L128#L130

### Recommendation

Use burning function instead of transfer to address(0).

# 4. Low severity issues:

## 4.1. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)



# Revealing audit reports:

https://gist.github.com/yuriy77k/a37bdda06dd2049d9a7393d8ca320039

https://gist.github.com/yuriy77k/69c2c11ff529299e84789f87286fdded

https://gist.github.com/yuriy77k/2b1f4789a522476a43abc39f0257aa04

