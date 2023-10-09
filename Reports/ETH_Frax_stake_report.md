# Frax-stake Security Audit Report

# 1. Summary

[Frax-stake](https://github.com/eepdev/frax-stake) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The contracts are vaults for Matic Network/Ethereum that take Quickswap/Uniswap LP token deposits and optimize yields on them.

Website: https://adamant.finance/

# 2. In scope

Commit [258285b1783fcc691cb9b1805af3a1ae461c5a22](https://github.com/eepdev/frax-stake/tree/258285b1783fcc691cb9b1805af3a1ae461c5a22)

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

In total, **7 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 3 notes.

- 1 owner privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Unused function `withdraw(IERC20 _asset)`

### Severity: note

### Description

The function [withdraw(IERC20 _asset)](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyBase.sol#L79-L80) can be called from a `Jar` contract only. But a `Jar` contract has no function which calls it.

## 3.3. The owner may transfer all users' tokens to a fake staking contract.

### Severity: owner privileges

### Description

The function [earn()](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/Jar_FxsFraxMatic.sol#L45-L57) allow an owner to transfer all tokens from `Jar` contract to an active staking contract (not often than once per day). Also, the owner has rights to [migrate to a new staking contract](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyStakingRewardsBase.sol#L58-L73).

Therefore owner has the ability to steal users tokens using the following steps:
1. Call the function [earn()](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/Jar_FxsFraxMatic.sol#L45-L57) at least 1 day before migration.
2. Create a fake staking contract that will transfer staked tokens to the owner.
3. [Migrate to new fake staking contract](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyStakingRewardsBase.sol#L58-L73). After it all users tokens will send beck to `Jar` contract from staking.
4. Call the function [earn()](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/Jar_FxsFraxMatic.sol#L45-L57) to transfer all users tokens to a fake staking contract.


## 3.4. Users may restake other people's funds in 1 day

### Severity: medium

### Description

Users who restake own tokens, may call [restake()](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/Jar_FxsFraxMatic.sol#L118) in [1 day](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/Jar_FxsFraxMatic.sol#L126). 
If you want to allow users to restake only own money you have to replace [this](https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/Jar_FxsFraxMatic.sol#L119-L126) code with following :

```Solidity
        uint256 lastTimeMigrated = IStrategy(strategy).getLastTimeMigrated();
        //Don't allow stakers to call this function if they staked after the migration because that would allow the new staker to move other people's funds
        require(lastTimeStaked[msg.sender] < lastTimeMigrated, "Staked after the migration");
        //prevent someone to deposit other people's funds
        require(lastTimeRestaked[msg.sender] < lastTimeMigrated, "User already restaked");

        lastTimeRestaked[msg.sender] = now;
```


## 3.5 Transfer is not the last statement

### Severity: note

### Description
There are no bugs now, but it is potentially dangerous for the re-entrance as some state change may be added.

### Code snippet
https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyStakingRewardsBase.sol#L72

### Recommendation
It is recommended to put transfers to the end of methods.

## 3.6 Event is probably missing

### Severity: low

In method `setJar()` should probably emit an event.

### Code snippet
https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyBase.sol#L70

### Recommendation
It is recommended to create new events.

## 3.7 Use constants

### Severity: note

### Description
In places where you used hardcode addresses, you may use constants to save gas.

### Code snippet
https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyBase.sol#L31
https://github.com/eepdev/frax-stake/blob/258285b1783fcc691cb9b1805af3a1ae461c5a22/StrategyFxsFrax.sol#L10-L12


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract. Investors have to pay attention to owner's right to transfer there tokens to any contract.

# 5. Revealing audit reports

https://gist.github.com/danbogd/43f89d23a8b211c178dd4bfc92147a02

