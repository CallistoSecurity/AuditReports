# EnduracoinToken v2 Security Audit Report

# 1. Summary

[EnduracoinToken](https://github.com/Enduracoin/EnduracoinToken/tree/main) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit `2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885`

- EnduracoinToken.sol
- EnduracoinValue.sol
- ManageApprovers.sol
- StringHelpers.sol


# 3. Findings

In total, **3 issues** were reported, including:

- 0 high severity issues.

- 2 medium severity issues.

- 1 low severity issue.

In total, **4 notes** were reported, including:

- 2 minor observations.

- 2 owner privileges.

No critical security issues were found.

## 3.1. Any approver can cancel a change request

### Severity: medium

### Description

Any approver can cancel the change request even if the majority voted for it. Also, if one approver's wallet is compromised, he can cancel any request (including a request to remove the compromised approver), making voting useless.

### Code snipped

- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L120-L123

### Recommendation

Since a change request can be removed when the majority votes against it, it would be better to allow a voter to change his voice rather than cancel it.

## 3.2. All approvers will be removed even if the "renounceOwnership" request is declined 

### Severity: medium

### Description

In case of majority votes against the "renounceOwnership" request, the function `cleanUpChangeRequests` will be called with the parameter `check` = 0. But this function does not check whether the request was approved or declined. In any case, all approvers and `dailyValueGain`, `baseValue` will be deleted, making the contract useless.

### Code snipped

- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L138-L138
- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L152-L161

### Recommendation

Make parameter `check` = 0 if the change request is declined and `check` = 1 if the request is approved. Remove all approvers only if `check == 1`.

```Solidity
        if(check == 1 && keccak256(bytes(pendingChangeType)) == keccak256(bytes("renounceOwnership"))){ 
```

## 3.3. Owner privileges

### Severity: owner privileges

### Description

1. 50 Billion Enduracoin will be pre-minted to the owner's wallet. If tokens are burnt, the owner has the right to mint new tokens up to 50 Billion in total supply.
2. The majority of approvers can set any value in the `EnduracoinValue` contract. So the `getCurrentValue` in the `EnduracoinValue` contract does not get a real market value of Enduracoin in a decentralized way.


## 3.4. The owner can be removed from the Approver list

### Severity: low

### Description

In the function [removeApprover](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L67) there is a requirement which doesn't allow to remove the owner from approver list. But if ownership was transferred to the new owner using function [transferOwnership](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L205-L212), the new owner may not be added to approver list and is possible to remove all approvers from the list.

### Recommendation

Remove the old owner and add the new owner to the approver list while [transferOwnership](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L205-L212).

And check of the number of approvers should not be less than 1 (in the [removeApprover](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L67) function).



## 3.5. Multiple minor observations

### Severity: minor observation

### Description

1. Since ERC20 functions [transfer](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinToken.sol#L48) and [transferFrom](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinToken.sol#L55) don't trigger any callback function, therefore the reentrance attack is not possible. Recommend to remove from the `Enduracoin` contract the `ReentrancyGuard` and `nonReentrant` modifier to reduce transactions cost.

2. The array [keyValues](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/ManageApprovers.sol#L8) in the contract `ManageApprovers` is not used and can be removed.

3. The contract `EnduracoinValue` is derived from `ManageApprovers`, so it is unnecessary to specify `ManageApprovers.` when you access the parent contract.
For example, here: https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L48 and in many other places.

4. When the error message in the `require` statement is a single string, it does not need to use `string.concat`
- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L48
- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L54

5. The modifier `requiresApprovals` already checks that `msg.sender` is approver, therefore `onlyApprovers` modifier can be removed from this line: 
- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L64

6. The variables `inFavorOfChange` and `opposedToChange` are used to add new value to `arrayOfApprovals`. Use local memory variables instead of global to reduce transaction gas costs.  

7. The variable `currentValue` is private but is not used (it only [assign](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L198)) in contract and can't be read (there isn't getter function for it). Recommend removing this variable.

8. The variable `upTime` is private (there isn't a getter function for it) and unused. Recommend removing this variable.
- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L13

9. In the function `newChangeRequest`, this [requirement](https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L92) is not needed because the new request doesn't have voters yet.

10. To reduce gas consumption, the statement `ManageApprovers.numberOfApprovers--;` should be replaced with `ManageApprovers.numberOfApprovers = 0` outside the loop (after the end of the loop).
- https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L157


## 3.6. Follow good coding practice

### Severity: minor observation

### Description

1. Missing docstrings

The contracts in the code base need more documentation. It hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assessing security and correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality should also be documented, even if not public. Consider following the Ethereum Natural Specification Format (NatSpec) when writing docstrings.

2. Missing test suite

The contract needs a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure the contract functions and behaves as expected.

3. Bad coding pattern

Here https://github.com/Enduracoin/EnduracoinToken/blob/2a6208b703e4c008cbb5e3eeef6bdbfcb8c34885/EnduracoinValue.sol#L75, the two code lines inside the if statement should be made in two lines instead of one line to increase code readability.



# 4. Security practices

- [ ] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits if a large amount is being withdrawn quickly until the owner or the project community approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. Every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it, even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native tokens deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021st transaction, then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021st transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/SakshamGuruji3/40c7d70ee9191752b2f85781c7cfa267
- https://gist.github.com/chhajershrenik/ce8679e1092592242be0f0d409bed32f
- https://gist.github.com/greywolf12/7cb099377b7f997b31108fe982c97c33

## 6.1. Previous audit report

- https://gist.github.com/yuriy77k/d374031c58202d6b1d57086dfecfc35c