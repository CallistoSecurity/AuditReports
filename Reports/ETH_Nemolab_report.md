# Security Audit Report

# 1. Summary

[Nemolab](https://github.com/nemolab-project/nemodax-erc20/blob/master/upgradable_TokenExchanger.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8.

- [upgradable_TokenExchanger.sol](https://github.com/nemolab-project/nemodax-erc20/blob/master/upgradable_TokenExchanger.sol).

# 3. Findings

In total, **7 issues** were reported including:

- 3 medium severity issues.

- 4 low severity issues.

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
## 3.2. RunningConctractManager is vulnerable to function selector clashing

### Severity: medium

### Description

This implements upgradable contracts based on forwarding messages to an implementation contract using **DELEGATECALL**.This forwarding logic is implemented in the [Proxy’s fallback function](https://github.com/nemolab-project/nemodax-erc20/blob/master/upgradable_TokenExchanger.sol#L632), which is only called if the current message's data doesn't start with one of the Proxy's functions' selectors. This means that if the selector of one of the implementation's functions clashes with one of the Proxy's, the latter will take precedence and the message will not be forwarded. This behavior can possibly break contracts.

### Code snippet

https://github.com/nemolab-project/nemodax-erc20/blob/master/upgradable_TokenExchanger.sol#L632

## 3.3. Owner can freeze any address

### Severity: medium

### Description

`FreezeAccount` function allow the contract owner to freeze any address and disable the transfer of the contained token value of that address.

### Code snippet

https://github.com/nemolab-project/nemodax-erc20/blob/63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8/upgradable_TokenExchanger.sol#L444

https://github.com/nemolab-project/nemodax-erc20/blob/63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8/upgradable_TokenExchanger.sol#L451

## 3.4. `initExchanger` function can be called multiple times

### Severity: medium

### Description

The owner can call `initExchanger` function not only once and this will change the total supply and balance of the owner to any value (`initialSupply` parameter).

### Code snippet

https://github.com/nemolab-project/nemodax-erc20/blob/63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8/upgradable_TokenExchanger.sol#L484

### Recommendation

Check if this function was also called.

## 3.5. No check to the zero address.

### Severity: low

### Description

1. Possibility of setting zero address as `newOwner` at `transferOwnership` function.

2. There is no zero address check at function `upgrade`.

### Code snippet

https://github.com/nemolab-project/nemodax-erc20/blob/63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8/upgradable_TokenExchanger.sol#L165

https://github.com/nemolab-project/nemodax-erc20/blob/63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8/upgradable_TokenExchanger.sol#L214

## 3.6. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to:

- set any ExchangeRate;
- destroy tokens;
- withdraw tokens and ether.

The contract is managed manually by the owner which is not good for investors.

## 3.7. The relocating is not secure process for investors.

### Severity: low

### Description

The owners can implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

### Code snippet

https://github.com/nemolab-project/nemodax-erc20/blob/63dc1650051d1f2e4d4a8a01a05ea3af1b9bebe8/upgradable_TokenExchanger.sol#L214

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/461abda3e82305bc12c8bf2d92fb093e

https://gist.github.com/yuriy77k/b368f7dc356abe99943b5764a8a3801d

https://gist.github.com/yuriy77k/8b5ea8dd76af5a430125c096714f7525
