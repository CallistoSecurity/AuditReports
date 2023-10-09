# 1. Summary

[Caelum](https://github.com/caelumproject/Caelum-Token/tree/master/contracts/src) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Caelum is build on top of the EIP918 mineable protocol and is a hybrid pow/masternode system.

# 2. In scope

- [EIP918Interface.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/interfaces/EIP918Interface.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [ERC20Interface.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/interfaces/ERC20Interface.sol) github commit hash 60136ec72492a487fbb7f33de1e39a88383dab48.
- [ICaelumMasternode.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/interfaces/ICaelumMasternode.sol) github commit hash c26289358af85a9692ff1c8551ef4c9ef9b0c13b.
- [ICaelumMiner.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/interfaces/ICaelumMiner.sol) github commit hash 60136ec72492a487fbb7f33de1e39a88383dab48.
- [ICaelumToken.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/interfaces/ICaelumToken.sol) github commit hash 60136ec72492a487fbb7f33de1e39a88383dab48.
- [IRemoteFunctions.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/interfaces/IRemoteFunctions.sol) github commit hash c26289358af85a9692ff1c8551ef4c9ef9b0c13b.
- [BasicToken.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/BasicToken.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [ERC20.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/ERC20.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [ERC20Basic.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/ERC20Basic.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [ExtendedMath.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/ExtendedMath.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [Ownable.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/Ownable.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [SafeMath.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/SafeMath.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [StandardToken.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/libs/StandardToken.sol) github commit hash e876a733f18d756c7bc0415e69c560ae36c50b99.
- [InterfaceContracts.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/InterfaceContracts.sol) github commit hash 0103261b879ef74322656a37caf610b714dd6dd4.
- [CaelumAcceptERC20.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/token/CaelumAcceptERC20.sol) github commit hash c26289358af85a9692ff1c8551ef4c9ef9b0c13b.
- [CaelumToken.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/token/CaelumToken.sol) github commit hash aebb69f7eacda320ce84cfdc3761aaf7ed570474.
- [tokenToSwap.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/token/tokenToSwap.sol) github commit hash 26cbb06d5426fef8180764fac038c7fca3caad70.
- [tokenToSwap2.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/token/tokenToSwap2.sol) github commit hash 26cbb06d5426fef8180764fac038c7fca3caad70.
- [AbstractERC918.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/miner/AbstractERC918.sol) github commit hash c26289358af85a9692ff1c8551ef4c9ef9b0c13b.
- [CaelumMiner.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/miner/CaelumMiner.sol) github commit hash 8bd04a5742bbde69990225f80327b1291a5c2b52.
- [CaelumMasternode.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/masternode/CaelumMasternode.sol) github commit hash b2f906c1b2c912cd986e8e7ac79e0b3e5eb2d547.
- [CaelumVotings.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/contracts/CaelumVotings.sol) github commit hash c26289358af85a9692ff1c8551ef4c9ef9b0c13b.
- [CaelumAbstractMiner.sol](https://github.com/caelumproject/Caelum-Token/blob/master/contracts/src/miner/CaelumAbstractMiner.sol) github commit hash 9ed453f8b5c46597cef3d0b729f8707849848929.


# 3. Findings

In total, **13 issues** were reported including:

- 3 high severity issues.

- 3 medium severity issues.

- 6 low severity issues.

- 1 minor observation.

## 3.1. Collateral Withdrawal

### Severity: high

### Description

1. There is no any restrictions at `withdrawCollateral` function. Anyone can get collateral funds as long as there are funds on the contract address.
2. There is possible of an underflow attack here:
```solidity
tokens[token][msg.sender] = tokens[token][msg.sender] - amount;
```
3. If in the future it will be possible to change the `requiredAmount` variable then the user will not receive the correct amount of funds:
```solidity
require(amount == getAcceptedTokenAmount(token)); // The amount needs to match our set amount, allow only one withdrawal at a time.
uint amountToWithdraw = amount;
``` 

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/token/CaelumAcceptERC20.sol#L130-L141

### Recommendation

Make a check on the belonging the user to the the masternode owners and check the funds that deposited by him, use SafeMath.

```solidity
tokens[token][msg.sender] = tokens[token][msg.sender].sub(amount);
```

## 3.2. It is possible to vote for a non-existent proposal. 

### Severity: low

### Description

There is possible to vote for a proposal with an id equal to total number of proposals (`proposalCounter`). But a last proposal id is `proposalCounter - 1`.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L349

### Recommendation

Use `proposalID < proposalCounter` in this case.

## 3.3. One voter can vote as much times as he wants.

### Severity: high

### Description

The `voteProposal` function has protection for re-vote: `voterProposals[proposalID] != msg.sender`. But it won't work because further in the code we see the change of this variable: `voterProposals[proposalID] = msg.sender`. Thus, after someone else votes, a malicious voter can register (`becomeVoter()`) and vote again.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L350

### Recommendation

Mark the voters who voted like this: `voterProposals[proposalID][msg.sender] = true`.

## 3.4. A wrong implementation of the functions `becomeVoter`, `VoteForVotingContract()` and `VoteForMiningContract()`.

### Severity: medium

### Description

* There is should be `require(voterMap[msg.sender].isVoter == false)` instead a `=` statement.

* In combination with issue 3.3, it is possible to increase the `votersCount` by the same user several times.

* After one proposal is completed and another proposal starts, the `votersCount` is not reset. It is constantly increasing.

* The `VoteForVotingContract()` function changes the value of the `_contract_miner`, but it should change the `_contract_voting` variable. And the same with the `VoteForMiningContract()` function.

All of these points violate the normal operation of the voting.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L332-L338

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L148

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L170

### Recommendation

Change contents of these functions.

## 3.5. It is possible to increase the minimum number of the voters, but there is no way to reduce.

### Severity: low

### Description

It is possible to increase the minimum number of the voters, but there is no way to reduce.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L223-L224

### Recommendation

For example, you may use this condition: `require(_total > 10)`;

## 3.6. Before using external contracts in voting, these contracts should be audited.

### Severity: minor observation

### Description

It's just a reminder, but it's an important point. This contract contains several call to external contracts. And it can be dangerous if you don't know what opportunities the external contract has.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L148

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L155

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L162

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L170

### Recommendation

Make an security audit of each external contract before each voting.

## 3.7. Reward Distribution

### Severity: high

### Description

`_reward` function member of `CaelumMiner` contract distribute the reward to the miners, however the given reward is fixed to `1*1e8` since `rewardExternal` is called inside the function with that same value.
The mining reward should be equal to the output of `masternodeInterface.rewardsProofOfWork()` (`_pow` local variable).

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/miner/CaelumMiner.sol#L83

## 3.8. Masternode Candidate Selection

### Severity: medium

### Description

The masternode selection logic do not allow the selection of the last masternode in the list (since it skips it each time when its turn reach.) (check function `setMasternodeCandidate`).

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/masternode/CaelumAbstractMasternode.sol#L143#L146

### Recommendation

Change line 143 to
```
if (masternodeCandidate == masternodeIDcounter) {
```

## 3.9. Modifier Error

### Severity: medium

### Description

`CaelumModifierAbstract` contract defined in `CaelumVotings.sol` file, the contract implements two modifiers `onlyVotingOrOwner` and `onlyVotingContract`, following the naming we can clearly see that `onlyVotingContract` should not allow contract owner to access when it does directly.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L105

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L110

## 3.10. State Variable Setup.

### Severity: low

### Description

`getDataFromContract` function member of `CaelumMiner` contract is more like a setup of the contract and should only be called at contract deployment phase since it sets most of the important contract state variables, there is no reason why the contract owner have access to such function after the contract is deployed and operable by miners.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/miner/CaelumMiner.sol#L140

### Recommendation

`getDataFromContract` should be marked as internal and called only at the deployment phase.

## 3.11. Wrong Assignments

### Severity: low

### Description

- `setVotingContract` function member of `CaelumModifierAbstract` contract is not setting the right state variable address. `_contract_masternode` is set to the inputted value rather than `_contract_voting`.

### Code snippet

https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L140

## 3.12. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.13. No zero address checking

### Severity: low

### Description

In [constructor](https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L44), [setMiningContract](https://github.com/caelumproject/Caelum-Token/blob/50987424815f1b8fca7b5c5604f9f3c7c668beff/contracts/src/contracts/CaelumVotings.sol#L120) there are no zero address checking.

### Recommendation

Check for zero address
```solidity
require(contract != address(0));
```

# 4. Conclusion

The audited contracts are proved to be exploitable by attackers and multiple logics are implemented badly, the contracts are not safe for deployment. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/cca8e9a1a8a257959c05c479e2a4f8e6

https://gist.github.com/yuriy77k/1e0f406999e94e276e8183b97fabee83

https://gist.github.com/yuriy77k/5421c301dba2f6cd0d679c6fa140d6f8

