# 1. Summary

[Aqua Intel](https://www.dropbox.com/s/m2oh64o01tmwzpo/AQX_2.sol?dl=0) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token Description:

    Name: AQUA
    Symbol: AQX
    Decimals: 10
    Standard: ERC20
    Total Supply: 500,000,000

# 2. In scope

- AQX_2.sol

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Just one burn call. 

### Severity: low

### Description

Owner will be available to [`burn`](https://gist.github.com/yuriy77k/64fcfd4cd9bc7678711b6d85500ea79a#file-aqx_2-sol-L68) tokens just once. That means, if he will fail, there will be no chance to fix this issue.

# 4. Conclusion

The token contract is safe and can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f7554aa17cf7b2da033ac0116022e33f

https://gist.github.com/yuriy77k/80e37c21bdd4a5c830794dbb13e51174

https://gist.github.com/yuriy77k/4ed2ff9ea1ce4d03e9f22b80811da380
