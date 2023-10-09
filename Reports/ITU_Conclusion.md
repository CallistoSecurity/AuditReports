# [ITU](https://github.com/itrueio/ITU/blob/master/itu.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The token contract can be considered safe to be deployed, however the issue 4.1 has to be taken seriously to avoid any possible repercussion.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

No Medium severity issues

# 4. Low severity issues:

## 4.1. Administration Power

### Description

The whitepaper and the website description do not describe the power of the token contract owner.
    
- Token transfer can be stopped and started by the owner using `tstart` and `tstop` function.
- The contract owner can transfer token from any address to receiver address without permission from the user using `transferF` and `transferHold` functions.

The repercussions of a private key hack, maybe be small when it is related to a single user, but when the repercussions extends to every investor it has to be taken seriously.

### Code snippet

https://github.com/itrueio/ITU/blob/master/itu.sol#L4

https://github.com/itrueio/ITU/blob/master/itu.sol#L76#L78

https://github.com/itrueio/ITU/blob/master/itu.sol#L80#L82

https://github.com/itrueio/ITU/blob/master/itu.sol#L118#L121

https://github.com/itrueio/ITU/blob/master/itu.sol#L123#L130

## 4.2. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4.3. Transfer Ownership to Address 0x0

### Description

It is possible to accidentally transfer ownership to 0x0 address in contract owned.

### Code snippet

https://github.com/itrueio/ITU/blob/3228cbebd417965fca61222feebc6c406105cc4b/itu.sol#L15-L18

### Recommendation

Use condition to check 0x0 address.

```require(newOwner != address(0));```

# 5. Minor observation:

## 5.1. Extra checking

### Description

Extra checking in [104](https://github.com/itrueio/ITU/blob/master/itu.sol#L104) line. Previous require checks this condition too.

### Recommendation

[This line](https://github.com/itrueio/ITU/blob/master/itu.sol#L104) may be deleted. 


# Revealing audit reports:

https://gist.github.com/yuriy77k/21ac299b648819821be1669007d484d6

https://gist.github.com/yuriy77k/ee67516c1a39cdd63bbdcc5b8732319c

https://gist.github.com/yuriy77k/c6bb612c3aa681c0ac077408aa43d067


