# Holo Token Security Audit Report

# 1. Summary

[Holo Token](https://etherscan.io/address/0x6c6ee5e31d828de241282b9606c8e98ea48526e2#contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

1. https://etherscan.io/address/0x6c6ee5e31d828de241282b9606c8e98ea48526e2#contracts

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. Zero address checking

### Severity: low

### Description

There are no zero address checking in functions `setMinter` at line 239, `setDestroyer` at line 269 and `mint` at line 243.

## 3.3. Owner privileges

### Severity: owner privileges

### Description

Owner can mint any amount of tokens and for a long period of time, because he can not finish minting and can set himself as minter.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c6a8d409a858f6155780ea7f4addd097

https://gist.github.com/yuriy77k/dcea1e26ba3e6e73e5e51a77e5d241d3

https://gist.github.com/yuriy77k/4afef9710e08a8a9030e58c8590019b6
