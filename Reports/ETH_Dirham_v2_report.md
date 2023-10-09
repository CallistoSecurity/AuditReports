# Dirham Token Security Audit Report v.2

# 1. Summary

[@openzeppelin/contracts-ethereum-package Token](https://github.com/DirhamCrypto/DirhamToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> Dirham is a fiat collateralized stablecoin backed by AED.
> It is native to Dirham crypto where bonds are introduced to blockchain for the first time ever. Dirham holders may earn interest. Paying interest done by calling the rebase function in the smart contract.

- https://dirhamcrypto.io
- https://twitter.com/DirhamCrypto

# 2. In scope

Commit `d67a5f947ae0eaa8000021e8493181eb9475b1ad`

- [Dirham.sol](https://github.com/DirhamCrypto/DirhamToken/blob/d67a5f947ae0eaa8000021e8493181eb9475b1ad/contracts/Dirham.sol)
- [ERC20Detailed.sol](https://github.com/DirhamCrypto/DirhamToken/blob/d67a5f947ae0eaa8000021e8493181eb9475b1ad/contracts/ERC20Detailed.sol)

## 2.1. Excluded
Openzeppelin library:
- [@openzeppelin/contracts-ethereum-package](https://github.com/DirhamCrypto/DirhamToken/tree/d67a5f947ae0eaa8000021e8493181eb9475b1ad/contracts/%40openzeppelin/contracts-ethereum-package)

## 3.1. Owner privileges

### Severity: owner privileges

### Description

Smart contract owners use the [Gnosis](https://gnosis-safe.io/) multisig wallet that increases private key security.
Owner may:
1. Owner has `DEFAULT_ADMIN_ROLE` and can set/remove `MINTER_ROLE` and `REBASER_ROLE` to any address.
2. User with `MINTER_ROLE` [can mint](https://github.com/DirhamCrypto/DirhamToken/blob/d67a5f947ae0eaa8000021e8493181eb9475b1ad/contracts/Dirham.sol#L45) any amount of tokens.
3. User with `REBASER_ROLE` [can set rebase factor](https://github.com/DirhamCrypto/DirhamToken/blob/d67a5f947ae0eaa8000021e8493181eb9475b1ad/contracts/Dirham.sol#L72-L75) to any value without restriction and can call function [rebase()](https://github.com/DirhamCrypto/DirhamToken/blob/d67a5f947ae0eaa8000021e8493181eb9475b1ad/contracts/Dirham.sol#L65-L70) at any time.

These owners' privileges are required for the functionality of stablecoin.

# 4. Conclusion

The audited smart contract can be deployed. 
The issues that were pointed in the previous audit report were fixed.

# 5. Revealing previous audit reports

https://gist.github.com/yuriy77k/36b16c93cd3c3a3bdf52cc79005bce07


