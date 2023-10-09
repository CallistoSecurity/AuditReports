# Immiris Security Audit Report

# 1. Summary

[Immiris v2]() smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash ad220d3ab6a01d39a41cff70a0fd44fb00fb0264.

- [OwnablePausable.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/plugins/OwnablePausable.sol).
- [OwnableSecondary.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/plugins/OwnableSecondary.sol).
- [Controller.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/Controller.sol).
- [ImmutableEternalStorage.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/ImmutableEternalStorage.sol).
- [ImmutableEternalStorageInterface.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/ImmutableEternalStorageInterface.sol).
- [Migrations.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/Migrations.sol).
- [StatementRegistery.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/StatementRegistery.sol).
- [StatementRegisteryInterface.sol](https://github.com/thibautvdu/ImmirisContracts/blob/ad220d3ab6a01d39a41cff70a0fd44fb00fb0264/StatementRegisteryInterface.sol).

# 3. Findings

In total, **2 issues** were reported including:

- 1 notes.

- 1 owner privileges (ability of owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Gas Consumption  

### Severity: note

### Description

### Code snippet

`statementIdsByBuildingPermit` and `getAllStatements` functions members of `StatementRegistery` contract contain a loop that might make the externals calls throw even if marked as `view`, the nodes that will run the request will need to be set with a higher gas limit for view function. If the node is public and the internal gas limit is high then an attacker can use it for a DoS attack.

This issue do not represent a direct risk, but it should be noted.

# Conclusion

https://github.com/RideSolo/ImmirisContracts/blob/master/StatementRegistery.sol#L45

https://github.com/RideSolo/ImmirisContracts/blob/master/StatementRegistery.sol#L110

## 3.2. Controller Redeployment

### Severity: owner privileges

### Description

This issue was presented as ["3.4. The relocating is not a secure process for investors"](https://gist.github.com/yuriy77k/ba8317d2aeefeeff9b8fb7f23a68f5d0#34-the-relocating-is-not-secure-process-for-investors) in the first audit.

Since the UI will use `Controller` contract to request `StatementRegistery` that will write in the eternal storage (`dataStore`), the user should get the address of the `StatementRegistery` contract once the procedure is done in the case if the controller contract is redeployed and `StatementRegistery` address is reset to a new one (or any other possibility in the near or far future). Please note that at the moment only `statementId` is returned by `recordStatement` member of `Controller` contract.

Eternal storage should also be found by the users, otherwise they will not be able to prove that their construction permit was visible.

### Code snippet

https://github.com/thibautvdu/ImmirisContracts/blob/master/Controller.sol#L23


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/38ebcbd9af8d3fa54f2b8b8f3ff09c8d

https://gist.github.com/yuriy77k/a4bd5d33a492c21c001f7fa7fcc4850c

https://gist.github.com/yuriy77k/9d6fbc496c8e6135005a2d139acbc2d3
