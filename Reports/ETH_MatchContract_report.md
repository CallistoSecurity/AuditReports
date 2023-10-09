# Match-Contract Security Audit Report

# 1. Summary

[Match-Contract](https://github.com/JPEX-IT/match-contract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit `d896bf9e427d4842b364ad2857c4857fedd8f152`

- [Match.sol](https://github.com/JPEX-IT/match-contract/blob/d896bf9e427d4842b364ad2857c4857fedd8f152/contract/Match.sol)

## 2.1. Excluded from audit

Imported contracts from Chainlink and Openzeppelin were excluded from audits.

# 3. Findings

In total, **2 issues** were reported, including:

- 1 high severity issue.

- 0 medium severity issues.

- 1 low severity issue.

In total, **5 notes** were reported, including:

- 4 notes.

- 1 owner privileges.

No critical security issues were found.

## 3.1. Challengers have zero address

### Severity: high

### Description

If `CHALLENGER_1` and `CHALLENGER_2` does not contain the correct addresses, the USDT that may be sent to the contract will be lost. But contract code set `CHALLENGER_1` and `CHALLENGER_2` to constant `address(0)`. It means that a contract can't be used without modification.

### Code snippet

- https://github.com/JPEX-IT/match-contract/blob/d896bf9e427d4842b364ad2857c4857fedd8f152/contract/Match.sol#L15-L16

### Recommendation

Don't use constant `CHALLENGER_1` and `CHALLENGER_2` addresses. Set these addresses in the constructor.

```Solidity
    address public CHALLENGER_1;
    address public CHALLENGER_2;

    constructor(address challenger_1, address challenger_2) ConfirmedOwner(msg.sender) {
        require(challenger_1 != address(0) && challenger_2 != address(0), "challenger address can't be 0");
        CHALLENGER_1 = challenger_1;
        CHALLENGER_2 = challenger_2;
        setChainlinkToken(CHAINLINK_TOKEN);
        setChainlinkOracle(CHAINLINK_ORACLE);
        _owner = msg.sender;
    }    

```

## 3.2. The constant value of the fee

### Severity: low

### Description

The chainlink request fee is set as a constant value in 1.4 Link tokens. But the fee can be changed during the time, so the request could not be fulfilled in case of increasing fee.

### Code snippet
- https://github.com/JPEX-IT/match-contract/blob/d896bf9e427d4842b364ad2857c4857fedd8f152/contract/Match.sol#L26

### Recommendation

Don't use constant `fee`; add a method to allow the owner to change the fee. 


## 3.3. Owner privileges

### Severity: owner privileges

### Description

Only the owner can call the function `announceResult()` to check the winner. Challengers can't check winners if the owner is unavailable.


## 3.4. The `announcementHours` is in past

### Severity: note

### Description

According to the logic of the `announceResult()` function we should wait until `announcementHours`.
But it is set to `January 30, 2023 12:00:00 PM GMT+08:00`, so this requirement does not make sense because it is past.

### Code snippet

- https://github.com/JPEX-IT/match-contract/blob/d896bf9e427d4842b364ad2857c4857fedd8f152/contract/Match.sol#L14
- https://github.com/JPEX-IT/match-contract/blob/d896bf9e427d4842b364ad2857c4857fedd8f152/contract/Match.sol#L119

### Recommendation

Update `announcementHours` to date in the future or set it in the constructor.

## 3.5. Centralized source of the result

### Severity: note

### Description

The winner is chosen according to the result obtained from "https://api.derektoyzboxing.com/result" received from Operator contract "0x1db329cDE457D68B872766F4e12F9532BCA9149b". How the result is generated and how the operator translates it to this contract is not possible to confirm in this audit.

So users should trust them if they agree to use this contract. 


## 3.6. Follow good coding practice

### Severity: note

### Description

1. Unlocked Pragma

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.7, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

2. Missing docstrings

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

3. Missing test suite

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.

4. Functions not used internally could be marked as external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract.

5. No need to initialize variables with 0 value

The default value of address type variables is the 0-address. There is no need to hardcode the initial value to 0 address.
- https://github.com/JPEX-IT/match-contract/blob/d896bf9e427d4842b364ad2857c4857fedd8f152/contract/Match.sol#L15-L18


## 3.7. Inherited `transferOwnership` function does not change real contract owner

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

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

Users should pay attention to the centralized source of choosing the winner. Users should understand that Operator and API provider can manipulate results. 

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/greywolf12/6f2041bbaad0c2876fed970cf8ec988c
- https://gist.github.com/SakshamGuruji3/544b5719f33d9dfa0cd8736b2f47b6a4
- https://gist.github.com/chhajershrenik/6b07c8543c12decac070ded27344d836
