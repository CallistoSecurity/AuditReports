# PGX Token Security Audit Report

# 1. Summary

[PGX Token](https://github.com/sharevestX/PGX/tree/master/contracts) smart contract security audit report.

# 2. In scope

Commit hash 5110fcc8a7b86b60c21e4a5973ed4bf03da83d12.

- [PhigoldMultisig.sol](https://github.com/sharevestX/PGX/blob/5110fcc8a7b86b60c21e4a5973ed4bf03da83d12/contracts/PhigoldMultisig.sol)
- [token.sol](https://github.com/sharevestX/PGX/blob/5110fcc8a7b86b60c21e4a5973ed4bf03da83d12/contracts/token.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.


## 3.1 Owner Privileges

### Severity: owner privileges

### Description

1. Owner can [mint](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol#L575) any amount of tokens. There is no limit for minting. 
2. Owner can [pause/unpause](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol#L547-L561) tokens [transferring](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol#L580-L584). 


# 4. Conclusion

The audited smart contract is safe and can be deployed. Investors should be aware of owners privileges which allow owners to mint any amount of tokens and block transferring at any time.
