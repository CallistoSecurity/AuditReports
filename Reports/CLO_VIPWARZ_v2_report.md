# VIPWARZ v2 Security Audit Report

# 1. Summary

[VIPWARZ](https://github.com/VIPSVERSE/VVT-Token) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)


# 2. In scope

Commit [6e3653502ac5dd4908937907fc30715f56e30867](https://github.com/VIPSVERSE/VVT-Token/tree/6e3653502ac5dd4908937907fc30715f56e30867)

- ICO.sol
- LockedFund.sol
- VVT.sol

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **1 notes** were reported, including:

- 0 notes.

- 1 owner privileges.


## 3.1. Owner privileges.

### Severity: owner privileges.

### Description

Owner of VVT token can set/change any address as [farm](https://github.com/VIPSVERSE/VVT-Token/blob/6e3653502ac5dd4908937907fc30715f56e30867/VVT.sol#L679-L681) that have permission to mint tokens.




# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

Technical task

1. Compare files from two directories (use byte to byt XOR operator).
2. Use unmatched data for restoring Master Key (according formula).
3. Scan entire driver (or folder with subfolder), decrypt filenames using formula then decrypt file content using Master Key
4. Save decrypted files with decrypted names into destination folder.
