# CRAD-DeFi v.3 Security Audit Report

# 1. Summary

[CRAD-DeFi](https://github.com/ixc-software/CRAD-DeFi/tree/master/v2.0/poolRegistry/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [103bb5583c678cf1769de1afd0be4d54da41c1ad](https://github.com/ixc-software/CRAD-DeFi/tree/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts)

- YouBank.sol
- InvestmentPool.sol
- TokensalePool.sol
- LiquidityInvestmentPool.sol
- OpenBiSea.sol
- OpenBiSeaAuction.sol
- ./interface/IPool.sol
- ./interface/IPoolRegistry.sol
- ./interface/IAssetsManageTeam.sol
- ./interface/IReturnInvestmentLpartner.sol
- ./interface/ICreator.sol
- ./interface/IRoleModel.sol
- ./interface/IOracle.sol
- ./interface/IERC20.sol
- ./interface/IOpenBiSeaAuction.sol
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

In total, **11 issues** were reported including:

- 4 high severity issues.

- 2 medium severity issues.

- 0 low severity issues.

- 4 notes.

- 1 owner privileges.


## 3.1. Gas wasting and a risk of OUT_OF_GAS exception.

### Severity: medium

### Description

In the function [investorUsdValue](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/YouBank.sol#L381-L409) use the loop [for()](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/YouBank.sol#L388-L407) to calculate `amountTotalUSD`. Cross-contract calls use much more gas then calculation inside contract therefore it will require a lot of Gas if investor has many deposits. The transaction may never be processed due `OUT_OF_GAS` exception if the required gas amount exceeds a block gas limit.

### Recommendation

To reduce gas usage calculate `amountTotalUSD` inside of pool contract.
Try to rebuild calculation function without `for()` loop. I.e. use create `totalDeposit` variable inside investor record and update it each time when investor deposit or withdraw money.

## 3.2. Wrong calculation

### Severity: high

### Description

When SafeMath functions used the standard math priority rules are not applies, and the order must be indicated explicitly using brackets.

I.e. `a + b * c == a.add(b.mul(c)) != a.add(b).mul(c)`

It the function [investorUsdValue()](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/YouBank.sol#L401-L404) you did not use brackets and did not store result after calculation.

### Recommendation

Lines [401-404](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/YouBank.sol#L401-L404) must be like this:
```Solidity
        amountTotalUSD = amountTotalUSD.add(amountWei.div(10 ** uint256(18))); // invested in BUSD
    } else {
        amountTotalUSD = amountTotalUSD.add(amountWei.mul(priceMainToUSDreturned).div(10 ** uint256(decimals)).div(10 ** uint256(18))); // invested in BNB
```


## 3.3. Code readability.

### Severity: note

### Description

The code has poor readability. Please follow [Style Guide](https://docs.soliditylang.org/en/v0.8.3/style-guide.html) to make code more readable.


## 3.4. The function `_withdrawLPartner` may return wrong result.

### Severity: medium

### Description

In the function [_withdrawLPartner](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/InvestmentPool.sol#L134-L160) of `InvestmentPool` contract calculates [totalAmountReturn](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/InvestmentPool.sol#L150) just for [token == address(0)](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/InvestmentPool.sol#L148-L150). So this function will return wrong [result](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/InvestmentPool.sol#L159) if last deposited `token != address(0)`.

The same issue is in function `_withdrawLPartner` of `TokensalePool`, `LiquidityInvestmentPool`, `OpenBiSea` contracts.

## 3.5. User with `STARTUP_TEAM_ROLE` and `SUPER_ADMIN_ROLE` may withdraw any amount of tokens from the pool

### Severity: owner privileges

### Description

A user with [STARTUP_TEAM_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L115-L126) or with [SUPER_ADMIN_ROLE](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/InvestmentPool.sol#L159-L171) may withdraw any amount of tokens from the pool without restriction.

This possibility exist in the `InvestmentPool`, `TokensalePool`, `LiquidityInvestmentPool`, `OpenBiSea` contracts.

## 3.6. Unused code

### Severity: note

### Description

1. Function [_removeIndexArray](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/TokensalePool.sol#L270-L275) and the same in [LiquidityInvestmentPool](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/LiquidityInvestmentPool.sol#L331-L336) is unused.

2. Function [isContract](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/TokensalePool.sol#L262-L268) and the same in [LiquidityInvestmentPool](https://github.com/ixc-software/CRAD-DeFi/blob/0f501d7f7d70cb531aa6fe6e67f3436b0224c9da/v2.0/poolRegistry/contracts/LiquidityInvestmentPool.sol#L324-L330) is unused.


### Recommendation

Remove unused code.

## 3.7. User may loss money in the function `purchaseTokens()`

### Severity: high

### Description

In the function [purchaseTokens()](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L258-L262) if user sends [amount < 100000000000000000](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L260-L261) the function just returns and user loss money.

### Recommendation

If you what limit minimum amount you have to use `require(amount < 100000000000000000);`.


## 3.8. Losing accuracy

### Severity: high

### Description

In the function `claimFreeTokens()` you are requesting [priceMainToUSD, decimals](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L397) and than [priceMainToUSD.div(10 ** uint256(decimals))](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L400). This will cause of losing values that less than 1. I.e. is price is $1.5 you will get $1. If price is $0.99 you wil get $0. It may cause different issues in future calculation.

The same issue is in the function [updateFirstDateAndValue()](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSeaAuction.sol#L165-L167).

### Recommendation

Keep decimals in calculation values.

## 3.9. Wrong calculation in the function `whitelistContractCreatorTokens()`

### Severity: high

### Description

The function [whitelistContractCreatorTokens()](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L325-L330) has wrong calculation of `_totalIncome`. 

Due contract logic the variable [_totalIncome](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L322) contain amount of BNB (with 18 decimals). But you calculate [amount = (10 ** uint256(18)).mul(_auctionCreationFeeMultiplier)](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L326), so `amount` will contain value with 18 decimals. Then you [initialPriceInt.mul(amount)](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L328) , where [initialPriceInt](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L244) also has 18 decimals. So will receive very big result (with 36 decimals) and add it to `_totalIncome`.

## 3.10. Buyer and Seller may claim token

### Severity: note

### Description

In the function `updateFirstDateAndValue()` the same value [adding to buyer and seller](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSeaAuction.sol#L170-L171), so buyer and seller may [claimFreeTokens()](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSea.sol#L394-L400).


## 3.11. Unused variable `tokensForClaim`

### Severity: note

### Description

The variable [tokensForClaim](https://github.com/ixc-software/CRAD-DeFi/blob/103bb5583c678cf1769de1afd0be4d54da41c1ad/v2.0/poolRegistry/contracts/OpenBiSeaAuction.sol#L293) in declared but never assigned.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues (at least medium severity) must be fixed prior to the usage of this contract.

The logic splits among many different contracts that can be changed by Team. Therefore overall behavior is unpredictable. Also using many cross-contracts calls significantly increase the Gas usage.

Will be better to refactor entire codebase to make it more readable with clear logic.


