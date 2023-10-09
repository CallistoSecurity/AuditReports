# [Cyber Capital Invest Token](https://gist.github.com/cybercapitalinvest/b68f7a861fa6236310cec32db32df825) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

No critical vulnerabilities were detected. Smart contract has low severity issues, please fix them before deploying.

# 2. Low severity issues:

## 2.1. Token Transfer to Address 0x0

### Description

`transfer` function member of `StandardToken` contract do not require the `to` address to be non null before `transfer`. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://gist.github.com/cybercapitalinvest/b68f7a861fa6236310cec32db32df825#file-gistfile1-txt-L141#L145

## 2.2. Approve Race

### Description

The implemented `approve` function does not completely solve the `double withdrawal attack`, since the user will have to check his balance to confirm that the spender didn't transfer the allowed tokens after resetting it to zero . Because even when setting the allowed value to zero, the spender address can transfer the allowed tokens just before it.

### Code snippet

https://gist.github.com/cybercapitalinvest/b68f7a861fa6236310cec32db32df825#file-gistfile1-txt-L156#L166

## 2.3. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 3. Minor observation:

## 3.1. No usage of contract

### Description

[`Haltable`](https://gist.github.com/cybercapitalinvest/b68f7a861fa6236310cec32db32df825#file-gistfile1-txt-L78) contract has no usage in main contract, it could be deleted.


# Revealing audit reports:

https://gist.github.com/yuriy77k/75af085fd08344b4c2f2ee37e4324957

https://gist.github.com/yuriy77k/ffe1a07750cf8ca79dbdcd6d6a2c0ad2

https://gist.github.com/yuriy77k/4b37a7e30085d6df862b05ed8ea4e709
