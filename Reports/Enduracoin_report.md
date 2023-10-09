# Enduracoin Security re-audit v4 Report

# 1. Summary

[Enduracoin](https://github.com/CallistoSecurity/EnduracoinToken/tree/main) smart contract security audit report performed by [GreyWolf](https://github.com/greywolf12)

# 2. In scope

Commit `08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb`

- EnduracoinToken.sol
- EnduracoinValue.sol
- ManageApprovers.sol
- StringHelpers.sol
- ChangeRequests.sol

# 3. Findings

In total, **0 issues** were reported, including:

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

1. The modifier [requiresMultiSig](https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/ChangeRequests.sol#L68) restrict `owner` to call function until voting is finished, but allow anybody else to call function without restriction. Therefore, in context of contract `EnduracoinValue` it does not make any sense and can be removed or replaced by modifier `onlyApprovers`.
- https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/EnduracoinValue.sol#L125
- https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/EnduracoinValue.sol#L137
- https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/EnduracoinValue.sol#L147
- https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/EnduracoinValue.sol#L161
- https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/EnduracoinValue.sol#L170


2. The `getPendingChangeRequest()` is a view function, so does not require `onlyApprovers` modifier.
- https://github.com/CallistoSecurity/EnduracoinToken/blob/08b4d7ec8603f7a0711c566bbd92bb9fc6d1eaeb/ChangeRequests.sol#L150


