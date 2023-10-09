# 1. Summary

[HYDRO Token](https://github.com/hydrogen-dev/smart-contracts/blob/master/hydro-token-and-server-raindrop/contracts/HydroToken.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [HydroToken.sol](https://github.com/hydrogen-dev/smart-contracts/blob/master/hydro-token-and-server-raindrop/contracts/HydroToken.sol) github commit hash 01146703bddae7658cac9afa3a8009d07fdef819.

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Representation

### Severity: minor observation

### Description

Better use [`decimal`](https://github.com/hydrogen-dev/smart-contracts/blob/master/hydro-token-and-server-raindrop/contracts/HydroToken.sol#L18) variable in calculating [`totalSuplay`](https://github.com/hydrogen-dev/smart-contracts/blob/master/hydro-token-and-server-raindrop/contracts/HydroToken.sol#L33) amount.

## 4. Conclusion

The contract can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/bcdc452765c91d75ebc6b9e303b4c0a8

https://gist.github.com/yuriy77k/31c5afe97574047f8a5c842df9776432

https://gist.github.com/yuriy77k/2c309e2dfe1b3ea5190fa12e3035d84c
