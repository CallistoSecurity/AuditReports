# Security Audit Report

# 1. Summary

[xEUR tokens](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

xEUR is a stable token than can be exchanged to fiat EUR and vice versa.
See description on https://xeuro.online and FAQ on https://xeuro.online/#!/faq

# 2. In scope

[xEuro.sol](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code)

# 3. Findings

In total, **8 issues** were reported including:

- 2 medium severity issues.

- 4 low severity issues.

- 1 notes.

- 1 owner privileges (ability of owner to manipulate contract, may be risky for investors).

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

## 3.2. Tokens can be burned multiple times on the same Id

### Severity: medium

### Description

At the end of the `burnTokens` function the `fiatOutPaymentIdsUsed[fiatOutPaymentId]` variable should be set to `true` because it is a condition of performance of burning:

```solidity
    require(!fiatOutPaymentIdsUsed[fiatOutPaymentId]);
```

### Code snippet

* [line 457](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code)
```solidity
    fiatOutPaymentIdsUsed[fiatOutPaymentId];
```

### Recommendation

Line 457 should be like following:

`fiatOutPaymentIdsUsed[fiatOutPaymentId] = true;`

## 3.3. ERC20 Compliance — event missing

### Severity: low

### Description

1. According to ERC20 standard when coins are minted(`mintTokens`) a `Transfer` event should be emitted.

2. The `burnTokens` function also should emit the `Transfer` event.

### Code snippet

* [lines 371 and 455](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code)

## 3.4. ERC20 Compliance — method missing

### Severity: medium

### Description

In the ERC-20 standard should be `approve` function, but here it is missing.

## 3.5 Malicious monarchy admin

### Severity: owner privileges

### Description

The function `removeAdmin()` gives the full authority to any admin to remove any/all of the admin(s) from the admin list.
Malicious admin can remove all the existing admins to become the owner(only admin) of the contract before he gets removed by the genuine admin.

### Code snippet

* [Line 245](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code)

### Recommendation

Implement proper access control and segregate owner from the admin.

## 3.6. Token Uses No Decimals

### Severity: note

### Description

While the specification defined the number of token decimals to be 18, no decimals were found to be used. This can cause problems when interacting with other smart contracts as tokens with 0 decimals can cause rounding errors. For example, many exchanges charge a small fee based on the tokens exchanged. As such, using no decimals will either make it impossible to list the token on these exchanges or it will result in having expensive fees compared to other tokens.

### Code snippet

* [Line 98](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code)

## 3.7. Token Transfer to 0x0 address

### Severity: low

### Description

Transfer & transferFrom functions do not prevent from sending tokens to address 0x0.

### Recommendation

Add zero address checking

```solidity
require( _to != address(0) );

```

## 3.8. It is necessary to check the input address to the zero-address.

### Severity: low

### Description

In the `addToCanMint`, `addToCanTransferFromContract`, `addToCanBurn` functions, the input address is not checked for a null value.

### Code snippet

* [Lines 268, 286, 304](https://etherscan.io/address/0xC31C61cf55fB5E646684AD8E8517793ec9A22c5e#code)

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b721589710d91e642f1298ec19045518

https://gist.github.com/yuriy77k/ed1f20d45c133fb42585b5f6ab9270b0

https://gist.github.com/yuriy77k/47b1b157319c42141eb3d3fbc316241f
