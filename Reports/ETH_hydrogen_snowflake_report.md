# 1. Summary

[Hydrogen Snowflake](https://github.com/HydroBlockchain/smart-contracts/blob/master/snowflake/contracts/Snowflake.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> A smart contract that enables identity-management under ERC 1484 and enables various types of transfers of HYDRO tokens.

https://www.hydrogenplatform.com/

# 2. In scope

- [Snowflake.sol](https://github.com/HydroBlockchain/smart-contracts/blob/master/snowflake/contracts/Snowflake.sol) github commit hash 90389befae1fcbca015f1257bf9d5ac5eccd302a.

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Gas limit and loops

### Severity: medium

### Description

There is no upper limit on providers, it increments each time a new transaction is registered. Eventually, as the count of transactions increases, gas cost of smart contract calls will raise.
If providers.length is large enough, the function exceeds the block gas limit, and transactions calling it will never be confirmed.
 
### Code snippet

https://github.com/HydroBlockchain/smart-contracts/blob/90389befae1fcbca015f1257bf9d5ac5eccd302a/snowflake/contracts/Snowflake.sol#L75

## 3.2. Non-initialized return value

### Severity: low

### Description

There is no return statement in this function that means that it always returns false by default. And this can break the logic of the Dapp.

### Code snippet

https://github.com/HydroBlockchain/smart-contracts/blob/90389befae1fcbca015f1257bf9d5ac5eccd302a/snowflake/contracts/Snowflake.sol#L440-L446

### Recommendation

Add return statement with the right expression.

## 3.3. Owner Privileges (the relocating is not secure process for investors).

### Severity: low

### Description

The owners can implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

### Code snippet

https://github.com/HydroBlockchain/smart-contracts/blob/90389befae1fcbca015f1257bf9d5ac5eccd302a/snowflake/contracts/Snowflake.sol#L53-L59

## 3.4. It is necessary to check the input address to the zero-address.

### Severity: low

### Description

In the setAddresses function, the input address of _hydroTokenAddress is not checked for a null value and the funds can be accidentally transferred to a 0x0-address.

### Code snippet

https://github.com/HydroBlockchain/smart-contracts/blob/90389befae1fcbca015f1257bf9d5ac5eccd302a/snowflake/contracts/Snowflake.sol#L53-L59

## 3.5. `identityExists` modifier error message.

### Severity: minor observation

### Description

In case if `check` variable is `false` an error message should be "The EIN exists.". But now in any case shows the message "The EIN **does not** exist.".

### Code snippet

https://github.com/HydroBlockchain/smart-contracts/blob/90389befae1fcbca015f1257bf9d5ac5eccd302a/snowflake/contracts/Snowflake.sol#L38

# 4. Conclusion

Medium issues can influence smart contracts operation in current implementation. We highly recommend addressing them.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0421d4c997161be46e5706bc2a65be60

https://gist.github.com/yuriy77k/c8e0997f2cee239d6ab35c28710e4952

https://gist.github.com/yuriy77k/7fa43a0c57b525cebdd7f859db5f4fda
