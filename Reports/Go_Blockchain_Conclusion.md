# 1. Summary

[Go Blockchain](https://github.com/goblockchain/smart-contract-identity) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

A DAO that aims to educate the community on all blockchain concepts, from business to tech. Teachers receive a fair fee, students pay a fair price and the result is knowledge flowing faster than ever. 

# 2. In scope

- [Collaborator.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/Collaborator.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [Migrations.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/Migrations.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [PersonIdentity.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/identity/PersonIdentity.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [TermsAndCondition.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/identity/TermsAndCondition.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [StringUtils.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/util/StringUtils.sol)github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [Math.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/zeppelin/math/Math.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [SafeMath.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/zeppelin/math/SafeMath.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [Ownable.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/zeppelin/ownership/Ownable.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [RBAC.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/zeppelin/ownership/rbac/RBAC.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.
- [Roles.sol](https://github.com/goblockchain/smart-contract-identity/blob/master/contracts/zeppelin/ownership/rbac/Roles.sol) github commit hash 41e7eb9d11c56c4d3613162994448f45416e6092.

# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Zero address checking required

### Severity: low

### Description

In function [addCollaborator](https://github.com/goblockchain/smart-contract-identity/blob/41e7eb9d11c56c4d3613162994448f45416e6092/contracts/Collaborator.sol#L40) need zero address checking

## 3.2. The name of the `isCollaborator` function does not match the meaning. 

### Severity: minor observation

### Code snippet

* [identity/PersonIdentity.sol](https://github.com/goblockchain/smart-contract-identity/blob/41e7eb9d11c56c4d3613162994448f45416e6092/contracts/identity/PersonIdentity.sol#L104)

### Description

Any address can get `true` as a result of `isCollaborator`, to do this, only need to leave a request by `requestApprove` function. But adding address as a collaborator is being only after [validation](https://github.com/goblockchain/smart-contract-identity/blob/41e7eb9d11c56c4d3613162994448f45416e6092/contracts/identity/PersonIdentity.sol#L76).

## 3.3. Consider using the latest version of solidity.

### Severity: minor observation

### Description

The contracts use different solidity version. It is suggested to use the latest version and fix all compiler warnings that arise.

# 4. Conclusion

There is no critical vulnerabilities. The smart contract safe to deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/624ca18b6d6bd19fb6e7225d7eb09284

https://gist.github.com/yuriy77k/124dd8254bdf56a5c0a5ac456e9d1783

https://gist.github.com/yuriy77k/90ce6a293bdc4712ab0b737ac7f9e4ea
