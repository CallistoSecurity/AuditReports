# FEMECONOMY token v.3. Security Audit Report

# 1. Summary

[FEMECONOMY token](https://github.com/femeconomyorg/smart-contract/blob/main/femeconomy.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Website:  https://www.femeconomy.org
- Twitter: https://twitter.com/femeconomyorg 
- LinkedIn: https://www.linkedin.com/company/femeconomyorg 
- Facebook: https://www.facebook.com/femeconomyorg  
- Instagram: https://www.instagram.com/femeconomyorg 
- Medium: https://medium.com/femeconomy 

# 2. In scope

Commit `b9c4bbef738a364dabfc944022ca59d30e4fbb32`

- [femeconomy.sol](https://github.com/femeconomyorg/smart-contract/blob/b9c4bbef738a364dabfc944022ca59d30e4fbb32/femeconomy.sol)

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **4 notes** were reported, including:

- 0 notes.

- 4 owner privileges.

## 3.1. Owner privileges.

### Severity: owner privileges

### Description

The contract owner has rights:

1. Lock tokens on any user account using function `_lock()`.
2. Unlock tokens from any account using functions `unlock()` and `unlockAll()`.
3. Burn tokens from any user account using functions `burn()`.
4. Mint tokens to any account using functions `_mint()` (up to 20,000,000,000 in total supply).

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Multisig owner account.**
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

Users have to pay attention to the owner privileges that have total control over users' tokens.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

