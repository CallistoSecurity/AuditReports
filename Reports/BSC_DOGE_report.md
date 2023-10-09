#  Binance-Peg Dogecoin Token (DOGE) Security Audit Report

# 1. Summary

[Binance-Peg Dogecoin Token (DOGE)](https://bscscan.com/address/0xba2ae424d960c26247dd6c32edc70b295c744c43) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The Binance-Peg Dogecoin Token (DOGE) is an upgradable contract where the owner can change the executable contract whenever they want. In this report, we audit contract implementation https://bscscan.com/address/0xba5fe23f8a3a24bed3236f05f2fcf35fd0bf0b5c#code that was active on 30 April 2023

# 2. In scope

- [BEP20UpgradeableProxy](https://bscscan.com/address/0xba2ae424d960c26247dd6c32edc70b295c744c43#code)
- [BEP20TokenImplementation](https://bscscan.com/address/0xba5fe23f8a3a24bed3236f05f2fcf35fd0bf0b5c#code)

# 3. Findings

In total, **1 issue** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issue.

In total, **2 notes** were reported, including:

- 0 notes.

- 2 owner privileges.

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

## 3.2. Owner privileges

### Severity: owner privileges

### Description

1. Token uses an upgradable proxy contract where the owner can change the implementation contract whenever they want. A new implementation may have issues or dangerous functionality for users.
2. Owner can [mint](https://bscscan.com/address/0xba5fe23f8a3a24bed3236f05f2fcf35fd0bf0b5c#code#L566) any amount of tokens to themselves.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only a low severity issue was found during the audit.

Users must pay attention to unlimited contract owner's rights. 

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

