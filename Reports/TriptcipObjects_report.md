# TriptcipObjects Contract Security Audit Report

# 1. Summary

[TriptcipObjects](https://github.com/Triptcip/triptcip-contracts/blob/master/contracts/TriptcipObjects.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `3089e49f39e03473d705071fe9fb4cf0e4334298`

- [TriptcipObjects](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol)

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **3 notes** were reported, including:

- 3 notes.

- 0 owner privileges.

No critical security issues were found.


## 3.1. Duplicate command

### Severity: note

### Description

The [require](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L54-L55) condition used twice.

### Recommendation

Remove duplicate to reduce gas usage.

## 3.2. Unused array `newTokenIds`

### Severity: note

### Description

The in the function `mintWhitelist` the local array [newTokenIds](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L78) is declared and [assigned](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L85) with `newTokenId`, but values from array is not used.

The same is applicable for functions [mint](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L103) and [airdrop](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L123)

### Recommendation

Remove array `newTokenIds` to reduce gas cost.

## 3.3. Purpose of function `mintWhitelist` is not clear

### Severity: low

### Description

The function [mintWhitelist](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L62-L89) allow user to mint token if he provide correct signature (is whitelisted), however the function [mint](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L91-L113) allow any user to mint tokens without signature. Therefor why users have to be whitelisted if they can mint without it?


## 3.4. Airdrop to single address

### Severity: note

### Description

Usually, airdrop do to the list of addresses, but the function `airdrop` allow `owner` to mint tokens to the [single address](https://github.com/Triptcip/triptcip-contracts/blob/3089e49f39e03473d705071fe9fb4cf0e4334298/contracts/TriptcipObjects.sol#L117). Pay attention to it.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit. Pay attention to notes.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

