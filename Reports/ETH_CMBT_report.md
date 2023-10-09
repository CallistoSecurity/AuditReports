# CMBT Token Security Audit Report

# 1. Summary

[CMBT Token](https://cmbusinesstoken.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> COINMARKETBRASIL PROVES THE DECENTRALIZATION OF OPERATIONS IN ERP SYSTEMS, VALIDATING DATA IN A RELIABLE, SAFE AND EFFICIENT WAY, BY LEVERAGING AUTOMATED SMART CONTRACTS TO ENTERPRISE SYSTEMS, USING BLOCKCHAIN PROTOCOLS.

# 2. In scope

1. [CMBT.sol](https://etherscan.io/address/0x3edd235c3e840c1f29286b2e39370a255c7b6fdb#code)

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

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

## 3.2. ERC20 Compliance — zero-value transfers rejecting

### Severity: medium

### Description

EIP20 says that:  Transfers of 0 values **MUST** be treated as normal transfers and fire the Transfer event.

    But in this contract, function transfer has a condition:
       
     require (_value > 0) throw;

### Code snippet

Lines 97, 110.

transfer(), transferFrom().

## 3.3. ERC20 Compliance.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

### Code snippet

Line 82.
```Solidity
        function CMBToken() {
	        balances[msg.sender] = _totalSupply;
        }
```

## 3.4. Zero address checking

### Severity: low

### Description

In functions `transfer` and `transferFrom` there are no zero address checking.

# 4. Conclusion

The audited smart contract is not completely compatible with ERC20 standard. Found issues have to be fixed prior to deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/01a43c61413c73b1a2bdccd69fb6fe04

https://gist.github.com/yuriy77k/33f9ea2bb8329e371b31ac336fcac4d8

https://gist.github.com/yuriy77k/b7a7681dff8762b83f056d823188ec60
