# PandAIEarnV1 Security Audit Report

# 1. Summary

[PandAI Earn](https://github.com/pandai-corp/pandai-earn-sc/tree/bce9e71647a72bdf0704cb09787c0baf267e082f) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing/)


# 2. In scope

Commit: bce9e71647a72bdf0704cb09787c0baf267e082f

- [PandAI.sol](https://github.com/pandai-corp/pandai-earn-sc/blob/bce9e71647a72bdf0704cb09787c0baf267e082f/contracts/PandAI.sol)
- [PandAIEarnV1.sol](https://github.com/pandai-corp/pandai-earn-sc/blob/bce9e71647a72bdf0704cb09787c0baf267e082f/contracts/PandAIEarnV1.sol)
- [USDT.sol](https://github.com/pandai-corp/pandai-earn-sc/blob/bce9e71647a72bdf0704cb09787c0baf267e082f/contracts/USDT.sol)

# 3. Findings

In total, **0 issues** were reported, including:

- 0 critical severity issues.

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **9 notes** were reported, including:

- 3 notes.

- 6 owner privileges.

No critical security issues were found.


## 3.1. Incorrect comparison in function canClaim()

### Severity: note

### Description

For an uninitialized `ApprovalLevel` of a wallet, the wallet must be allowed to claim $1000 as a daily limit. The statement incorrectly checks the `claimUsdt` value to `DAILY_CLAIM_LIMIT`.

### Code Snippet
- https://github.com/pandai-corp/pandai-earn-sc/blob/bce9e71647a72bdf0704cb09787c0baf267e082f/contracts/PandAIEarnV1.sol#L366

### Recommendation
Consider updating the following statement in the function `canClaim()`

```solidity
claimUsdt / (10 ** usdtToken.decimals()) < DAILY_CLAIM_LIMIT
```

to

```solidity
claimUsdt / (10 ** usdtToken.decimals()) <= DAILY_CLAIM_LIMIT
```

## 3.2. Owner Privileges

### Severity: owner privileges

### Description

1. Contract PandAIEarn inherits the traits of OpenZeppelin [AccessControl](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) contract allowing admin to manage admin's, the role could be renounced leading to locking out of access to critical functions of the contract.
2. Owner is entirely responsible for maintaining an adequate balance on the contract for users' withdrawals and rewards payouts. Otherwise, users will not be able to withdraw or claim.
3. Function `setLpAddress()` allows the admin to change the liquidity pool address to any wallet. The value of PandAi tokens would be affected, which is computed using the function `getPandaiWorthOf()` based on the amount of USDT tokens and PandAi tokens available in `newLpAddress` wallet.
4. Function `withdrawTreasury()` allows the admin to withdraw USDT tokens from the contract to the admin address.
5. Function `setUserApprovalLevel()` allows admin to change the claim status of any wallet address to `NotApproved`, `Approved`, and `Forbidden`, limiting the user's ability to withdraw or restrict a user from withdrawing USDT tokens deposited in the contract based on the approval status.
6. Functions `pause()` and `unpause()` allows the admin to disable or enable the ability for the user's deposit and claim USDT tokens, respectively.


### Recommendation

Since the owner has unlimited rights to do everything, the ownership must be transferred to a multi-sig contract.

## 3.3. Follow good coding practice

### Severity: note

### Description

1. Unorganized and non-standardized docstrings

The contracts in the code base contain unorganized and non-standardized docstring does not explain the contract's state and functionalities in detail. This hinders reviewers' understanding of the code's intention, which is fundamental to correctly assessing security and correctness. Additionally, detailed docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts' public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

## 3.4 Struct packing not only limited to the Tier Struct

## Severity: note

### Description:

The team saved gas by packing together the variables in the Tier struct, but that was meant to be an example as every struct can have that optimization and can save gas in the multiples of 20,000.

All the pending reward variables can be made into `uint128` as `uint256` depicts a value that will be practically not achieved. Moreover, all the timestamp variables can be packed in `uint64` as timestamps can be depicted in `uint64` for many upcoming years.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is withdrawn in a short period until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native tokens deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._


# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

Users should pay attention to the owner's responsibility to maintain an adequate balance on the contract for users' withdrawals and rewards payouts. Otherwise, users will not be able to withdraw or claim.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

### Previous report 

- https://gist.github.com/yuriy77k/4e94c1e8ddfe8620123d3aac80999020

### Re-audit reports

- https://gist.github.com/chhajershrenik/0c1c678c62a30f51d1786b0d347c5680
- https://gist.github.com/SakshamGuruji3/f5418a6340e7ae766e406536ff0a58e4
