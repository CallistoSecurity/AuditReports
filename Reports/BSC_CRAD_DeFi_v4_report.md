# CRAD-DeFi v.4 Security Audit Report

# 1. Summary

[CRAD-DeFi](https://github.com/ixc-software/CRAD-DeFi/tree/master/v2.0/poolRegistry/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [be36084d45c100d4db6f26606a7602076f61ec8b](https://github.com/ixc-software/CRAD-DeFi/tree/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts)

- YouBank.sol
- InvestmentPool.sol
- TokensalePool.sol
- LiquidityInvestmentPool.sol
- ./interface/IPool.sol
- ./interface/IPoolRegistry.sol
- ./interface/IAssetsManageTeam.sol
- ./interface/IReturnInvestmentLpartner.sol
- ./interface/ICreator.sol
- ./interface/IRoleModel.sol
- ./interface/IOracle.sol
- ./interface/IERC20.sol
- ./interface/IERC721.sol
- ./interface/IERC1155.sol
- ./interface/IERC721Receiver.sol
- ./interface/IERC1155Receiver.sol
- ./access/TeamRole.sol
- ./access/lib/Roles.sol
- ./access/PoolRoles.sol
- ./access/AccessControl.sol
- ./utils/EnumerableSet.sol
- ./utils/EnumerableUintSet.sol
- ./math/SafeMath.sol
- ./ownership/Ownable.sol
- ./GSN/Context.sol

## 2.1. Excluded

1. Standard Uniswap interfaces:
- @uniswap/v2-periphery/contracts/interfaces/IUniswapV2Router02.sol
- @uniswap/v2-core/contracts/interfaces/IUniswapV2Factory.sol
- @uniswap/v2-core/contracts/interfaces/IUniswapV2Pair.sol

2. The audited contracts use other contracts by its interface. We can't be sure what those contracts do, because their addresses are changeable. The list of interfaces of those contracts:
- IPool
- IAssetsManageTeam
- IReturnInvestmentLpartner
- IOracle

3. The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.

# 3. Findings

In total, **5 issues** were reported including:

- 0 high severity issues.

- 2 medium severity issues.

- 0 low severity issues.

- 2 notes.

- 1 owner privileges.


## 3.1. Gas wasting and risk of OUT_OF_GAS exception.

### Severity: medium

### Description

In the function [investorUsdValue](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/YouBank.sol#L381-L409) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/YouBank.sol#L388-L407) to calculate `amountTotalUSD`. Cross-contract calls use much more gas than calculation inside contract therefore it will require a lot of Gas if an investor has many deposits. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.

### Recommendation

To reduce gas usage calculate `amountTotalUSD` inside of pool contract.
Try to rebuild calculation function without `for()` loop. I.e. use creates `totalDeposit` variable inside investor record and update it each time when investor deposit or withdraw money.


## 3.2. Code readability.

### Severity: note

### Description

The code has poor readability. Please follow [Style Guide](https://docs.soliditylang.org/en/v0.8.3/style-guide.html) to make the code more readable.


## 3.3. The function `_withdrawLPartner` may return the wrong result.

### Severity: medium

### Description

In the function [_withdrawLPartner](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/InvestmentPool.sol#L134-L160) of `InvestmentPool` contract calculates [totalAmountReturn](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/InvestmentPool.sol#L150) just for [token == address(0)](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/InvestmentPool.sol#L148-L150). So this function will return wrong [result](https://github.com/ixc-software/CRAD-DeFi/blob/be36084d45c100d4db6f26606a7602076f61ec8b/v2.0/poolRegistry/contracts/InvestmentPool.sol#L159) if last deposited `token != address(0)`.

The same issue is in function `_withdrawLPartner` of `TokensalePool`, `LiquidityInvestmentPool`, `OpenBiSea` contracts.

## 3.4. User with `STARTUP_TEAM_ROLE` and `SUPER_ADMIN_ROLE` may withdraw any amount of tokens from the pool

### Severity: owner privileges

### Description

A user with [STARTUP_TEAM_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L115-L126) or with [SUPER_ADMIN_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L159-L171) may withdraw any amount of tokens from the pool without restriction.

This possibility exist in the `InvestmentPool`, `TokensalePool`, `LiquidityInvestmentPool`, `OpenBiSea` contracts.

## 3.5. Unused code

### Severity: note

### Description

1. Function [_removeIndexArray](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/TokensalePool.sol#L270-L275) is unused.

2. Function [isContract](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/TokensalePool.sol#L262-L268) is unused.


### Recommendation

Remove unused code.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues (at least medium severity) must be fixed prior to the usage of this contract.

The logic splits among many different contracts that can be changed by Team. Therefore overall behavior is unpredictable. Also using many cross-contracts calls significantly increases Gas usage.

Will be better to refactor the entire codebase to make it more readable with clear logic.


