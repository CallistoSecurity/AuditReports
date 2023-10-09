# Fixed Staking Contract Security v.2. Audit Report

# 1. Summary

[Fixed Staking](https://github.com/giopaul/staking/blob/main/staking.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Smart contract is for staking purposes like 1,3,6,12 months locked staking. User will be able to stake the tokens before 'stakeStartDate' and unstaked tokens after 'stakeEndDate'. Reward percentage is calculated on monthly basis, but automatically it will multiple with (stakeEndDate - stakeStartDate )

# 2. In scope

Commit `907d2c01265232237d9378f0484637c9cd86cbd4`

- [staking.sol](https://github.com/giopaul/staking/blob/907d2c01265232237d9378f0484637c9cd86cbd4/staking.sol)

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **5 notes** were reported, including:

- 0 notes.

- 5 owner privileges.


## 3.1. Owner privileges

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

The audited smart contract can be deployed. No security issues were found during the audit. Users should pay attention to the owner privileges.

Users should note that the reward will only be paid if the owner has added tokens to the contract balance. Otherwise, the reward will be paid from the staked tokens of other users, like in a Ponzi scheme. If there are not enough tokens, the user will receive nothing at all. 

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

