# SymVerse Security Audit Report

# 1. Summary

[SymVerse](symverse.com) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [SymToken.sol](https://github.com/symverse-lab/SmartContract/blob/master/SymToken.sol) github commit hash d38e09b7c90763c283215c6da043e720b324f98f.

# 3. Findings

In total, **6 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Decrease Allowance

### Severity: low

### Description

`decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

https://github.com/RideSolo/SmartContract-1/blob/master/SymToken.sol#L244

## 3.3. Owner Privileges

### Severity: owner privileges

### Description

1. Lock/Unlock token transfers and approvals [code snippet](https://github.com/RideSolo/SmartContract-1/blob/master/SymToken.sol#L450#L460).
2. Lock/Unlock tokens on every possible account address by the contract owner or delegator [code snippet](https://github.com/RideSolo/SmartContract-1/blob/master/SymToken.sol#L408#L447).

## 3.4. Owner minting

### Severity: medium

### Description

The owner can increase circulation supply any time he wants, but `_totalSupply` value will not increase. It may cause the situation when actual circulation supply higher than `_totalSupply`. The owner can mint tokens to himself using functions `increaseCirculation` and `increaseAllowance`.

### Code snippet

https://github.com/symverse-lab/SmartContract/blob/d38e09b7c90763c283215c6da043e720b324f98f/SymToken.sol#L368

https://github.com/symverse-lab/SmartContract/blob/d38e09b7c90763c283215c6da043e720b324f98f/SymToken.sol#L229

## 3.5. ERC20 Compliance.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

### Code snippet

https://github.com/symverse-lab/SmartContract/blob/d38e09b7c90763c283215c6da043e720b324f98f/SymToken.sol#L469

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/de893de91b8a0f5e185d930329376518

https://gist.github.com/yuriy77k/f5c755acf16e756b7629c3a939281ec6

https://gist.github.com/yuriy77k/ae07aaf82f84214b3a53bdc69d929aac
