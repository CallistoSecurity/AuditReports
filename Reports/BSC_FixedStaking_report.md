# Fixed Staking Contract Security Audit Report

# 1. Summary

[Fixed Staking](https://github.com/giopaul/defipii/blob/main/staking.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Smart contract is for staking purposes like 1,3,6,12 months locked staking. User will be able to stake the tokens before 'stakeStartDate' and unstaked tokens after 'stakeEndDate'. Reward percentage is calculated on monthly basis, but automatically it will multiple with (stakeEndDate - stakeStartDate )

# 2. In scope

Commit `96b34419410e54feb15937d9ded46cc8556723ed`

- [staking.sol](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol)

# 3. Findings

In total, **6 issues** were reported, including:

- 2 high severity issues.

- 1 medium severity issues.

- 2 low severity issues.

In total, **6 notes** were reported, including:

- 1 notes.

- 5 owner privileges.

## 3.1. Not effective `totalStakes` calculation

### Severity: low

### Description

To calculate `totalStakes` [nested loops](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L359-L362) are used. In case of calling this function on-chain from other contract it will use a lot of gas. The transaction even may be failed due to OUT_OF_GAS exception in case of big number of stakes. 

### Recommendation

Try to avoid loops, especially nested loops.

To fix this issue you have to create global variable `uint public totalStakes;` and add/subtract amount each time when somebody stake/unstake.


## 3.2. High gas consumption with risk to get OUT_OF_GAS exception

### Severity: medium

### Description

The function [isStakeholder()](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L366-L372) uses `for` loop to find stakeholder index. It require a lot of gas and may cause abort transaction due to OUT_OF_GAS exception in case of big number of stakeholders.

### Recommendation

Try to avoid loops.

Create `mapping(address => uint) public stakeholderIndex;` where index is 1-based.

Replace [isStakeholder()](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L366-L372) and [removeStakeholder](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L380-L386) with:

```Solidity
    function isStakeholder(address _address) public view returns(bool, uint256) {
        uint s = stakeholderIndex[_address]; // 1-based index
        if (s == 0) return (false, 0);
        return (true, s - 1); // return 0-based index
    }

    function removeStakeholder(address _stakeholder) internal {
        (bool _isStakeholder, uint256 s) = isStakeholder(_stakeholder);
        if (_isStakeholder) {
            delete stakeholderIndex[_stakeholder];
            address lastStakeholder = stakeholders[stakeholders.length - 1];
            stakeholderIndex[lastStakeholder] = s+1; // 1-based index
            stakeholders[s] = lastStakeholder;
            stakeholders.pop();
        }
    }    
```


## 3.3. Wrong require condition in the function `stake()`

### Severity: high

### Description

The [require((block.timestamp > stakeStartDate) , "Staking Pool is locked for fixed period!");](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L389) is incorrect.

If you want to allow to users to stake before `stakeStartDate` you have to use:
```Solidity
require(block.timestamp < stakeStartDate, "Staking Pool is locked for fixed period!");
```

## 3.4. Wrong require condition in the function `unstake()`

### Severity: high

### Description

In the function `unstake()` you allow to users to ustake [only before](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L401) `stakeEndDate` if user will not unstake before that date their tokens will be locked forever.

### Recommendation

The users should be able unstake only after `stakeEndDate` therefore replace [requirement](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L401) with this:
```Solidity
require(block.timestamp > stakeEndDate , "Staking Pool will be unlocked after fixed period!");
```

## 3.5. Variables have no getter functions

### Severity: low

### Description

To be able read variable values from outside of contract you have to declare [these variables](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L315-L328) as `public`.



## 3.6. MIN and MAX stake amounts

### Severity: note

### Description

The staking token contract was not reviewed and we don't know the decimals of staking tokens. 

The owner should pay attention to the [MIN_STAKE_AMOUNT](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L315) and [MAX_STAKE_AMOUNT](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L316) values. It may be too small if staking tokens has big decimals.


## 3.7. Owner privileges

### Severity: owner privileges

### Description

The contract owner has right to:
1. Change reward percent to any amount at any time using function [changeRewardPercent](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L343-L345).
2. Change stake start date to any date at any time using function [changeStakeStartDate](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L348-L350).
3. Change stake end date to any date at any time using function [changeStakeEndDate](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L353-L355).
4. Add tokens to contract balance at any time using function [sendTokens](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L418-L421).
5. Withdraw tokens from contract balance at any time using function [withdrawTokens](https://github.com/giopaul/defipii/blob/96b34419410e54feb15937d9ded46cc8556723ed/staking.sol#L423-L426).

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

Users should note that the reward will only be paid if the owner has added tokens to the contract balance. Otherwise, the reward will be paid from the staked tokens of other users, like in a Ponzi scheme. If there are not enough tokens, the user will receive nothing at all. 

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

