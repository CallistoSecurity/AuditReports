# PGX Token Security Audit Report

# 1. Summary

[PGX Token](https://github.com/sharevestX/PGX/tree/master/contracts) smart contract security audit report.

# 2. In scope

Commit hash 5a567e9678c4ce4c673008f1bc7137984a862f80.

- [PhigoldMultisig.sol](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/PhigoldMultisig.sol)
- [token.sol](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1 Any of owners can bypass multisig restriction for token transfer.

### Severity: medium

### Description

The function `airdrop` allow any of owners make token transfer and airdrop without approval of other owners. It allow bypass multisig restriction which requires approval 2 of 3 owners.

### Code snippet

https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/PhigoldMultisig.sol#L281-L286

### Recommendation

There is multisig function for airdrop - `newAirdropRequest`, therefore function `airdrop` should be deleted.

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.3 `setTokenAddress` can be called by anyone

### Severity: low

### Description

If token address was not set previously, anyone can call the function `setTokenAddress` to set the address and the owner could not change it.

### Code snippet

https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/PhigoldMultisig.sol#L40

### Recommendation

Add `onlyOwners` modifier or set token address in the constructor.

## 3.4 Owner Privileges

### Severity: owner privileges

### Description

1. Owner can [mint](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol#L575) any amount of tokens. There is no limit for minting. 
2. Owner can [pause/unpause](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol#L547-L561) tokens [transferring](https://github.com/sharevestX/PGX/blob/9dfd026b5d26a8628622739a3fde8ed3275af2f5/contracts/token.sol#L580-L584). 


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract. Investors should be aware of owners privileges which allow them to mint any amount of tokens and block transferring at any time.
