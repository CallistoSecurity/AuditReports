# [Atomz](https://etherscan.io/address/0x7a43c09c6babb4f4d96e3e2e389523c3e478be82#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The token contract can be considered safe to be deployed.

# 2. Low severity issues:

## 2.1. Known Issues of ERC20 Standard

### Description

ERC20 Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

- Approve + transferFrom mechanism allows double Withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
- Lack of transaction handling. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)


# 3. Minor observation:

## 3.1. Extra checking

### Description

Extra checking in [92](https://gist.github.com/yuriy77k/fbf55263bc29aa1c9710c19b3c1f798b#file-atomz-sol-L92) line. SafeMath library checks it anyway.

### Recommendation

This line may be deleted.


# Revealing audit reports:

https://gist.github.com/yuriy77k/e4ea968e4f2a4475a5aec0433b9d0e23

https://gist.github.com/yuriy77k/5a14fc9dbc29c1129c4517bbf9bf5162

https://gist.github.com/yuriy77k/dba77a5bcd7f9d28b6246b1ae413efd1

