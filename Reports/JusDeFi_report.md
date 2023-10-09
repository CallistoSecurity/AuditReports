# JusDeFi Security Audit Report

# 1. Summary

[JusDeFi](https://www.jusde.fi/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

JusDeFi is a next-generation yield farming experiment, combining some of the most exciting features from today’s biggest players in DeFi with revolutionary new tokenomics. Participants can stake JDFI tokens directly (or to stake LP for even larger rewards) and our unique fee structure powers the burn and buyback systems which in turn drive price action. Whenever you unstake your JDFI, a fee will be charged. Half goes into the staking rewards pool, and the other half is burned. This unstaking fee is dynamic, and changes weekly based on community governance.

# 2. In scope

Commit hash [04ec75af9dd802e9f56f64b1b37b89c7e21a4480](https://github.com/jusdefi/jusdefi/tree/04ec75af9dd802e9f56f64b1b37b89c7e21a4480/contracts)

Include: 

- AirdropToken.sol
- DevStakingPool.sol 
- FeePool.sol
- JDFIStakingPool.sol
- JusDeFi.sol
- StakingPool.sol
- UNIV2StakingPool.sol
- interfaces/IJDFIStakingPool.sol 
- interfaces/IJusDeFi.sol
- interfaces/IStakingPool.sol

Exclude:

- test/JusDeFiMock.sol
- test/StakingPoolMock.sol

# 3. Findings

In total, **1 issues** were reported including:

- 1 notes.


## 3.1. Trusted addresses

### Severity: note

### Description

The function [transferFrom](https://github.com/jusdefi/jusdefi/blob/04ec75af9dd802e9f56f64b1b37b89c7e21a4480/contracts/JusDeFi.sol#L119) in `JusDeFi` allow transferring from any address without allowance if `msg.sender` is in the whitelist. Two of three whitelisted addresses are `JDFIStakingPool` and `UNIV2StakingPool` which is part of the codebase, but the third address `uniswapRouter` sets in the constructor and is an external contract which is not part of the provided codebase. You have to be sure that `uniswapRouter` passed the security audits and have no issues.

# 4. Conclusion

In the audited smart contracts, there were not found security issues.  
The only note: require to check the address of `uniswapRouter` which will be used in contract deployment, to be a real address of Uniswap Router.

