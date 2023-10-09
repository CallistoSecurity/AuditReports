# 1. Summary

[RapidProfit](https://github.com/vpomo/RapidProfit/) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ContractStakeToken.sol](https://github.com/vpomo/RapidProfit/blob/master/contracts/ContractStakeToken.sol) github commit hash a8194047efeb80b364b89374c54143fc56b7f85a.

- [ERC20Token.sol](https://github.com/vpomo/RapidProfit/blob/master/contracts/ERC20Token.sol) github commit hash bbb10f4db84b346e0fcac37b38ee58bb581d9826.

- [RapidProfit.sol](https://github.com/vpomo/RapidProfit/blob/master/contracts/RapidProfit.sol) github commit hash a8194047efeb80b364b89374c54143fc56b7f85a.


# 3. Findings

In total, **9 issues** were reported including:

- 1 high severity issues.

- 2 medium severity issues.

- 5 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Contract Destruction

### Severity: medium

### Description

`removeContract` member of `RapidProfit` contract, call `selfdestruct`. 
`selfdestruct` will send the ether balance of the contract to the owner. However, it doesn't send the token balances back to their stakers. 
Users's assests should always remain safe when interacting with such Dapps.

### Code snippet

https://github.com/vpomo/RapidProfit/blob/master/contracts/RapidProfit.sol#L202#L204

## 3.2. Token Withdrawal

### Severity: medium

### Description

`withdrawOwnerToken` member of `RapidProfit` contract allows the owner to withdraw stakers tokens since the  
contract hold both stakers stakes and reward to be distributed.
The developers should keep track of the total reward amount to be distributed and not allow any withdrawal higher than it, direct withdrawal from the token balance of the contract is a risk for the stakers.

Same issue when transfering stakes and reward back to the stakers using `withdrawToken` member of `RapidProfit` contract, the reward should be given from the total reward balance allocated to the contract, otherwise the reward will be given from other users balances.

If the developers do not plan to set up a reward fund, this dapp will be considred as a Ponzi scheme.

### Code snippet

https://github.com/vpomo/RapidProfit/blob/master/contracts/RapidProfit.sol#L251#L255

https://github.com/vpomo/RapidProfit/blob/master/contracts/RapidProfit.sol#L218#L225
    
## 3.3. Additional Requirement

### Severity: Low

### Description

The value of `_stakeType` has to be checked before executing `depositToken` memeber of `ContractStakeToken`, in case of wrong parameter input the users will automatically get the 1 day staking reward.

### Code snippet

https://github.com/vpomo/RapidProfit/blob/master/contracts/ContractStakeToken.sol#L142#L162

## 3.4. Stake Requests Cleaning 

### Severity: Low

### Description

`withdrawToken` function member of `ContractStakeToken` call `clearTransferInsToken` function as shown below:

```
        while (clearTransferInsToken(_address) == false) {
            clearTransferInsToken(_address);
        }
```
the return value of `clearTransferInsToken` should be assigned inside the while loop, and checked in the condition:

```
	bool value_returned = false;
        while (value_returned == false) {
            value_returned = clearTransferInsToken(_address);
        }
```

And another remark is why `clearTransferInsToken` returns false, when one of the user stakes has reached the minimum staking period.
The for loop should keep checking if there is any stake request that was completed until the end of the array and return true at the end.

### Code snippet

https://github.com/vpomo/RapidProfit/blob/master/contracts/ContractStakeToken.sol#L210#L223

https://github.com/vpomo/RapidProfit/blob/master/contracts/ContractStakeToken.sol#L225#L234

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Code snippet

* [ERC20Token.sol, line 154](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/ERC20Token.sol#L154)

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.6 Anyone can change the status of stakes to `COMPLETED` and `isRipe` to `true` for any investor.

### Severity: high

### Code snippet

* [ContractStakeToken.sol, line 204-205](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/ContractStakeToken.sol#L204-L205)

* [RapidProfit.sol, line 167](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/RapidProfit.sol#L167)

### Description

`validWithdrawToken` function has no access restrictions and it is possible to use as arguments any staking address and any value as current time. In this case an attacker can distort contract work by changing values of `arrayStakesToken[currentStakeIndex].status` and `transferInsToken[_address][i].isRipe` variables. For example, an investor will [lose his funds](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/ContractStakeToken.sol#L227-L228) in the withdrawal process after this attack.

### Recommendation

Use function restrictions and check input variables.

## 3.7. Anyone can start withdrawal process instead of owner of stake.

### Severity: low

### Code snippet

* [RapidProfit.sol, line 218-219](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/RapidProfit.sol#L218-L219)

### Description

Anyone can call `withdrawToken` with another investors address. Anyone should not have this right.

### Recommendation

Use permissions for this function.

## 3.8 Someone may harm by initiating deposit with incorrect type of stake or value.

### Severity: low

### Code snippet

* [RapidProfit.sol, line 155-158](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/RapidProfit.sol#L155-L158)

### Description

Anyone can call `depositToken` with investor address if the investor has allowed tokens to transfer to RapidProfit contract. Someone can get ahead the investor in this action and spoil his plans.

### Recommendation

Use permissions for this function.

## 3.9. Test values should be deleted. 

### Severity: minor

### Description

In line [213](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/ContractStakeToken.sol#L213) there left test value, that will cause the wrong work of project.


# 4. Conclusion

These contracts have critical issues. The developers should fix the above mentioned issues before deployment. The audited contracts are not safe for stakers.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/d4d3c8cd0718cf4d46698d71802f3db7

https://gist.github.com/yuriy77k/c19e4adc1129e768c0f0a8c878dada06

https://gist.github.com/yuriy77k/52bb179e86133fe5e5b0511f71a059e0
