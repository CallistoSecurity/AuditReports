# 1. Summary

[Odin platform](https://etherscan.io/address/0xf9cf56cda5ba1a3b8143d02b72a873203b7f309a#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The Odin platform is a safe and simple all-in-one marketplace to promote the ICO token and the circulation of virtual currency-related services which passed rigorous ODIN Wealth Management AG (ODIN WMA) checks.  At the same time, government and financial regulators are on high alert to protect its citizens and investors alike by defining and implementing rules & regulations to prevent potential fraud. As a result, Odin WMA is working on whitelisting measures with leading law firms to hedge ICO related risks through a rigorous vetting process enforced through the Odin platform.

# 2. In scope

- [OdinCoin.sol](https://gist.github.com/yuriy77k/2f13782b4391b82c16654fbced3c04dd)

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. The reserve address 0x0 checking

### Severity: low

### Description

It is advisable to check the input address. If you do not pass the address by accident, the tokens can be sent to 0x0 address.

### Code snippet

https://gist.github.com/yuriy77k/2f13782b4391b82c16654fbced3c04dd#file-odincoin-sol-L114-L116

# 4. Conclusion

The audited contract is safe.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/aad807f61be5962a49bdbc63ce6938b0

https://gist.github.com/yuriy77k/6f108538ae8c0bc634560e8c8a76d2b0

https://gist.github.com/yuriy77k/7d1f5e28d7fb29fcf712ba3dc82df035
