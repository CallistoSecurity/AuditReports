# Frax-stake Security Audit Report

# 1. Summary

[Frax-stake](https://github.com/eepdev/frax-stake) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The contracts are vaults for Matic Network/Ethereum that take Quickswap/Uniswap LP token deposits and optimize yields on them.

Website: https://adamant.finance/

# 2. In scope

Commit [05db4b0cafb0bacb7c0c38ff1775560bf4188907](https://github.com/eepdev/frax-stake/tree/05db4b0cafb0bacb7c0c38ff1775560bf4188907)

- Jar_FxsFraxMatic.sol 
- StrategyBase.sol
- StrategyFxsFrax.sol
- StrategyStakingRewardsBase.sol 

## 2.1. Excluded

The smart contract use open source library from Openzeppelin. Following files was excluded from audit:

- @openzeppelin/contracts/token/ERC20/SafeERC20.sol
- @openzeppelin/contracts/math/SafeMath.sol
- @openzeppelin/contracts/token/ERC20/ERC20.sol
- @openzeppelin/contracts/access/Ownable.sol


The next files were not provided to audit:

- IStrategy.sol
- IJar.sol
- IStakingRewards.sol
- IUniswapV2Pair.sol
- IUniswapRouterV2.sol


# 3. Findings
 
No security issues were found in provided files.

# 4. Conclusion

All found issues from previous audit was fixed. The audited smart contract can be deployed.

# 5. Revealing audit reports

Previous audit report: https://gist.github.com/yuriy77k/5be867a77d3ee10dc3cadc9c308e9252

