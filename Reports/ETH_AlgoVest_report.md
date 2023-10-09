# AlgoVest High-Yield Investment Pool Security Audit Report

# 1. Summary

[AlgoVest High-Yield Investment Pool](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#readContract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Website: https://algovest.fi
- Twitter: www.twitter.com/AlgoVestAVS
- Telegram: @AlgoVestANN

# 2. In scope

https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#readContract



# 3. Findings

In total, **4 issues** were reported, including:

- 2 high severity issues.

- 2 medium severity issues.

- 0 low severity issues.

In total, **5 notes** were reported, including:

- 2 notes.

- 3 owner privileges.


## 3.1. Incorrect requirement

### Severity: medium

### Description

In the function [depositAvsToken](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L105) there is requirement:
```Solidity
    require(
        _amount > stakingAmount,
        "Amount cannot greater than stakingAmount"
    );
```

This means that `_amount` MUST be greater than `stakingAmount`, but the message says the opposite condition.

Please, check what is correct: message or condition?


## 3.2. User may lose previously staked tokens

### Severity: high

### Description

In the function [depositAvsToken](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L111) new staked amount replace previously staked amount: `stakeInfo.amount = _amount;`. It means if user will do several staking, only last staking amount will be recorded and all previously staked money will be lost by user.

### Recommendation

Add new stake amount to already stored value: `stakeInfo.amount = stakeInfo.amount.add(_amount);`

## 3.3. Wrong error messages

### Severity: note

### Description

1. In the function [withdrawAvsToken](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L132) there is requirement:
```Solidity
        require(
            rewardsPending <= rewardAvsCoinBalance,
            "Amount greater than pending rewards."
        );
```

The message: "Amount greater than pending rewards." does not according to condition `rewardsPending <= rewardAvsCoinBalance`.
You may change message to "Pending rewards is greater than available reward balance".

2. In the function [depositToPool](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L150) there is requirement:
```Solidity
        require(
            pool.minDeposit <= _amount,
            "Deposit higher than minimum amount"
        );
```

The message: "Deposit higher than minimum amount" does not according to condition `pool.minDeposit <= _amount`. You may change message to "Amount is less than minimum amount".

## 3.4. Possible reentrance attack

### Severity: medium

### Description

Depends on functionality of `stablecoin` and `avscoin` may be possible reentrance attack to the functions [withdrawAvsToken](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L115), [withdrawPrinciple](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L163).

### Recommendation

Add `nonReentrant` modifier to those functions.

## 3.5. Incorrect calculation of interest to claim

### Severity: high

### Description

In the function [claimInterest](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L205) the expression `bond.currentNonce - oldNonce` means how many `noncePeriod` passed since last claim interest. The more nonce periods passed the more interest user should receive.

But in this code:
```Solidity
        IERC20(stablecoin).transfer(
            msg.sender,
            bond.pendingInterest.div(bond.currentNonce - oldNonce)
        );
```
the calculation is opposite: the less nonce periods passed the more interest user receive. If 1 nonce period was passed user will receive entire amount of `bond.pendingInterest`.


## 3.6. Owner privileges

### Severity: owner privileges

### Description

Owner of smart contract has right to:
1. Withdraw `stablecoin`, deposited by users, form contract using function `withdrawToTradingAdmin`;
2. Deposit `stablecoin` to contract using functions `depositAdmin` to return previously withdrawn `stablecoin` for trading and `depositStableCoin` to add `stablecoin` for paying interest.
3. Change `noncePeriod` to any value at any time using function `changeNoncePeriod`. It will make an effect on interest payment for already deposited `stablecoin` into pools.
### Recommendation

## 3.7. Bonds is not transferable

### Severity: note

### Description

When users deposits `stablecoin` to pools they receive NFT tokens (bonds). But users will not be able to [transfer](https://rinkeby.etherscan.io/address/0xE57CD24a8C7F1B158C994BeDf8415E232B9E461F#code#F1#L234) bonds to another address (user). They can only use it to get interest from contract.


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

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
