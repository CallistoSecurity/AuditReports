# EnduracoinToken v3 Security Audit Report

# 1. Summary

[EnduracoinToken](https://github.com/Enduracoin/EnduracoinToken/tree/main) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit [410ae3c9773815a225459edfc37783a013769643](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/)

- EnduracoinToken.sol
- EnduracoinValue.sol
- ManageApprovers.sol
- StringHelpers.sol
- ChangeRequests.sol

# 3. Findings

In total, **3 issues** were reported, including:

- 1 high severity issue.

- 1 medium severity issue.

- 1 low severity issue.

In total, **3 notes** were reported, including:

- 1 minor observation.

- 2 owner privileges.



## 3.1. Anybody can `transferOwnership` or `renounceOwnership` 

### Severity: high

### Description

1. The modifier `requiresMultiSig` checks that voting is finished only if `msg.sender == owner()` or `pendingChange.addrValueToChange == owner()` otherwise function executed independently of voting status. 
2. Functions `transferOwnership` and `renounceOwnership` require the `pendingChangeType` should have an appropriate value (`ChangeType.transferOwnership` or `ChangeType.renounceOwnership` accordingly) and do not check the voting result.

Therefore, when a new change request to `transferOwnership` or `renounceOwnership` is created, anybody will be able to transfer ownership to any address or renounce ownership.

### Code snippet

- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L33-L38
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L133
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L143


### Recommendation

Add into functions `transferOwnership`, `renounceOwnership` requirements:
```Solidity
        require(forChange >= MIN_VOTES_REQUIRED, "Not approved");
```

## 3.2. All approvers will be removed even if "renounceOwnership" request is declined 

### Severity: medium

### Description

The function `cleanUpChangeRequests()` is called when voting is complete. This function will remove all approvers and delete `historicalListOfApprovers` if `pendingChangeType == ChangeType.renounceOwnership`, independently of the voting result. So if "renounceOwnership" request is declined all approvers will be deleted anyway.

### Code snipped
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L67
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinValue.sol#L87
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L161-L168

### Recommendation

Add to [if](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L161) condition check of the voting result:

```Solidity
        if(pendingChangeType == uint8(ChangeType.renounceOwnership) && forChange >= MIN_VOTES_REQUIRED ){ 
```


### Recommendation

## 3.3. The new owner will not be added to `historicalListOfApprovers`

### Severity: low

### Description

In the function `execChangeRequest()` in case of `pendingChangeType == uint8(ChangeType.transferOwnership)` new owner's address will be added to the approver list by function `_setApprover()`, but this function doesn't add the approver address to `historicalListOfApprovers` (it was implemented in function `setApprovers()`).

### Code snippet

- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L54-L56
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ManageApprovers.sol#L102-L108
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ManageApprovers.sol#L94

### Recommendation

Move command which adds new approver to `historicalListOfApprovers` from `setApprovers()` to `_setApprover()`.

```Solidity
    /**
     * @dev `setApprovers`(`address`[`MAX_APPROVERS`] memory `newApprovers`) adds approver addresses from 
     * the array of `newApprovers` to the list of approvers. Internal function without access restriction.  */
    function setApprovers(address[MAX_APPROVERS] memory newApprovers) internal virtual {
        require(newApprovers.length < MAX_APPROVERS + 1, "Attempted to add too many approvers.");
        for (uint i = 0; i < newApprovers.length; i++) {
            if(newApprovers[i] != address(0)){ 
                _setApprover(newApprovers[i]); 
            }
        }
    }
    
    /**
     * @dev `_setApprover`(address `newApprover`) adds an address of 'newApprover' to the list of approvers.
     * Internal function without access restriction.  */
    function _setApprover(address newApprover) internal {
        require(!approverAddresses[newApprover], "INFO: The approver address is already on the approvers list.");
        require(numberOfApprovers < MAX_APPROVERS, "ERROR: The maximum approver limit has been reached.");
        historicalListOfApprovers.push(newApprover); 
        approverAddresses[newApprover] = true;
        numberOfApprovers++;
        emit ApproverAdded(newApprover);
    }

```

## 3.4. Owner privileges

### Severity: owner privileges

### Description

1. 50 Billion Enduracoin will be pre-minted to the owner's wallet. If tokens are burnt, the owner has the right to mint new tokens up to 50 Billion in total supply.
2. The majority of approvers can set any value in the `EnduracoinValue` contract. So the `getCurrentValue` in the `EnduracoinValue` contract does not get a real market value of Enduracoin in a decentralized way.


## 3.5. Multiple minor observation

### Severity: minor observation

### Description

1. In the function `setApprovers()` don't need to check `newApprovers.length` because `newApprovers` is a fixed-size array.
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ManageApprovers.sol#L90

2. The `getPendingChangeRequest()` is a view function, so does not require `onlyApprovers` modifier.
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L133

3. The function `voteForChangeRequest()` has `onlyApprovers` modifier, so `require(approverAddresses[msg.sender] == true)` is a double-check and can be removed.
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L193

4. The `validateApproverExists()` is a view function, so does not require `onlyApprovers` modifier. 
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ManageApprovers.sol#L82

5. If `MIN_VOTES_REQUIRED` will be changed, the function `voteForChangeRequest` will use 4 required votes anyway. Recommend replacing "magic number" `4` with `MIN_VOTES_REQUIRED`.
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L221
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L231

6. Overriding [removeApprover](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L148-L159) function in `Enduracoin` contract is not necessary and can be removed. All checking was made in other places:
   1. This function is internal and called only from function [execChangeRequest()](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinToken.sol#L53) if `forChange == MIN_VOTES_REQUIRED`.
   2. Checking of removed address was in function [newChangeRequest](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L103).
   3. Checking of `numberOfApprovers` was in function [newChangeRequest](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ChangeRequests.sol#L104) as well.

The same is applicable for [removeApprover](https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinValue.sol#L136-L148) in `EnduracoinValue` contract.

7. Modifier `requiresApprovals()` should allow calling functions only if a majority voted for. `|| opposeChange >= MIN_VOTES_REQUIRED` should be removed.
- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/ManageApprovers.sol#L57

8. Multiple imports of the ManageApprovers contract.

The contract `EnduracoinValue` inherits the contract's `ManageApprovers` and `ChangeRequests`, the import for the contract `ManageApprovers` is redundant as the contract is already inherited by the contract `ChangeRequests`.

- https://github.com/Enduracoin/EnduracoinToken/blob/410ae3c9773815a225459edfc37783a013769643/EnduracoinValue.sol#L12

# 4. Security practices

- [ ] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits if a large amount is being withdrawn quickly until the owner or the project community approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. Every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it, even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native tokens deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021st transaction, then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021st transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed before the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/chhajershrenik/43ce58a11e1c706714df3da4b330fbdc
- https://gist.github.com/greywolf12/b2c1bd468f059d8503b288aa73b7cb57

## 6.1. Previous audit report

- v1: https://gist.github.com/yuriy77k/d374031c58202d6b1d57086dfecfc35c
- v2: https://gist.github.com/yuriy77k/af105a36bb8109bcafe742825efb4906
