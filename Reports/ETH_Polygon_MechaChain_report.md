# MechaChain ERC20 & Vesting Security Audit Report

# 1. Summary

[MechaChain ERC20 & Vesting](https://mechachain.io) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Project: https://mechachain.io

# 2. In scope

Commit `2554bee685a7221defcdb7b0b01b615c58178886`

- [ChildMechanium.sol](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts_v0.6.6/ChildMechanium.sol)
- [Mechanium.sol](https://github.com/thibautvdu/MechaChain-Smart-Contracts/2554bee685a7221defcdb7b0b01b615c58178886/develop/contracts/Mechanium.sol)
- [MechaniumVesting](https://github.com/thibautvdu/MechaChain-Smart-Contracts/tree/2554bee685a7221defcdb7b0b01b615c58178886/contracts/MechaniumVesting)
- [MechaniumVestingWallet](https://github.com/thibautvdu/MechaChain-Smart-Contracts/tree/2554bee685a7221defcdb7b0b01b615c58178886/contracts/MechaniumVestingWallet)

## 2.1 Excluded from audit

- Standard library contracts were excluded from audit:

1. @openzeppelin/contracts/token/ERC20/ERC20.sol
2. @openzeppelin/contracts/utils/math/SafeMath.sol
3. @openzeppelin/contracts/access/AccessControl.sol
4. @openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol
5. @openzeppelin/contracts/utils/Counters.sol
6. @maticnetwork/pos-portal/contracts/child/ChildToken/ChildERC20.sol


- Staking pool contract, that used in the [MechaniumPresaleDistribution](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts/MechaniumVesting/MechaniumPresaleDistribution.sol#L251), was excluded from audit due to lack of source code.

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **4 notes** were reported, including:

- 1 notes.

- 3 owner privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Deployer get the DEFAULT_ADMIN_ROLE

### Severity: note

### Description

In the `ChildMechanium` contract the `DEFAULT_ADMIN_ROLE` assign to [adminWallet](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts_v0.6.6/ChildMechanium.sol#L21). But in the `ChildERC20` contract the `DEFAULT_ADMIN_ROLE` also assign to the [msg.sender](https://github.com/maticnetwork/pos-portal/blob/88dbf0a88fd68fa11f7a3b9d36629930f6b93a05/flat/ChildERC20.sol#L1468). It's mean the deployer will has `DEFAULT_ADMIN_ROLE` as well.

### Recommendation

Deploy contract from `adminWallet` or remove deployer address from `DEFAULT_ADMIN_ROLE` adding following code in the [constructor](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts_v0.6.6/ChildMechanium.sol#L18-L22):
```Solidity
    _revokeRole(DEFAULT_ADMIN_ROLE, _msgSender());
```

## 3.3. Owner privileges

### Severity: owner privileges

### Description

1. Admin wallet and deployer of `ChildMechanium` contract have right to setup any role to any address that alow them to mint tokens.
2. Admin of `MechaniumPresaleDistribution` contract has right to set/change [ptePoolAddress](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts/MechaniumVesting/MechaniumPresaleDistribution.sol#L192-L199) and has right to [transfer unsold tokens](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts/MechaniumVesting/MechaniumPresaleDistribution.sol#L204-L222) to that address when vesting started.
3. Admin of `MechaniumPresaleDistribution` contract has right to set/change [staking pool address](https://github.com/thibautvdu/MechaChain-Smart-Contracts/blob/2554bee685a7221defcdb7b0b01b615c58178886/contracts/MechaniumVesting/MechaniumPresaleDistribution.sol#L228-L236).



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
