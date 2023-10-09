# TokenDispense Security Audit Report

# 1. Summary

[TokenDispense](https://github.com/mx-samurai/mx-samurai-token/blob/V2/contracts/TokenDispense.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [ef0a983d1b45673ebae53474aa74e5136ccfbc5d](https://github.com/mx-samurai/mx-samurai-token/blob/ef0a983d1b45673ebae53474aa74e5136ccfbc5d/contracts/TokenDispense.sol)

- TokenDispense.sol

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **1 notes** were reported, including:

- 1 notes.

- 0 owner privileges.

No critical security issues were found.

## 3.1. Unused code

### Severity: note

### Description

The libraries `SafeMath`, `Address`, `console` are not required and not used in the code.

### Recommendation

Remove unused libraries to reduce deployment gas cost.

Remove next rows:
- https://github.com/mx-samurai/mx-samurai-token/blob/ef0a983d1b45673ebae53474aa74e5136ccfbc5d/contracts/TokenDispense.sol#L3-L4
- https://github.com/mx-samurai/mx-samurai-token/blob/ef0a983d1b45673ebae53474aa74e5136ccfbc5d/contracts/TokenDispense.sol#L7
- https://github.com/mx-samurai/mx-samurai-token/blob/ef0a983d1b45673ebae53474aa74e5136ccfbc5d/contracts/TokenDispense.sol#L10-L11


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
