# Match-Contract v2 Security Audit Report

# 1. Summary

[Match-Contract](https://etherscan.io/address/0x05acf259736fde4dccb952cda90099d8459654e2#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

Match-Contract is deployed to: https://etherscan.io/address/0x05acf259736fde4dccb952cda90099d8459654e2#code

The Match-Contract chooses the winner between `CHALLENGER_1` and `CHALLENGER_2` based on the result received from `https://api.derektoyzboxing.com/result`, which is obtained using Operator contract (based on Chainlink solution). The winner can claim USDT from the contract balance. Also, `CHALLENGER_1` and `CHALLENGER_2` can vote to allow the contract owner to claim all USDT.

Users should pay attention to the centralized source of choosing the winner. Users should understand that Operator and API provider can manipulate results. 


# 2. In scope

- [Match.sol](https://etherscan.io/address/0x05acf259736fde4dccb952cda90099d8459654e2#code#F1)

## 2.1. Excluded from audit

Imported contracts from Chainlink and Openzeppelin were excluded from audits.

# 3. Findings

In total, **2 issues** were reported, including:

- 0 high severity issueS.

- 0 medium severity issues.

- 0 low severity issueS.

In total, **3 notes** were reported, including:

- 3 notes.

- 0 owner privileges.

No critical security issues were found.


## 3.1. Centralized source of the result

### Severity: note

### Description

The winner is chosen according to the result obtained from "https://api.derektoyzboxing.com/result" received from Operator contract "0x1db329cDE457D68B872766F4e12F9532BCA9149b". How the result is generated and how the operator translates it to this contract is not possible to confirm in this audit.

So users should trust them if they agree to use this contract. 


## 3.2. Follow good coding practice

### Severity: note

### Description

1. Missing docstrings

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

2. Missing test suite

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.


## 3.3. Inherited `transferOwnership` function does not change real contract owner

### Severity: note

### Description

The inherited contract `ConfirmedOwner` has `transferOwnership()` function, but this function did not change `_owner` variable that is used in the `Match` contract. So owner should be aware that ownership is not transferable.


# 4. Security practices

- [ ] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

Users should pay attention to the centralized source of choosing the winner. Users should understand that Operator and API provider can manipulate results. 

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing previous audit reports

- https://gist.github.com/yuriy77k/842a06f4e849c6d5f94590fcfb108741
