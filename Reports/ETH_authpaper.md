# 1. Summary

[AuthPaper Token](https://github.com/SolonAuthpaper/AUPC-code/blob/master/AUPC.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [AUPC.sol](https://github.com/SolonAuthpaper/AUPC-code/blob/master/AUPC.sol) github commit hash 335da0c78c7d1b66d2b7f10dea4d3c113bf3c008.

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Call to External Contract

### Severity: low

### Description

The audited contract uses an external call to another contract to convert date to timestamp, when the same functions can be directly implemented to the contract.
The external contract was not disclosed to us for audit, and we cannot judge the timestamps outputs returned by it.

### Code snippet

https://github.com/RideSolo/AUPC-code/blob/master/AUPC.sol#L36

https://github.com/RideSolo/AUPC-code/blob/master/AUPC.sol#L38#L42

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.3. ERC20 Compliance.

### Severity: minor observation

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

# 4. Conclusion

The audited contract is safe without taking into account the external call, for a better analysis a full disclosure is mandatory.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/e24cf5a7f1725b636e222a708996261a

https://gist.github.com/yuriy77k/677fb84301d569fef0cbf81a730d689b

https://gist.github.com/yuriy77k/48d7723581e5ab7222520d64c39bc275
