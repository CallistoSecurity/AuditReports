# Cryptomillions.io Security Audit Report

# 1. Summary

[Cryptomillions.io](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> create ERC20 tokens, it has the functionality to transfer, burn tokens, destroy tokens, and pause token transfer and token destruction

https://www.cryptomillions.io/

# 2. In scope

Commit hash: `2274bf5224168da72285399fca9be14471ea7764`

1. [ERC20_CPM1_Token.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/ERC20_CPM1_Token.sol)
2. [Destructible.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/Destructible.sol)
3. [ERC20.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/ERC20.sol)
4. [Ownable.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/Ownable.sol)
5. [Pausable.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/Pausable.sol)
6. [SafeMath.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/SafeMath.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 2 medium severity issues.

- 1 notes.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. ERC20 Compliance: zero-value transfers rejecting

### Severity: medium

### Code snippet

* [`_transfer()`](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/ERC20_CPM1_Token.sol#L69)

### Description

EIP20 says that:
> Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.
But in this contract, function `transfer` has a condition:
```solidity
    require(amount != 0);
```

## 3.2. ERC20 Compliance: methods missing

### Severity: medium

### Description

There are no `tranferFrom`, `approve` and `allowance` function described in [ERC20 specification](https://eips.ethereum.org/EIPS/eip-20). This can cause problems with integration with some dApps.

## 3.3. Owner privileges

### Severity: owner privileges

### Code snippet

* [`destroy`](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/master/Destructible.sol#L13)
* [`pause`](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/master/Pausable.sol#L20)
* [`transfer()`](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/ERC20_CPM1_Token.sol#L58)

### Description

1. Owner can `destroy` contract any time without any restrictions.
2. Owner can `pause` transfer any time he wants.

## 3.4. Needless event call

### Severity: note

### Code snippet

* [`transfer()`](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1/blob/2274bf5224168da72285399fca9be14471ea7764/ERC20_CPM1_Token.sol#L75)

### Description

Strange event emitting:

`emit Transfer(sender, address(0), amount);`

# 4. Conclusion

The audited smart contract has some ERC20 noncompliance and can't be used as ERC20 token. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c8f76bb5ef99860f227cc6db8a3dc302

https://gist.github.com/yuriy77k/fe3e56b8b40c25aa02988355763cbdc3

https://gist.github.com/yuriy77k/db86b71a870cc0983b1aba667bb0f9e0
