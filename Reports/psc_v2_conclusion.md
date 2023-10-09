# 1. Summary

[P3C.io v.2.](https://github.com/p3c-bot/p3c-bot.github.io/blob/upgrade/contracts/v1.0.0/Farm.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

1. [Farm.sol](https://github.com/p3c-bot/p3c-bot.github.io/blob/28772c8bfa5c034491bec3aac3f2b9f77e8cea55/contracts/v1.0.0/Farm.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. No checking for zero address.

### Severity: low

### Description

Transfer function and constructor Crop members of Crop contract do not require the to address to be non null before transfer. 

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

ERC20 Tokens have some well-known issues (listed below), This is just a reminder for the contract developers.

1. Lack of transaction handling mechanism issue.

The above mentioned issues are well documented, a basic search can help to get more information.

# 4. Conclusion

The vulnerability from the previous contract has been fixed. No new ones were found. The smart contract safe to deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/527953e18d71fe7a3dcb35c6fc14462f

https://gist.github.com/yuriy77k/f1e42e367828766c99d2572754f7dedb

https://gist.github.com/yuriy77k/24f888209386b7d38f6258fcf334bec9
