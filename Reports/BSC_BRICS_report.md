# BRICS Token Security Audit Report

# 1. Summary

[BRICS token](https://github.com/bricschain/bricschaintoken/tree/main) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit: c203cc42f2c683269ad88249a658e996943cc266

- [BRICSChainToken.sol](https://github.com/bricschain/bricschaintoken/blob/main/BRICSChainToken.sol)

# 3. Findings

In total, **3 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issue.

- 2 low severity issues.

In total, **11 notes** were reported, including:

- 3 minor observations.

- 8 owner privileges.


## 3.1. The `transfer`, `transferFrom`, `approve` functions do not return boolean. ERC-20 INCOMPLIANCE  

### Severity: medium

### Description

According to the [EIP-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#transfer) specification, the `transfer`, `transferFrom`, `approve` functions should return boolean. This vulnerability may lead to incorrect operation with other contracts.

### Recommendation

Set the appropriate return values and types for the `transfer`, `transferFrom`, `approve` functions.


## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.3. Missing events access control.

### Severity: minor observation

### Description

Detect missing events for critical access control parameters.

### Code Snippet

- https://github.com/bricschain/bricschaintoken/blob/main/BRICSChainToken.sol#L64

### Recommendation

Emit an event for critical parameter changes.

## 3.4. Missing zero address validation.

### Severity: low

### Description

Detect missing zero address validation.

### Code Snippet
- https://github.com/bricschain/bricschaintoken/blob/main/BRICSChainToken.sol#L422

### Recommendation

Check that the address is not zero.

## 3.5. Incorrect length of the `decimals` variable. 

### Severity: minor observation

### Description

According to the [EIP-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#decimals) specification, the length of the `decimals` variable should be `uin8`.

### Code Snippet
- https://github.com/bricschain/bricschaintoken/blob/main/BRICSChainToken.sol#L354

### Recommendation
It is recommended to change the length of the `decimals` variable to `uint8`.

## 3.6.  Owner Privileges.

### Severity: owner privileges

### Description
 
BlackList
 1. The `addBlackList` function restricts token transfers for the user.
 2. The `destroyBlackFunds` function resets the user's account to zero.

Pausable
 1. The `pause` function allows the owner to stop the `transfer` and `transferFrom` operations.
 
BRICSChainToken
1. The `deprecate` function allows the owner to change the address of the token at any time.
2. The `issue`, `mint`, `mintTo` functions allow the owner to issue an unlimited number of tokens.
3. The `redeem` function allows the owner to burn tokens, but no more than `balances[owner]`.
4. The `setParams` function allows the owner to change `basisPointsRate` and `maximumFee`, but not more than 20 and 50 respectively.
5. The `recoverTokens` function allows the owner to withdraw tokens after an upgrade.

### Recommendation
Since the owner has unlimited rights to do everything, ownership must be given to a contract with multiple signatures.



## 3.7. Follow good coding practice.

### Severity: minor observation

### Description

1. Unlocked Pragma.

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.4.17, ensures that contracts do not accidentally get deployed using a compiler version with unfixed bugs.

2. Missing docstrings.

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assessing not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

3. Missing test suite.

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.

4. Functions not used internally could be marked as external.

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is withdrawn in a short period until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native tokens deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._


# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed before the usage of this contract.

Users should pay attention to unlimited owner's privileges.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/oipkasec/7cbbce438022869fbc1177851388e7ac
- https://gist.github.com/chhajershrenik/e194dc3d80be863911a2697c3f9fce4f
