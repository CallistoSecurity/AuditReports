# CRAD-DeFi Security Audit Report

# 1. Summary

[CRAD-DeFi](https://github.com/ixc-software/CRAD-DeFi/tree/master/v2.0/poolRegistry/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [a998c13b399e2e9ae210074f03951aa7e24d582c](https://github.com/ixc-software/CRAD-DeFi/tree/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts)

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

In total, **13 issues** were reported including:

- 1 critical severity issues.

- 1 high severity issues.

- 5 medium severity issues.

- 5 notes.

- 1 owner privileges.


## 3.1. Gas wasting and a risk of OUT_OF_GAS exception.

### Severity: medium

### Description

1. In the library `EnumerableSet` the function [_remove()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/utils/EnumerableSet.sol#L18) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/utils/EnumerableSet.sol#L28-L33) to find address in the array then use another [for()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/utils/EnumerableSet.sol#L49-L51) loop to remove address from array. It requires a lot of Gas if many addresses are in the array. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.

We recommend to rebuild this function without loops or replace current `EnumerableSet` library with [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) library from Openzeppelin.

2. In the library `Roles` the function [remove()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/access/lib/Roles.sol#L12) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/access/lib/Roles.sol#L14-L19) to find address in the array then use another [for()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/access/lib/Roles.sol#L27-L29) loop to remove address from array. It requires a lot of Gas if many addresses are in the array. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.
The similar issue is in the function [_revokeRole](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/access/AccessControl.sol#L52-L66) of `AccessControl` contract.

We recommend to rebuild this function without loops or use [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) instead of [address array](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/access/lib/Roles.sol#L4). So you could remove record using function [remove()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/243adff49ce1700e0ecb99fe522fb16cff1d1ddc/contracts/utils/structs/EnumerableSet.sol#L157).

3. In the function [setPoolValues()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L251) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L259-L265) to calculate new total values. Cross-contract calls use much more gas then calculation inside contract therefore it will require a lot of Gas if many pools are in the array. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.

We recommend to refactor logic of calculation of new total amounts to read info just from one pool which amount will be changed.

4.  In the function [investorUsdValue](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L368) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L375-L393) to calculate `amountTotalUSD`. Cross-contract calls use much more gas then calculation inside contract therefore it will require a lot of Gas if investor has many deposits. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.

## 3.2. Typos

### Severity: note

### Description

There are some typos in functions / variables name: `mulitpier`, `mulitpierDefault`, `feesMulitpier`, `getAddrRequestsReturnInvesLpartner`, `anualPrecent`, `lenghtDepSender`, `getReturnInvesmentLpartner`, `referalAddress`, `feesMulitpier`, `requestTokensWithdwawalFromStartup`, `approveTokensWithdwawalFromStartup`, `lenghtSender`.



## 3.3. User may use fake pool address

### Severity: note/High

### Description

1. The function [returnsFromStartupTeam()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L177) transfer `msg.value` to user-provided pool address and emit [ReturnsFromStartupTeam](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L180) event. 
So user may provide any address to transfer and emit those event. The severity depends on what action `ReturnsFromStartupTeam` event trigger. The `require` statement does not protect from sending fake pool address because it relies on that pool response.

2. User may use fake pool address to increase [_investedFunds](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L172) and emit [DepositToPool](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L173) event. The severity depends on what action this change trigger.

3. User may use fake pool address to change `_poolValues[pool]` and `_poolValuesUSD[pool]` in [setPoolValues()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L251) function and emit [SetPoolValues](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L272). The `require` statement does not protect from sending fake pool address because it relies on that pool response.

### Recommendation

You have to validate user-provided inputs. Check the pool address with the [_addressesPools](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L22) list.

## 3.4. Code readability.

### Severity: note

### Description

The code has poor readability. Please follow [Style Guide](https://docs.soliditylang.org/en/v0.8.3/style-guide.html) to make code more readable.


## 3.5. A user may steal `tokens for sale`.

### Severity: Critical

### Description

If user send to function [claimFreeTokens()](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L410) fake pool address he can steal `tokens for sale`. Fake pool will return info that user made a big deposit and eligible for receiving tokens.

### Recommendation

You have to validate user-provided inputs. Check the pool address with the [_addressesPools](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/YouBank.sol#L22) list.

## 3.6. Ownership is not transferable

### Severity: note

### Description

The deployer of contract becomes its owner. In some cases (compromised deployer key, changing project owner, etc) may be required to transfer ownership to other address but there is not such function in the [Ownable](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/ownership/Ownable.sol) contract.

### Recommendation

Add function to transfer ownership or use [Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) from OpenZeppelin.

## 3.7. Usage different tokens deposits to calculate return amount.

### Severity: high

### Description

In the function [_withdrawLPartner](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/InvestmentPool.sol#L132) of `InvestmentPool` contract overwrites [token](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/InvestmentPool.sol#L143) address and mixed up the [tokens deposits amount](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/InvestmentPool.sol#L140) when calculate `totalAmountReturn`. To user will transfer the [last deposited token](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/InvestmentPool.sol#L143) but in total amount of all deposited (different) tokens.

The same issue is in function `_withdrawLPartner` of `TokensalePool` and `LiquidityInvestmentPool` contracts.

## 3.8. User with `STARTUP_TEAM_ROLE` and `SUPER_ADMIN_ROLE` may withdraw any amount of tokens from the pool

### Severity: owner privileges

### Description

A user with [STARTUP_TEAM_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/InvestmentPool.sol#L115-L126) or with [SUPER_ADMIN_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/InvestmentPool.sol#L160-L172) may withdraw any amount of tokens from the pool without restriction.

This possibility exist in the `InvestmentPool`, `TokensalePool` and `LiquidityInvestmentPool` contracts.

## 3.9. Unused code

### Severity: note

### Description

1. Variable [teamReward = 0](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/TokensalePool.sol#L119) so [this code](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/TokensalePool.sol#L132) never used.

2. Function [_removeIndexArray](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/TokensalePool.sol#L275-L280) and [here](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/LiquidityInvestmentPool.sol#L328-L333) is unused.

3. Function [isContract](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/TokensalePool.sol#L267-L273) and [here](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/LiquidityInvestmentPool.sol#L321-L327) is unused.

4. [Transfer](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/TokensalePool.sol#L228) coins to self has no sense.

### Recommendation

Remove unused code.

## 3.10. Wrong calculation

### Severity: medium

### Description

The calculation of [newPrice](https://github.com/ixc-software/CRAD-DeFi/blob/a998c13b399e2e9ae210074f03951aa7e24d582c/v2.0/poolRegistry/contracts/TokensalePool.sol#L215) will be wrong due loss accuracy. You have to `mul` before `div`.

### Recommendation

Replace that line with:
```
uint256 newPrice = (initialPriceInt.mul(delta).div(balance)).add(initialPriceInt);
```


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

The logic splits among many different contracts that can be changed by Team. Therefore overall behavior is unpredictable. Also using many cross-contracts calls significantly increase the Gas usage.

Will be better to refactor entire codebase to make it more readable with clear logic.


