# Seasonal Tokens Security Audit Report

# 1. Summary

[Seasonal Tokens](https://github.com/seasonaltokens/seasonaltokens) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `0be82284de6484d5adee25f8f9eb8f38d725ecf6`

- [./interfaces/ERC20.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/ERC20.sol)
- [./interfaces/ERC918.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/ERC918.sol)
- [./interfaces/Owned.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/Owned.sol)
- [./interfaces/ApproveAndCallFallBack.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/ApproveAndCallFallBack.sol)


- [./contracts/AutumnToken.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/AutumnToken.sol)
- [./contracts/SpringToken.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/SpringToken.sol)
- [./contracts/SummerToken.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/SummerToken.sol)
- [./contracts/WinterToken.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/WinterToken.sol)

## 2.1. Excluded 

- [./contracts/TestSpringToken.sol](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/TestSpringToken.sol)

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **3 notes** were reported, including:

- 3 notes.

- 0 owner privileges.

No critical security issues were found.

## 3.1. The constructor should not have `onlyOwner` modifier

### Severity: note

### Description

The [constructor](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L80) runs once on contract deployment, at the same time the deployer [assigned](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/interfaces/Owned.sol#L24) as `owner`.  So `onlyOwner` modifier has no sense in this case.

Also, [revert](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L82) contract deployment based on preset variable has no sense.

This applies to all contracts for `Seasonal Tokens` (`SpringToken`, `SummerToken`, `AutumnToken`, `WinterToken`).

### Recommendation

Remove `onlyOwner` modifier and [revert](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L82).

## 3.2. Duplicate functions

### Severity: note

### Description

The functions [getChallengeNumber()](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L253-L254) and [getMiningTarget()](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L261-L262) returns value of appropriate variables.
But the variables [challengeNumber](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L67) and [miningTarget](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L69) declared as `public` therefore compiler will automatically create the getter function for it. 

This applies to all contracts for `Seasonal Tokens` (`SpringToken`, `SummerToken`, `AutumnToken`, `WinterToken`).

### Recommendation

Remove functions  [getChallengeNumber()](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L253-L254) and [getMiningTarget()](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L261-L262).

## 3.3. The `totalSupply` does not reflect the real total supply of tokens

### Severity: note

### Description

The function [totalSupply()](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L294-L300) returns constant [TOTAL_SUPPLY](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L45) that does not reflect real total amount of tokens.

This applies to all contracts for `Seasonal Tokens` (`SpringToken`, `SummerToken`, `AutumnToken`, `WinterToken`).

### Recommendation

Return in function [totalSupply()](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L294-L300) the [tokensMinted](https://github.com/seasonaltokens/seasonaltokens/blob/0be82284de6484d5adee25f8f9eb8f38d725ecf6/contracts/AutumnToken.sol#L71) value instead of `TOTAL_SUPPLY`.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit. Some notes were pointed to optimize gas usage.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
