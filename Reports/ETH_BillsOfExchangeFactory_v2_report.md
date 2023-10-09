# Bills of exchange factory v.2 Security Audit Report

# 1. Summary

[Bills of exchange factory v.2](https://ropsten.etherscan.io/address/0x74eB4DBD3124D41B6775701FD1821571EAd5cf9A#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> 'Bills Of Exchange Factory' is a smart contracts based service that allows user to draw electronic bills or exchange.

https://cryptonomica.net/bills-of-exchange/

# 2. In scope

https://ropsten.etherscan.io/address/0x74eB4DBD3124D41B6775701FD1821571EAd5cf9A#code

# 3. Findings

In total, *2 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

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

## 3.2. ERC223 Compliance: The `tokenFallback` function is not called when it should be

### Severity: medium

### Description

The main idea of ERC223 is to protect the tokens from loss by sending to a contract that is not intended to work with ERC20 tokens. [Look at Motivation of ERC223](https://github.com/ethereum/EIPs/issues/223). But here the main `transfer(address, uint256)` function is not implement `tokenFallback()` mechanism.

### Code snippet

* [transfer(), line 251](https://etherscan.io/address/0xAA2CB1a1b014b92EDb93B1f163C7CA40a07EEcAa#contracts)

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/adce899cb0b55cb4d52f34a0909ff965

https://gist.github.com/yuriy77k/9b945659e8dff3dd4b2ccb19725d0421

https://gist.github.com/yuriy77k/b339a49de33c72893fc102e2368e6833
