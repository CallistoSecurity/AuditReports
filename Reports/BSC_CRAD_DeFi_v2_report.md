# CRAD-DeFi v.2 Security Audit Report

# 1. Summary

[CRAD-DeFi](https://github.com/ixc-software/CRAD-DeFi/tree/master/v2.0/poolRegistry/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [0f501d7f7d70cb531aa6fe6e67f3436b0224c9da](https://github.com/ixc-software/CRAD-DeFi/tree/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts)

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
- ./access/TeamRole.sol
- ./access/lib/Roles.sol
- ./access/PoolRoles.sol
- ./access/AccessControl.sol
- ./utils/EnumerableSet.sol
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

In total, **6 issues** were reported including:

- 2 medium severity issues.

- 1 low severity issues.

- 2 notes.

- 1 owner privileges.


## 3.1. Gas wasting and a risk of OUT_OF_GAS exception.

### Severity: medium

### Description

In the function [investorUsdValue](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/YouBank.sol#L383-L410) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/YouBank.sol#L390-L408) to calculate `amountTotalUSD`. Cross-contract calls use much more gas then calculation inside contract therefore it will require a lot of Gas if investor has many deposits. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.

### Recommendation

To reduce gas usage calculate `amountTotalUSD` inside of pool contract.
Try to rebuild calculation function without `for()` loop. I.e. use create `totalDeposit` variable inside investor record and update it each time when investor deposit or withdraw money.

## 3.2. Overflow/underflow is possible.

### Severity: low

### Description

In calculation of [_poolValuesTotal, _poolValuesUSDTotal](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/YouBank.sol#L267-L268) the overflow/underflow is possible.

### Recommendation

When you are using user-input values in calculation you have to always use SafeMath to avoid overflow/underflow.
```Solidity
    _poolValuesTotal = _poolValuesTotal.add(poolValue).sub(poolValueforSumOld);
    _poolValuesUSDTotal = _poolValuesUSDTotal.add(poolValueUSD).sub(poolValueUSDforSumOld);
```


## 3.3. Code readability.

### Severity: note

### Description

The code has poor readability. Please follow [Style Guide](https://docs.soliditylang.org/en/v0.8.3/style-guide.html) to make code more readable.


## 3.4. Usage different tokens deposits to calculate return amount.

### Severity: medium

### Description

In the function [_withdrawLPartner](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L132-L157) of `InvestmentPool` contract you may mixed up the `deposit.amountWithdrawal` of different tokens (if investor has used different tokens) when calculate [totalAmountReturn](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L141). So this function may return wrong [result](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L157).

The same issue is in function `_withdrawLPartner` of `TokensalePool` and `LiquidityInvestmentPool` contracts.

## 3.5. User with `STARTUP_TEAM_ROLE` and `SUPER_ADMIN_ROLE` may withdraw any amount of tokens from the pool

### Severity: owner privileges

### Description

A user with [STARTUP_TEAM_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L115-L126) or with [SUPER_ADMIN_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L159-L171) may withdraw any amount of tokens from the pool without restriction.

This possibility exist in the `InvestmentPool`, `TokensalePool` and `LiquidityInvestmentPool` contracts.

## 3.6. Unused code

### Severity: note

### Description

1. Function [_removeIndexArray](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/TokensalePool.sol#L270-L275) and the same in [LiquidityInvestmentPool](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/LiquidityInvestmentPool.sol#L331-L336) is unused.

2. Function [isContract](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/TokensalePool.sol#L262-L268) and the same in [LiquidityInvestmentPool](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/LiquidityInvestmentPool.sol#L324-L330) is unused.


### Recommendation

Remove unused code.


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues (at least medium severity) must be fixed prior to the usage of this contract.

The logic splits among many different contracts that can be changed by Team. Therefore overall behavior is unpredictable. Also using many cross-contracts calls significantly increase the Gas usage.

Will be better to refactor entire codebase to make it more readable with clear logic.


