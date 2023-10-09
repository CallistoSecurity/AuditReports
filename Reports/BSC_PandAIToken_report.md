# PandAI Token Contract Security Audit Report

# 1. Summary

[PandAI Token](https://bscscan.com/token/0x550d7984b7adfff88815e5528e12e322df6d3b9b#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://pandai.io/

# 2. In scope

[PandAIToken](https://bscscan.com/token/0x550d7984b7adfff88815e5528e12e322df6d3b9b#code)

# 3. Findings

In total, **1 issue** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issue.

In total, **2 notes** were reported, including:

- 1 note.

- 1 owner privilege.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 / BEP-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Owner Privileges

### Severity: owner privilege

### Description

Currently, the total supply of the tokens is minted to the owner of the contract, and the distribution of tokens is controlled by the owner.  

```solidity
    constructor() ERC20("PandAI Token", "PANDAI") {
        _mint(msg.sender, 100000000000000 * 10 ** decimals());
    }
```

Consider transferring the tokens initially to a multi-sig account, so that the tokens are protected by multiple members during the distribution and vesting period.

## 3.3. Follow good coding practice

### Severity: Note

### Description
1. Missing docstrings

   The PandAIToken contract in the code base lack documentation. Docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

   Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

2. Unlocked Pragma

   Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.9, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

3. Missing test suite

    The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.

4. PandAI Token has 6 decimals

    Tokens with 18 decimals are the current norm, For instance, USDC has 6 decimals, cTokens from Compound and WBTC have 8. Your internal calculations may be inaccurate if you are handling PandAI tokens without considering these cases.



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

# 6. Revealing audit reports

- https://gist.github.com/SakshamGuruji3/6c668428563d9feaec69b438e401bb59
- https://gist.github.com/chhajershrenik/30cdc8cebcb7541d454675f21073462f
- https://gist.github.com/ESNJS/c54f84d736b8e5e4cc04c9fe3d19c504
