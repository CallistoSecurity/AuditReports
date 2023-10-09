# 1. Summary

[ELAD Project](https://github.com/ELADnetwork/ELAD-smart-contracts?files=1) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [EladCrowdsale.sol](https://github.com/ELADnetwork/ELAD-smart-contracts/blob/master/EladCrowdsale.sol) github commit hash 2f338beee4b0b8aeb0aa39b54f7a344f9e90cc33.
- [EladToken.sol](https://github.com/ELADnetwork/ELAD-smart-contracts/blob/master/EladToken.sol) github commit hash 2f338beee4b0b8aeb0aa39b54f7a344f9e90cc33.

# 3. Findings

In total, **6 issues** were reported including:

- 3 medium severity issues.

- 3 low severity issues.

## 3.1. Token Crowdsale Allocation.

### Severity: medium

### Description

When the crowdsale is finalized the tokens left can be sent back to the contract owner using `allocateRemainingTokens` function member of `EladCrowdsale` contract, when they should be either burned or used in another sale.
As the Token contract shows, a fixed amount of tokens is allocated to the team, foundation & advisors.

### Code snippet

https://github.com/RideSolo/ELAD-smart-contracts/blob/master/EladCrowdsale.sol#L20#L24

https://github.com/RideSolo/ELAD-smart-contracts/blob/master/EladToken.sol#L16#L32

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/ELAD-smart-contracts/blob/master/EladToken.sol

## 3.3. It is necessary to actualize the start time.

### Severity: low

### Code snippet

* [EladToken.sol, line 12](https://github.com/ELADnetwork/ELAD-smart-contracts/blob/2f338beee4b0b8aeb0aa39b54f7a344f9e90cc33/EladToken.sol#L12)

### Description

The time of start is outdated. It is necessary to actualize this value. Otherwise, the blocking of funds will not work.

### Recommendation

You can add a check of this value to the constructor. Like this `require(startTime > now + 3 days)`.

## 3.4. The duration of lock time may not correspond of reality.

### Severity: low

### Code snippet

* [EladToken.sol, line 57](https://github.com/ELADnetwork/ELAD-smart-contracts/blob/2f338beee4b0b8aeb0aa39b54f7a344f9e90cc33/EladToken.sol#L57)

### Description

Funds of advisor, team and foundation are locked when the contract is deployed, not when the `startTime`. But unlocking is possible only after `startTime + lock.duration` date. `startTime` value is not time when the contract is deployed. Thus, the funds may remain blocked more or less than declared.


## 3.5. It is necessary to allow funds for the crowdsale contract.

### Severity: medium

### Code snippet

* [Crowdsale.sol, line 193](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/6d415c508be94ef8391ed6525df365452466da76/contracts/crowdsale/Crowdsale.sol#L193)

### Description

For working of the token purchase mechanism and for the process to be transparent, it is necessary to allow the contract to spend funds.

### Recommendation

Make like this `_token.safeApprove(this, _token.balanceOf(owner));` in the crowdsale contract constructor.

## 3.6. Incorrect variables names in the `isOpen` method.

### Severity: medium

### Code snippet

* [EladCrowdsale.sol, line 17](https://github.com/ELADnetwork/ELAD-smart-contracts/blob/2f338beee4b0b8aeb0aa39b54f7a344f9e90cc33/EladCrowdsale.sol#L17)

* [TimedCrowdsale.sol, line 57](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/6d415c508be94ef8391ed6525df365452466da76/contracts/crowdsale/validation/TimedCrowdsale.sol#L57)

### Description

There is no `openingTime` and `closingTime` storage variables in these contracts. `TimedCrowdsale.sol` use `_openingTime` and `_closingTime` variable. Also `TimedCrowdsale` contains `isOpen` method and no need to overwrites this. But in this case with using incorrect variables `isOpen` will always return `false` and `buyTokens` method will not work. Because of [modifier](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/6d415c508be94ef8391ed6525df365452466da76/contracts/crowdsale/validation/TimedCrowdsale.sol#L79):

```solidity
function _preValidatePurchase(
  address beneficiary,
  uint256 weiAmount
)
  internal
  onlyWhileOpen
  view
{
  super._preValidatePurchase(beneficiary, weiAmount);
}
```

The compiler should return an error: "Undeclared modifier". Either way, it's a dangerous issue.

### Recommendation

`TimedCrowdsale` contract contains `isOpen` method and no need to overwrites this.


# 4. Conclusion

The contract contains some issues that should be addressed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/e21dbdb85ac50176f986a086349661dd

https://gist.github.com/yuriy77k/60ff13c38aa606e82ec632bffcc92b05

https://gist.github.com/yuriy77k/fa1be2bf3f0610fc04c602690eb2b8c5
