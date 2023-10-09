# 1. Summary

[Pipschain](https://etherscan.io/address/0x59db9fde270b39a07f38fa3106a760829074c7d9#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [pipschain.sol](https://gist.github.com/yuriy77k/b3963b23b40e97b7bdf1f2a239079bc4)

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. ERC20 Compliance

### Severity: low

### Description

Accroding to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emited.
An event isn't emited when assigning the initial supply to the msg.sender.

## 3.2. Zero Address transfer

### Severity: low

### Description

`transfer` & `transferFunction` function do not prevent from sending tokens to address 0x0.

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4. Conclusion

The contract can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/56e6a205f79823b27b48ad2fe31c9d1f

https://gist.github.com/yuriy77k/959dd907d7c9d45141392faac72beb17

https://gist.github.com/yuriy77k/65d9aaa4573d94f643b5a5ec8c57e409
