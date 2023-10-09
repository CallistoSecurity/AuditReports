# 1. Summary

[1Address](https://github.com/1Address/1Address/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Outsource Bitcoin vanity address generation. The final vanity address private key should be known only to the buyer.

https://1address.io/


# 2. In scope

# In scope

1. [Migrations.sol](https://github.com/1Address/1Address/blob/ec32941eebe777de6ba1d64ff2e9da351ac0a345/contracts/Migrations.sol)
2. [TaskRegister.sol](https://github.com/1Address/1Address/blob/ec32941eebe777de6ba1d64ff2e9da351ac0a345/contracts/TaskRegister.sol)
3. [Upgradable.sol](https://github.com/1Address/1Address/blob/ec32941eebe777de6ba1d64ff2e9da351ac0a345/contracts/Upgradable.sol)
4. [VanityLib.sol](https://github.com/1Address/1Address/blob/ec32941eebe777de6ba1d64ff2e9da351ac0a345/contracts/VanityLib.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.


## 3.1. Using tx.origin for authorization

### Severity: low

### Description

Never use tx.origin for authorization. Use msg.sender instead. 
More information [here](https://solidity.readthedocs.io/en/develop/security-considerations.html#tx-origin).

### Code snippet

https://github.com/1Address/1Address/blob/ec32941eebe777de6ba1d64ff2e9da351ac0a345/contracts/Upgradable.sol#L31

## 3.2. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.18. It is suggested to use the latest version and fix all compiler warnings that arise. 
Compiler version should be fixed to avoid any potential discrepancies in smart contract behavior caused by different versions of compiler.

# 4. Conclusion

There is no serious vulnerability was discovered in this contract. It can be used safely.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/2dc67b68c7e3165296ce946c98a5dfb0

https://gist.github.com/yuriy77k/2dc67b68c7e3165296ce946c98a5dfb0

https://gist.github.com/yuriy77k/45e15117bc79eed6fe3f116b374b9af8
