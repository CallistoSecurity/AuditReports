# Security Audit Report

# 1. Summary

[Crypto.com Chain (CRO)](https://etherscan.io/address/0xa0b73e1ff0b80914ab6fe0444e65848c4c34450b#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Audit Top 200 CoinMarketCap tokens.

Crypto.com Chain | CRO
--|--
Circulating Supply | 4 009 132 420
Total Supply | 100 000 000 000

https://www.crypto.com/en/chain

# 2. In scope

- [ CroToken.sol ](https://gist.github.com/yuriy77k/454881d9a5c08d24c134ddcbb2bc12e4).

# 3. Findings

In total, **1 issues** were reported including:

- 1 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/62050cf3626a27a0262867cc0c934525

https://gist.github.com/yuriy77k/806a7a0991f389e3025d9fa1c3ba2ad2

https://gist.github.com/yuriy77k/ee8423a1f8e8c6783d78d91752aacde4
