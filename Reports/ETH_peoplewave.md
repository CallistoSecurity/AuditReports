# 1. Summary

[Peoplewave Token](https://etherscan.io/address/0xbecf5bd3df959098ff71605569395787174154d9#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

[PeopleWaveToken smart contract](https://etherscan.io/address/0xbecf5bd3df959098ff71605569395787174154d9#code)

# 3. Findings

In total, **1 issues** were reported including:

- 1 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

# 4. Conclusion

The audited token smart contract does not contain any critical issue and can be used safely.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/927d31cb5c2c5d2e8bb5f5141bd50bda

https://gist.github.com/yuriy77k/8da30d72bfcb6343366a26b82d595309

https://gist.github.com/yuriy77k/d34a5f7401da469d44e606a432374daa

