# APO1 token Security Audit Report

# 1. Summary

[APO1 token](https://github.com/geomad/APO/blob/master/apo.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> ERC-20 token. Supply is fixed and set from the start. In every transaction 1% gets burned and 1% gets transfered to a charity address. The contract is owned. Only the owner should be able to set a new owner, or change the charity address.

# 2. In scope

Commit hash: `7b18c7689fc90bbbb5fa092c02ae9fd75e1074d7`

1. [apo.sol](https://github.com/geomad/APO/blob/7b18c7689fc90bbbb5fa092c02ae9fd75e1074d7/apo.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 4 low severity issues.

- 1 notes.

-  owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

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
## 3.2. Checking input addresses

### Severity: low

### Description

Incoming addresses should be checked for an empty value(`0x0` address) to avoid loss of funds or blocking some functionality.

### Code snippet

* [`_mint()`](https://github.com/geomad/APO/blob/7b18c7689fc90bbbb5fa092c02ae9fd75e1074d7/apo.sol#L207)


## 3.3. Should compared the length of the input arrays

### Severity: low

### Description
Input arrays of `multiTransfer` may have different length by accidentally. This can lead to incorrect sending of funds to many recipients.

### Code snippet

* [multiTransfer()](https://github.com/geomad/APO/blob/7b18c7689fc90bbbb5fa092c02ae9fd75e1074d7/apo.sol#L156)

### Recommendation

Use `require(receivers.length == amounts.length)`.

## 3.4. Decrease Allowance

### Severity: low

### Description

The function `decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

* [decreaseAllowance()](https://github.com/geomad/APO/blob/7b18c7689fc90bbbb5fa092c02ae9fd75e1074d7/apo.sol#L200)

## 3.5. Payable constructor

### Severity: note

### Description

The constructor allows to deposit Ether to the contract, but there is no way to withdraw these funds. Possible loss of funds.

### Code snippet

* [constructor()](https://github.com/geomad/APO/blob/7b18c7689fc90bbbb5fa092c02ae9fd75e1074d7/apo.sol#L112)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5c8b6aff09a72d77a4ed79eb1777dffa

https://gist.github.com/yuriy77k/0db797fb7490cf4427806668e7410910

https://gist.github.com/yuriy77k/2f295ea94053e8fbb1fdc01b2ec8b019
