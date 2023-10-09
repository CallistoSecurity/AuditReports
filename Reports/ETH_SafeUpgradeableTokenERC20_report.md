# Safe Upgradeable Token ERC20 Security Audit Report

# 1. Summary

[Safe Upgradeable Token ERC20](https://github.com/EthereumCommonwealth/Auditing/issues/265) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [SafeUpgradeableTokenERC20.sol](https://github.com/guylando/EthereumSmartContracts/blob/master/SafeUpgradeableTokenERC20/contracts/SafeUpgradeableTokenERC20.sol) github commit hash 8bfb49766d967009a680548ef3cc28c011aa1c4c.


# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Allowance Approval

### Severity: low

### Description

Following ERC20 standard, `approve` function "Allows _spender to withdraw from your account multiple times, up to the _value amount. If this function is called again it overwrites the current allowance with _value.", However the implemented function throw in case if  `allowed[msg.sender][_spender]` is different than zero or `_value` different than zero. This partially solve double withdrawal attack but create incompatibility for some Dapps and do not allow the user to directly reduce the allowance creating a race between user and spender.

### Code snippet

https://github.com/RideSolo/EthereumSmartContracts/blob/master/SafeUpgradeableTokenERC20/contracts/SafeUpgradeableTokenERC20.sol#L217#LL229

## 3.2. Owner Privilege

### Severity: owner privileges

### Description

The token owner allow himself to:

- Pause/unpause tokens transfers / transferFrom / allowance allocation and approvals

### Code snippet

https://github.com/RideSolo/EthereumSmartContracts/blob/master/SafeUpgradeableTokenERC20/contracts/SafeUpgradeableTokenERC20.sol#L505#L524

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1dd50f527012fc0d18ba5cc8ea34edbe

https://gist.github.com/yuriy77k/c15a757737adcf079bd6e92156796231

https://gist.github.com/yuriy77k/c2c2132bcb35c54d82f4c2828990f500
