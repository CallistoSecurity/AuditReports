# ShieldHash Security Audit Report

# 1. Summary

[ShieldHash](https://github.com/shield2protocol/dapp-contract/blob/main/shieldhash.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Project Information: 

- Website: https://www.shieldprotocol.org
- Twitter: https://twitter.com/shield2protocol
- Telegram: https://t.me/shield2protocol
- Medium: https://shield2protocol.medium.com


# 2. In scope
Commit hash a23fe0fa22d6420f6395785d10b426d42caf484a
[shieldhash.sol](https://github.com/shield2protocol/dapp-contract/blob/a23fe0fa22d6420f6395785d10b426d42caf484a/shieldhash.sol)

# 3. Findings

In total, **1 issues** were reported including:

- 1 notes.

No critical security issues were found.

## 3.1. Using `string` type

### Severity: note

### Description

The type [hashinfo](https://github.com/shield2protocol/dapp-contract/blob/a23fe0fa22d6420f6395785d10b426d42caf484a/shieldhash.sol#L5) declared as `string`, but this type intends to use with text messages. If you need to store bytes data with arbitrary length better to use `bytes` type. In case if the `hashinfo` holds Keccak-256 or SHA-256 hash (or any other hash with length 256 bits) the more organic will be to use `bytes32` type (it will save gas on storage usage).


# 4. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

