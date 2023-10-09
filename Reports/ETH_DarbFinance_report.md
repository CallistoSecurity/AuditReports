# Darb Finance Security Audit Report

# 1. Summary

[Darb Finance](https://darbfinance.com) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Darb Finance is a trading platform with it's own infernal economy based on DARB Tokens. If other information is required, please send me a message - I will gladly complete the application for missing information

# 2. In scope

1. [Darb.sol](https://etherscan.io/address/0xc224dfe42a5332a497334fadb8fed7e7aa4bdf13#contracts)

# 3. Findings

In total, **8 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

- 1 notes.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. ERC20 Compliance: zero-value transfers rejecting

### Severity: medium

### Description

EIP20 says that:
> Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.
But in this contract, function `transfer` has a condition:
```solidity
if (tokens <= 0) revert();
```

### Code snippet

* Line 110.

## 3.3. Blocking transferring

### Severity: owner privileges

### Description

The contract owner allowed to block transfer functions(`transfer`, `transferFrom`, `approve`).

### Code snippet

* Line 144.

## 3.4. ERC20 Compliance — event missing

### Severity: low

### Description

According to ERC20 standard when coins are minted(or burned) a `Transfer` event should be emitted.

### Code snippet

* Line 95.

## 3.5. The owner can mint tokens without any restrictions

### Severity: owner privileges

### Description

`mintFromTraded` function allows the owner to mint as many coins as he wants at any time without any restrictions.

## 3.6. Overflowing is possible

### Severity: note

### Description

The `mintFromTraded()` includes not safe math operations as a result overflowing is possible. This can happen for example by mistake. The consequences will be a different value of the real emission and the `totalSupply()` value.
An example:

1. Call `mintFromTraded(1111)`.
```solidity
totalSupply() = 9026875000;
balances[owner] = 9026875000;
```
2. Call `transfer(some_address, 9026875000)`.
```solidity
totalSupply() = 9026875000;
balances[owner] = 0;
balances[some_address] = 9026875000;
```
3. Call `mintFromTraded(14251334059977377898285659693376973274248613497309607881779394954818968)`.
```solidity
totalSupply() = 12235064; // !!!
balances[owner] = 14251334059977377898285659693376973274248613497309607881779394954818968;
balances[some_address] = 9026875000;
```

### Recommendation

Use `SafeMath` library for calculation `toMint` value.

## 3.7. Zero address checking

### Severity: low

### Description

There is no zero address checking in function `transferFrom`.

### Code snippet

* Line 124.

## 3.8. Contract accept payment from anyone.

### Severity: low

### Description

An anybody, who send Ether to contract address may lose it because of no payment processing in contract code.

### Code snippet

Line: 155.

         function() external payable {}

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/e5e5b1bb1b8cdcc0c4b5d21a2255bc8a

https://gist.github.com/yuriy77k/949fbb026932c8a29f39ab0a84c39ba8

https://gist.github.com/yuriy77k/10bb447e3c1f662cebb8d02734803971
