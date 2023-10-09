# Security Audit Report

# 1. Summary

[ODEM Token (ODEM)](https://etherscan.io/address/0xbf52f2ab39e26e0951d2a02b49b7702abe30406a#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Top100 coin

ODEM.IO is developing a first-of-its-kind global, decentralized education marketplace to deliver complete, end-to-end, custom, in-person education programs, and experiences.

# 2. In scope

- [ODEMToken.sol](https://gist.github.com/yuriy77k/7ef83a27f05279e33350737c141fe735).

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

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

## 3.2. Owner's Privileges

### Severity: low

### Code snippet

* [whenNotPaused](https://gist.github.com/yuriy77k/7ef83a27f05279e33350737c141fe735#file-odemtoken-sol-L332-L350)

### Description

The contract owner allow himself to pause functions of contract (`transfer`, `transferFrom`, `approve`).

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c037e6e025e6cfaf935898512deb73b6

https://gist.github.com/yuriy77k/9b5ccb26aceb5cb043089da82adf5471

https://gist.github.com/yuriy77k/0a9ad4263f6716989838c7a066686acf
