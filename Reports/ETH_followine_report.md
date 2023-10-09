# 1. Summary

[Followine](https://github.com/followine/Daico) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Authorizable.sol](https://github.com/followine/Daico/blob/master/Authorizable.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [BasicToken.sol](https://github.com/followine/Daico/blob/master/BasicToken.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [BurnToken.sol](https://github.com/followine/Daico/blob/master/BurnToken.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [ERC20Basic.sol](https://github.com/followine/Daico/blob/master/ERC20Basic.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [FiatContract.sol](https://github.com/followine/Daico/blob/master/FiatContract.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [HumanStandardToken.sol](https://github.com/followine/Daico/blob/master/HumanStandardToken.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [OriginToken.sol](https://github.com/followine/Daico/blob/master/OriginToken.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [Ownable.sol](https://github.com/followine/Daico/blob/master/Ownable.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [SafeMath.sol](https://github.com/followine/Daico/blob/master/SafeMath.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [StandardToken.sol](https://github.com/followine/Daico/blob/master/StandardToken.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [StartToken.sol](https://github.com/followine/Daico/blob/master/StartToken.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [Voter.sol](https://github.com/followine/Daico/blob/master/Voter.sol) github commit hash 237f149d34727847d0623df7662c79a5d429b4a9.
- [VoterInterface.sol](https://github.com/followine/Daico/blob/master/VoterInterface.sol) github commit hash ec86c9bc026bd691fecce196c43f9bd6473466e9.
- [WINE.sol](https://github.com/followine/Daico/blob/master/WINE.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [erc20.sol](https://github.com/followine/Daico/blob/master/erc20.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [interfaceProposal.sol](https://github.com/followine/Daico/blob/master/interfaceProposal.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [proposal.sol](https://github.com/followine/Daico/blob/master/proposal.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.
- [startable.sol](https://github.com/followine/Daico/blob/master/startable.sol) github commit hash 8fc6c6f3cabcf522d3ca081c4843dabfc028e19c.

# 3. Findings

In total, **8 issues** were reported including:

- 1 high severity issues.

- 3 medium severity issues.

- 4 low severity issues.

## 3.1. Block Gas Limit

### Severity: high

### Description

Each time that a user receives token through `transfer`, `transferCustom` and`transferFrom` functions member of `startToken`, the transfer info is pushed to `log` mapping state variable.

If the `log` mapping length for an address X is high enough, `getFreeCoin` function may throw the execution of any of the above mentioned function since it contains a loop which may cause an out of gas or a gas limit reached, making users enable to transfer their tokens.

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L131

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L142

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L149

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L19

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L24

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L33-L35

## 3.2. Origin Transfer

### Severity: medium

### Description

`OriginToken` contract implement two functions that represent a risk on users, if someone create a contract and succeed to pass the address of the contract as an authorized address, users can be deceived by making a transaction to the created contract allowing it to withdraw their tokens or burn them since `tx.origin` is used instead of `msg.sender`.

The contract developers didn't explain the logic behind adding such functions.

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/OriginToken.sol#L12

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/OriginToken.sol#L20

## 3.3. Wrong Computation

### Severity: medium

### Description

The category 8 condition on `getFreeCoin` function member of `startToken` contract, should compute the number of months and for each month the user should get 5% of his original amount. However, the number of months is wrong since it divide it by an extra 6, making the whole vesting time for that category equal to 10 years.

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L95

## 3.4. Burn Logic

### Severity: low

### Description

`burnFunction` member of `BurnToken` contract check if the `_burner` address is different than `tx.origin` to execute `deleteCoin` inherited from `StartToken`, which means that only `burnFrom` function will execute `deleteCoin` since the `_from` will be different than `tx.origin` that initiated the chain.
However, a normal address when calling `burn` will not execute `deleteCoin`. 

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/BurnToken.sol#L38

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/BurnToken.sol#L19

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/BurnToken.sol#L47

## 3.5. Truncated Values

### Severity: medium

### Description

When logging the refund value on the fallback function, the euro value is calculated but its value is truncated twice first the token value is divided by `10 ** uint256(decimals)` then is is divided by 100 as last operation , since the token decimals are too small this kind of operation affect in a considerable way.

Also when computing the refund, as shown below:

```
uint256 ethTT = refundLog[msg.sender][i].token / 10 ** uint(decimals);
uint256 ethT = (refundLog[msg.sender][i].etherReceived).div(ethTT * 10 ** uint(decimals));
```

the value of refundLog[msg.sender][i].token is first divided by the decimals to get ethTT then the same ethTT is multiplied again by the same value that was divided by.

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L133

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L134

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L146

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L146

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L159

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L172

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L185

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L198

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L217

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L352#353

## 3.6. Burnout & Burn

### Severity: low

### Description

`burnout` function member of `WINE` contract do not override `burn` function inherited from `BurnToken`, even if `burnout` require `burnoutActive` state variable to be true, `burn` or `BurnFrom`function can still be called to burn tokens in the opposite case.

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/WINE.sol#L312

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/BurnToken.sol#L38

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/BurnToken.sol#L47

## 3.7. UnderFlow

### Severity: low

### Description

Following the vesting logic implemented on `StartToken` contract, the following line present a possible underflow issue in function `getFreeCoin` allowing the users to withdraw more than what the vesting logic allow:

`totalValue = totalValue - transferredCoin[field];`

Good coding patterns, when using Solidity, is using safemath will avoid thinking about such issues.

### Code snippet

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L106

https://github.com/followine/Daico/blob/8d3f3c362d790ef404146f1a89168913d9559534/StartToken.sol#L142

## 3.8. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 4. Conclusion

The audited contracts are not safe, all the highlighted issues should be fixed before deployment. We highly recommend to complete bugs bounty program.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/86f46b335cdf1dba457827a058cd8a6e

https://gist.github.com/yuriy77k/ff13dddb28b9f12bce9a77a1981ac5c7

https://gist.github.com/yuriy77k/37e757d86324399e56be1a4c82c3111a
