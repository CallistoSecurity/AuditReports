# EnduracoinToken v4 Security Audit Report

# 1. Summary

[EnduracoinToken](https://github.com/Enduracoin/EnduracoinToken/tree/main) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit [2ac940bfee90dba11d0b5499ae3e16d988b47ec6](https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/)

- EnduracoinToken.sol
- EnduracoinValue.sol
- ManageApprovers.sol
- StringHelpers.sol
- ChangeRequests.sol

# 3. Findings

In total, **30 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **3 notes** were reported, including:

- 1 minor observation.

- 2 owner privileges.



## 3.1. Owner privileges

### Severity: owner privileges

### Description

1. 50 Billion Enduracoin will be pre-minted to the owner's wallet. If tokens are burnt, the owner has the right to mint new tokens up to 50 Billion in total supply.
2. The majority of approvers can set any value in the `EnduracoinValue` contract. So the `getCurrentValue` in the `EnduracoinValue` contract does not get a real market value of Enduracoin in a decentralized way.


## 3.2. Multiple minor observation

### Severity: minor observation

### Description

1. The modifier [requiresMultiSig](https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/ChangeRequests.sol#L68) restrict `owner` to call function until voting is finished, but allow anybody else to call function without restriction. Therefore, in the context of the contract `EnduracoinValue` it does not make any sense and can be removed or replaced by the modifier `onlyApprovers`.
- https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/EnduracoinValue.sol#L125
- https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/EnduracoinValue.sol#L137
- https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/EnduracoinValue.sol#L147
- https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/EnduracoinValue.sol#L161
- https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/EnduracoinValue.sol#L170


2. The `getPendingChangeRequest()` is a view function, so does not require `onlyApprovers` modifier.
- https://github.com/Enduracoin/EnduracoinToken/blob/2ac940bfee90dba11d0b5499ae3e16d988b47ec6/ChangeRequests.sol#L150

3. Owner can't create change request and must be the last approver in voting. If owner vote earlier the chenge reqest can't be approved and all others aprovver should vote against it to be able create new request. It's not a security issue, but may cause inconvinient.

4. Redundant require statement in the modifier `requiresMultiSig`, the require statement is unnecessary as the following check is already being performed in the function `voteForChangeRequest()`.
- https://github.com/Enduracoin/EnduracoinToken/blob/3b7fae4cee41858fc229c2b587879a92d7f9a741/ChangeRequests.sol#L69
- https://github.com/Enduracoin/EnduracoinToken/blob/3b7fae4cee41858fc229c2b587879a92d7f9a741/ChangeRequests.sol#L252

# 4. Security practices

- [ ] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits if a large amount is being withdrawn quickly until the owner or the project community approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. Every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it, even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native tokens deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021st transaction, then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021st transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Users should pay attention to the contract owner's rights.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/greywolf12/fc0fa65f8c0ec69a5ef1db80d2fb0261
- https://gist.github.com/chhajershrenik/dca0610f3413d4d255f25f47ec34f38c

## 6.1. Previous audit reports

- v1: https://gist.github.com/yuriy77k/d374031c58202d6b1d57086dfecfc35c
- v2: https://gist.github.com/yuriy77k/af105a36bb8109bcafe742825efb4906
- v3: https://gist.github.com/yuriy77k/b84d523240684ca6c4e1d06fdad525c7
