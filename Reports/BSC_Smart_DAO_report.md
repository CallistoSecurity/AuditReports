# Security Audit Report

# 1. Summary

[SMART token and DAO](https://github.com/jointerinc/smartToken/tree/main/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash: [36eaed2c06e22ca775be758e56e37758f6749238](https://github.com/jointerinc/smartToken/tree/36eaed2c06e22ca775be758e56e37758f6749238)

SMART Token deployed at: https://bscscan.com/address/0xcc1084e76d9473fc6d8838a7bab8f03eca82a21c


# 3. Findings

In total, **4 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 4 low severity issues.

Also, pay attention on **2 notes** were reported including:
  
- 0 notes.

- 2 owner privileges.

No critical security issues were found.


## 3.1. An airdrop recipient can be undeservedly rewarded

### Severity: low

### Description

The reward depends on the duration of staking and on the amount of staking tokens. In the common case when the balance increases then reward is paid and staking starts again with the updated balance and resets staking period.

But in the case of airdrop, there is no staking period reset. This means that the staking start date remains the old one. And if the user stored even minimal funds without moving, for example, for a year, then when receiving an airdrop, in the next block, he can receive an annual reward for stacking the airdrop.

### Code Snippet

* [`mint()`, Increasing balance and `startBlock` reset](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L656-L660)
* [`airdrop()`, Increasing balance without `startBlock` reset](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L421)

## 3.2 The user who received Airdropped tokens should have double tokens amount of last airdrop

### Severity: low

### Description

If there are several airdrops for different amounts then the users who received a smaller quantity of tokens should unlock the account for a doubled amount of the last airdrop.

For example, there are two airdrops of 1 Smart and 3 Smarts for different users. Then users who received 1 Smart should have 6 Smarts(instead of 2 Smarts) to unlock their account.

In addition, blocking the account restricts the user. Not everyone will want to get a lock on their old funds, even for getting an airdrop. Perhaps you should only block an airdrop amount, not all user funds.

### Code snippet

* [`_transfer()`, checking for unlocking](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L634)

* [setting the amount required for unlocking](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L451)

## 3.3. Governance privileges

### Severity: owner privileges

### Description

Ownership of token's contract transfered to the  [Governance](https://bscscan.com/address/0x457c1Bd14aDC03920d09287326fD5fE7a0955758) contract where token holders may vote for:

1. Block tokens transfers from any address for any duration. This functionality design for locking tokens on its wallets when users vote to avoid double voting with the same tokens. But Governance contract may activate this function also by voting.
 [1](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L456) [2](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L622)

2. Mint any amount of token for any address. [link](https://github.com/jointerinc/smartToken/blob/fb69ef918a4b2ef2c5825a4190810032e7accff0/contracts/SmartToken.sol#L660)

## 3.4. Known vulnerabilities of BSC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.5. Funds can be accidentally sent to a zero address

### Severity: low

### Description

There is no check for an empty input value of `_to` parameter in `transferFund` function. It may be empty by mistake and funds will be lost. The owner can lose both erc-20 tokens and ether.

### Code Snippet

* [`transferFund()`](https://github.com/jointerinc/smartToken/blob/36eaed2c06e22ca775be758e56e37758f6749238/contracts/SmartProtocol.sol#L60)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/gorbunovperm/f054bca582994a9b23ca940117b8d122

https://gist.github.com/gorbunovperm/7ebb07e6aaf8cc0af9490a4984cebb1c

## 5.1. Note about the revealed report

The issue [An airdrop recipient can be undeservedly rewarded](https://gist.github.com/gorbunovperm/7ebb07e6aaf8cc0af9490a4984cebb1c#31-an-airdrop-recipient-can-be-undeservedly-rewarded) has low severity since airdrop will be done only to address with zero balance. Even in case if airdrop come to address with some tokens, the amount of airdrop is small enough to give user significant profit.
