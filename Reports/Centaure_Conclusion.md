# [Centaure](https://github.com/centaureio/centaure/blob/master/centaure.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The described issues should be fixed. The audited contract is not fully ERC20 compliant.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

## 3.1. Token Transfer to Address 0x0

### Description

Centaure Token do not require the `to` address to be non null before `transfer`. Accidental token loss to address 0x0 can be applicable.

The version of ERC20 used in this contract, use a basic burn mechanism where anyone can send tokens to 0x0 address to burn them. However, this mechanism leads to the above mentioned issue.

### Code snippet

https://github.com/centaureio/centaure/blob/master/centaure.sol#L206#L211

https://github.com/centaureio/centaure/blob/master/centaure.sol#L188#L190


# 4. Low severity issues:

## 4.1. Total Supply

### Description

`totalSupply` function return the value of `_totalSupply  - balances[address(0)]`, since 0x0 is used to send the burned tokens to it, However the implemented lock mechanism will also save the locked tokens in address 0x0, until the claim day by the contract owner.

The value returned by `totalSupply` will increase when the contract owner will withdraw the locked tokens. `totalSupply` function represent the circulating supply not the total supply.

### Code snippet

https://github.com/centaureio/centaure/blob/master/centaure.sol#L188#L190

https://github.com/centaureio/centaure/blob/master/centaure.sol#L133#L135

https://github.com/centaureio/centaure/blob/master/centaure.sol#L138#L142

## 4.2. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)


# 5. Minor observation:

## 5.1. Old solidity version. 

### Description

Used solidity version in [Centaure](https://github.com/centaureio/centaure/blob/master/centaure.sol) is old. 

### Recommendation

Need to use one of the latest version of solidity.


# Revealing audit reports:

https://gist.github.com/yuriy77k/3845a51483e3ee7c5ac5e7a0d1bbe4c5

https://gist.github.com/yuriy77k/57e800752c51996212f702642af9423e

https://gist.github.com/yuriy77k/b2fa0fc1f73c46593f5543b026067197


