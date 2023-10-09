# Security Audit Report

# 1. Summary

[Immiris](https://github.com/thibautvdu/ImmirisContracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The purpose of this contract is quite simple : record 'statements' in an eternal storage. Each statement is made of 12 key-value strings pair that canno't be changed or deleted by anyone ever. The purpose is to use it as a legal statement to claim that the recorded strings have not been tempered/modified since the transaction. 2 of those key-value strings are IPFS hashes pointing to images.

# 2. In scope

Commit hash: `7d56cc3490e991d0846baa5563087c252878363d`

1. [Controller.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/Controller.sol)
2. [ImmutableEternalStorage.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/ImmutableEternalStorage.sol)
3. [ImmutableEternalStorageInterface.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/ImmutableEternalStorageInterface.sol)
4. [Migrations.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/Migrations.sol)
5. [StatementRegistery.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegistery.sol)
6. [StatementRegisteryInterface.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegisteryInterface.sol)
7. [plugins/OwnableSecondary.sol](https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/plugins/OwnableSecondary.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 1 high severity issues.

- 4 low severity issues.

No critical security issues were found.

## 3.1. Out of gas

### Severity: high

### Description

`statementCountByBuildingPermit` function has a while-loop to count the number of statements. This can lead to "Out of Gas" error or to "Block Gas Limit". `view` functions do not charge for gas, but it is still calculated to not exceed the limits. For example, for 10 statements in storage the cost of calling the function will be equal to 63998 gas. For 100 statements it will be 402116 gas. Block gas limit is about 8000000. If in storage will be more than 1600 statements — **smart contract will not be workable** because the call to this function is used when creating new statements in `recordStatement`.

### Code snippet

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegistery.sol#L165

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegistery.sol#L25

### Recommendation

Store the number of statements for each `buildingPermitId` to not recalculate each time.


## 3.2. It is necessary to check the input address to the zero-address.

### Severity: low

### Description

The input address is not checked for a null value and the funds can be transferred to a 0x0-address.

### Code snippet

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/Controller.sol#L19-L21

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegistery.sol#L16


## 3.3. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to change the price.

### Code snippet

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/Controller.sol#L24-L28

## 3.4. The relocating is not secure process for investors.

### Severity: low

### Description

The owners can implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

### Code snippet

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/Controller.sol#L19

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegistery.sol#L17

## 3.5. Consider removing the destruction mechanism

### Severity: Low

### Description

When a contract is destruct from the blockchain, its bytecode is reverted to 0, but transactions made to this address are still accepted. That means that any amount transferred to the contract after its destruction is lost forever.

### Recommendation

A better approach is to implement a soft ending mechanism, where no function can be called and sent ether can be rejected. Take a look at the Pausable.sol from Zeppelin.

### Code snippet

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/Controller.sol#L61-L64

https://github.com/thibautvdu/ImmirisContracts/blob/7d56cc3490e991d0846baa5563087c252878363d/StatementRegistery.sol#L39-L42

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/a816552cca5cc9468817cfaf60a8765b

https://gist.github.com/yuriy77k/b548b814776088eae4a97d83b7340c13

https://gist.github.com/yuriy77k/f3324b69dcb7219a9f868cb782b7fd3d
