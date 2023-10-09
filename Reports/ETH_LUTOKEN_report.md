# LUTOKEN Security Audit Report

# 1. Summary

[LUTOKEN](https://github.com/CINEMADROM/SMART-CONTRACT/blob/master/LUTOKEN.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Smart contract Token ERC20

https://cinemadrom.com/

# 2. In scope

Commit hash: `efdf556013c20b225ae31261ef95d8911e0b37fe`

1. [LUTOKEN.sol](https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol)

**Notice:** `UpgradeAgent.upgradeFrom()` method is not audited because the lack of method code here.

# 3. Findings

In total, **6 issues** were reported including:

- 3 low severity issues.

- 3 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. ERC20 Compliance: event missing

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a `Transfer` event should be emitted.
An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol#L585

## 3.3. Owner privileges

#### Severity: owner privileges

#### Description

1. Owner can `pause` transfers any time he wants.
2. Owner can `freeze` at any time he wants.
3. Owner can upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

### Code snippet

https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol#L100

https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol#L622

https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol#L503

## 3.4. Required check for an 0x0 address

### Severity: low

### Description

- There is no zero address checking in `constructor`.
- It is possible to remain out of contract control by accidentally calling `setSale` function without parameter.

### Code snippet

https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol#L477

https://github.com/CINEMADROM/SMART-CONTRACT/blob/efdf556013c20b225ae31261ef95d8911e0b37fe/LUTOKEN.sol#L617

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0c0b5327bfb28a5f061e3773af2b44d0

https://gist.github.com/yuriy77k/b80c07a36e5c276351c89c3bb535aab7

https://gist.github.com/yuriy77k/c34b18d43ab2d60a02f195061ae1a4d5
