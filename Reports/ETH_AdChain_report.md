# AdChain Security Audit Report

# 1. Summary

[AdChain](https://github.com/AdChain/AdChainRegistry/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The goal of the adChain Registry is to provide advertisers with a list of websites that offer high - quality inventory for serving digital ads.

https://publisher.adchain.com/domains

# 2. In scope

Commit hash: `5930cede2d533c8ee92001fd0be8eada2f2bc614`

1. [Registry.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol)
2. [historical/StandardToken.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol)
3. [historical/Token.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Token.sol)
4. [historical/HumanStandardToken.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/HumanStandardToken.sol)
5. [AttributeStore.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/AttributeStore.sol)
6. [Challenge.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Challenge.sol)
7. [DLL.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/DLL.sol)
8. [Migrations.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Migrations.sol)
9. [Parameterizer.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Parameterizer.sol)
10. [PLCRVoting.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/PLCRVoting.sol)
11. [AttributeStore.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/AttributeStore.sol)
12. [historical/Disbursement.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Disbursement.sol)
13. [historical/Filter.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Filter.sol)
14. [historical/Migrations.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Migrations.sol)
15. [historical/Sale.sol](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Sale.sol)

# 3. Findings

In total, **11 issues** were reported including:

- 2 medium severity issues.

- 8 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. ERC20 Compliance: `false` instead of `throw`

### Severity: medium

### Description

From ERC-20 specification:
> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

But in this implementation it just returns `false`. This can lead to serious consequences. Because checking the return value of this function is rare.
For example, external contract may use this token contract as:
```solidity
AdChainToken.transferFrom(recipient, this, value);
points[recipient] += value;
```
In this case recipient can get any value of points, but he may not have enough money and the code will succeed.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol#L25

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol#L37

### Recommendation

> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

## 3.3. Required check for an `0x0` address

### Severity: low

### Description

It is possible to lose tokens by sending to 0x0 address.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol#L15

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol#L28

### Recommendation

Use condition like `require(to != address(0))`.

## 3.4. ERC20 Compliance: zero-value transfers rejecting

### Severity: low

### Description

EIP20 says that:
> Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.
But in this contract, function `transfer` has a condition:
```solidity
if (balances[msg.sender] >= _value && _value > 0) {
    // ...
}
```

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol#L20

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/StandardToken.sol#L31

## 3.5. ERC20 Compliance: event missing

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a `Transfer` event should be emitted.
An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/HumanStandardToken.sol#L44

## 3.6. The relocating is not secure process for investors.

### Severity: note

### Description

The owners can implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Disbursement.sol#L67

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Filter.sol#L37

## 3.7. Multi Transfer arrays length check.

### Severity: low

### Description

There are two/three input arrays, but no check for the same length. In this case it is possible to skip some parameters.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Filter.sol#L22-L35

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Sale.sol#L120

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Sale.sol#L99

## 3.8. Wrong Vote Result

### Severity: medium

### Description

`isPassed` function member of `PLCRVoting` contract is used by multiple function to define if a poll id that ended has passed or not.

A poll is a structure constituted of multiple parameters essential for the voting system success, like `voteQuorum` that is the minimum number of voter required to pass a poll.

However is in `isPassed` function the comparison used to validate if the poll has passed or not do not respect the minimum number of voters even if the quorum parameter is included, for example once the poll has ended and we have:

	voteQuorum = 10
	votesFor = 1
	voteAgainst = 8

`isPassed` will return true since the result will be `100 * 1 > 10 * (1+8)`.
Developers should explain do use of such comparison since first  `voteQuorum` is not respected and `votesFor` and `voteAgainst` are not compared fairly, resulting in a wrong vote result.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/PLCRVoting.sol#L25

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/PLCRVoting.sol#L220

## 3.9. Voters Reward Error

### Severity: low

### Description

When a vote is committed using `commitVote` a `_secretHash` is saved into the voter parameters. The voter reveal the vote option using `revealVote` and inputting `_voteOption` and `_salt` that were used previously to compute the `_secretHash` and commit the vote.

Please note that when the user commit the vote he can use either `_voteOption` equal to 1 to vote for the `_pollID` or any other value to vote against, as we can see [here](https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/PLCRVoting.sol#L160#L163).

However when the reward is distributed using `claimReward` member of `Challenge` library `getNumPassingTokens` function member of `PLCRVoting` contract is called, and the only values that will return the `getNumTokens` are if `_voteOption` was used with a value equal to 0 or 1. The consequences are if a user voted against using value different than zero his vote is counted but he won't get the reward even if the marjority voted against.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/PLCRVoting.sol#L175#L183

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Challenge.sol#L80

## 3.10. Reentrancy Attack

### Severity: low

### Description

If we consider the token contract code is `StandardToken.sol` — it is ok. But considering that we are auditing not production version in the main network we have to assume that the contract of the token may be different. In this case it is theoretically possible the Reentrancy Attack. 

For example when [calling] to double withdrawal because `listing.unstakedDeposit` updated after calling external contract(`token.transfer`).

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L95

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L113

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L133

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L187

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L369

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L403

## 3.11. Underflow and Overflow

### Severity: low

### Description

In all the audited contracts the developers didn't use SafeMath to prevent over or underflow, all the code lines that might raise issues are listed below. Please note that this operation can overflow or underflow only is an error was made mainly by the owner.

### Code snippet

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/historical/Sale.sol#L142

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Challenge.sol#L51

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Challenge.sol#L63

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/PLCRVoting.sol#L220

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Parameterizer.sol#L161

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L201

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Registry.sol#L98

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Parameterizer.sol#L123

https://github.com/AdChain/AdChainRegistry/blob/5930cede2d533c8ee92001fd0be8eada2f2bc614/contracts/Parameterizer.sol#L128

### Recommendation

Use SafeMath.

### Recommendation

All calculations are carried out above the call of external contracts.

# 4. Conclusion

The audited smart contract should not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/bd018ba219aaee55196e3fa486730c83

https://gist.github.com/yuriy77k/36a894ed52edb59ed72376ef96e049f0

https://gist.github.com/yuriy77k/29aff095d44e10cf7087a3e18a58d9d2
