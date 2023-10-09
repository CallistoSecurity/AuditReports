# Idex Security Audit Report

# 1. Summary

[Idex](https://github.com/AuroraDAO/idex) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 4a05eb28e570e9820066474ff2adc924ce7a27bd.

- [DVIP.sol](https://github.com/AuroraDAO/idex/blob/master/DVIP.sol).
- [Exchange.sol](https://github.com/AuroraDAO/idex/blob/master/Exchange.sol).
- [ExchangeWhitelist.sol](https://github.com/AuroraDAO/idex/blob/master/ExchangeWhitelist.sol).
- [MyToken.sol](https://github.com/AuroraDAO/idex/blob/master/MyToken.sol).

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

## 3.1. ERC20 Compliance: `transfer` function returns nothing

### Severity: medium

### Description
ERC-20 Token [Standard specifies](https://eips.ethereum.org/EIPS/eip-20) for function transfer.
The current convention is for ERC20 tokens to revert if there's an error and return true if there isn't, because this is safest should work for everyone. 
But in this implementation transfer function returns 0 bytes is violating the ERC20 interface. 

The biggest risk is that a smart contract that is compiled with solc ≥ 0.4.22 , which is expecting an ERC20 interface, will not be able to interact with MyTokens. This could mean that tokens which are send to such a contract, will be stuck there forever even if the contract has a function to transfer ERC20 token. There are many different scenarios where contracts, handling ERC20 tokens would run into this bug. One example is, that you would not be able to use decentralized exchanges that compiled its contract with solc ≥ 0.4.22 with this implementation of MyToken.
More details [here](https://medium.com/coinmonks/missing-return-value-bug-at-least-130-tokens-affected-d67bf08521ca).

### Code snippet

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/MyToken.sol#L34

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```
## 3.3. ERC20 Compliance.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

### Code snippet

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/MyToken.sol#L26

## 3.4. Check for an empty input value.

#### Severity: low

### Description

There is no check for an empty input value.

### Code snippet

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/Exchange.sol#L42

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/Exchange.sol#L49 

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/Exchange.sol#L53

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/ExchangeWhitelist.sol#L33

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/ExchangeWhitelist.sol#L40

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/ExchangeWhitelist.sol#L44

## 3.5. Deprecated method.

### Severity: minor observation

### Description

The function () { throw; } was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded.

### Code snippet

https://github.com/AuroraDAO/idex/blob/4a05eb28e570e9820066474ff2adc924ce7a27bd/MyToken.sol#L72-L74

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/cf776b11b3297a0b0a46e50349ecc78a

https://gist.github.com/yuriy77k/dc45fb5987564479eb29e58b0d485157

https://gist.github.com/yuriy77k/cf2e2fb5c5177f1e3e42c92eee54bc7d
