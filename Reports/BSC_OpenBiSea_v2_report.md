# OpenBiSea v.2 Security Audit Report

# 1. Summary

[OpenBiSea](https://github.com/ixc-software/CRAD-DeFi/tree/master/v2.0/poolRegistry/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://openbisea.io/
- https://twitter.com/openbisea
- https://t.me/openbisea

# 2. In scope

- https://bscscan.com/address/0x1Bf12f0650d8065fFCE3Cd9111feDEC21deF6825#contracts

- https://bscscan.com/address/0x1c2b69833967500042d476B1149D4074B59c1A17#contracts

## 2.1. Excluded

1. Standard Uniswap interfaces:
- @uniswap/v2-periphery/contracts/interfaces/IUniswapV2Router02.sol
- @uniswap/v2-core/contracts/interfaces/IUniswapV2Factory.sol
- @uniswap/v2-core/contracts/interfaces/IUniswapV2Pair.sol

2. The audited contracts use other contracts by their interface. We can't be sure what those contracts do, because their addresses are changeable. The list of interfaces of those contracts:
- IPool
- IAssetsManageTeam
- IReturnInvestmentLpartner
- IOracle

3. The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.

# 3. Findings

In total, **2 issues** were reported including:

- 0 high severity issues.

- 1 medium severity issues.

- 0 low severity issues.

- 1 notes.


## 3.1. Losing accuracy

### Severity: medium

### Description

In the function `claimFreeTokens()` you are requesting [priceMainToUSD, decimals](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/OpenBiSea.sol#L397) (OpenBiSea.sol line 397) and than [priceMainToUSD.div(10 ** uint256(decimals))](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/OpenBiSea.sol#L400) (OpenBiSea.sol line 400). This will cause of losing values that less than 1. I.e. is price is $1.5 you will get $1. If price is $0.99 you will get $0. It may cause different issues in future calculation.

The same issue is in the function [updateFirstDateAndValue()](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/OpenBiSeaAuction.sol#L165-L167) (OpenBiSeaAuction.sol lines 165-167).

### Recommendation

Keep decimals in calculation values.

## 3.2. Buyer and Seller may claim token

### Severity: note

### Description

In the function `updateFirstDateAndValue()` the same value [adding to buyer and seller](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/OpenBiSeaAuction.sol#L170-L171) (OpenBiSeaAuction.sol lines 170-171), so buyer and seller may [claimFreeTokens()](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/OpenBiSea.sol#L394-L400) (OpenBiSea.sol lines 394-400).

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues (at least medium severity) must be fixed prior to the usage of this contract.

The logic splits among many different contracts that can be changed by Team. Therefore overall behavior is unpredictable. Also using many cross-contracts calls significantly increases Gas usage.

Will be better to refactor the entire codebase to make it more readable with clear logic.