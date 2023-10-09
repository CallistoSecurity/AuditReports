# AlgoVest High-Yield Investment Pool v2. Security Audit Report

# 1. Summary

[AlgoVest High-Yield Investment Pool](https://github.com/grape404/FixedInterestBond/blob/341e29d74a1191b60583fa99d1693656fa2526da/contracts/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Website: https://algovest.fi
- Twitter: www.twitter.com/AlgoVestAVS
- Telegram: @AlgoVestANN

# 2. In scope

Commit [341e29d74a1191b60583fa99d1693656fa2526da](https://github.com/grape404/FixedInterestBond/blob/341e29d74a1191b60583fa99d1693656fa2526da/contracts/AlgoPool.sol)

- AlgoPool.sol


# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **4 notes** were reported, including:

- 1 notes.

- 3 owner privileges.


## 3.1. Owner privileges

### Severity: owner privileges

### Description

Owner of smart contract has right to:
1. Withdraw `stablecoin`, deposited by users, form contract using function `withdrawToTradingAdmin`;
2. Deposit `stablecoin` to contract using functions `depositAdmin` to return previously withdrawn `stablecoin` for trading and `depositStableCoin` to add `stablecoin` for paying interest.
3. Change `noncePeriod` to any value at any time using function `changeNoncePeriod`. It will make an effect on interest payment for already deposited `stablecoin` into pools.


## 3.2. Bonds is not transferable

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

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
