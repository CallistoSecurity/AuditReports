# [Talenta token swap contract](https://files.fm/u/8hcmwc3c) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The Token swap contract can be considered safe to be deployed, however the tokens swap contract is not dynamic, the contract takes only two tokens and the release time and the amounts are fixed inside the smart contract.

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

## 5.1.  Extra checking.

### Description

Extra checking in 33 line of BasicToken contract. SafeMath library checks it anyway.

### Code snippet
```
    require(_value <= balances[msg.sender]);
    
```
### Recommendation

This line may be deleted.

# Revealing audit reports:

https://gist.github.com/yuriy77k/ba1b397cfb6052c61fdc0c61b0422953

https://gist.github.com/yuriy77k/7291d820ae4ac75d5c3f42691a8d9e19

https://gist.github.com/yuriy77k/9d2dc93d76f7f1ee5f88f783970e48d5
