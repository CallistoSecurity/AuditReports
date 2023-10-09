# ForkDelta Security Audit Report

# 1. Summary

[ForkDelta](https://forkdelta.app) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ForkDelta.sol](https://github.com/forkdelta/smart_contract/blob/master/contracts/ForkDelta.sol) github commit hash bfeddd2ab33f78d1ad428370f5752f9a5853db1a.
- [IToken.sol](https://github.com/forkdelta/smart_contract/blob/master/contracts/IToken.sol) github commit hash 049a7aeca378d3891be491400d4b351477335ba3.
- [LSafeMath.sol](https://github.com/forkdelta/smart_contract/blob/master/contracts/LSafeMath.sol) github commit hash 049a7aeca378d3891be491400d4b351477335ba3.
- [SampleToken.sol](https://github.com/forkdelta/smart_contract/blob/master/contracts/test/SampleToken.sol) github commit hash 049a7aeca378d3891be491400d4b351477335ba3.

# 3. Findings

In total, **5 issues** were reported including:

- 1 high severity issues.

- 2 low severity issues.

- 2 notes.

## 3.1. ERC223 Compliance

### Severity: High

### Description

Even if `ForkDelta` contract contain `tokenFallback` function, it does not make the exchange ERC223 compatible.

ERC223 interface is define [here](https://github.com/Dexaran/ERC223-token-standard/blob/master/token/ERC223/ERC223_interface.sol) and as we can see ERC223 does not implement an `approve/transferFrom` mechanism, meaning that a token that do not implement a mix ERC20/ERC223 will not be traded on forkdelta.

Following the implementation of `tokenFallback` only a call that was initiated from `depositToken` or `depositTokenForUser` allow the successful execution of `tokenFallback` since `depositingTokenFlag` will be set to true then to false inside either `depositToken` or `depositTokenForUser` and as we can see in both function `transferFrom` is called, in ERC223 `transferFrom` is not implemented and if a token implement `transferFrom` it shouldn't call `tokenFallback` since `transferFrom` is part of `ERC20` and not `ERC223`. Developers should understand one of the main goal or `ERC223` described [here](https://github.com/ethereum/eips/issues/223#issuecomment-284245613).

### Code snippet

https://github.com/RideSolo/smart_contract-1/blob/master/contracts/ForkDelta.sol#L143#L152

https://github.com/RideSolo/smart_contract-1/blob/master/contracts/ForkDelta.sol#L126#L133

https://github.com/RideSolo/smart_contract-1/blob/master/contracts/ForkDelta.sol#L429#L437

### Recommendation

When a user calls ERC223 `transfer` to transfer tokens to forkdelta `tokenFallback` should handle the token deposit by checking if the amount deposited was added to the contract balance then add it to `tokens` mapping (the internal user balance).

## 3.2. Burn From

### Severity: notes (high)

### Description

The function `burnFrom` In `SampleToken` contract, allow an address to burn from another address that has approved token to it, however the burned value is not subtracted from the allowance once the function is executed, making the spender able to burn the total balance of the from address. 

The impact of this issue cannot be defined accurately since the usage of the contract containing the issue should be described by the development team, in all the cases the contract should be corrected since the repository is public.

### Code snippet

https://github.com/RideSolo/smart_contract-1/blob/master/contracts/test/SampleToken.sol#L85#L92

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.4. Contract changing

#### Severity: low

#### Description

`migrateFunds` function give access to user to transfer his funds to any contract, but this contract could be with critical mistakes. 

### Code snippet

https://github.com/forkdelta/smart_contract/blob/master/contracts/ForkDelta.sol#L380

## 3.5. Zero address checking

### Severity: note

### Description

There is no zero address checking at `constructor`.

### Code snippet

https://github.com/forkdelta/smart_contract/blob/f2fc60430a94840659f19df324a5f31a0c799ffc/contracts/ForkDelta.sol#L42

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/3e314be121289d127b7b5b318222afdd

https://gist.github.com/yuriy77k/468188ce8c1489b6e5ab962567a77b65

https://gist.github.com/yuriy77k/20d78c18a32bf66c40675816ddf9cdb7
