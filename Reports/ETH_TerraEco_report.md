# TerraEco Token Security Audit Report

# 1. Summary

[TerraEco Token](https://etherscan.io/address/0x63bcb9818cdd0c21dea6c83ce89f7138297b5fe2)  smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [TerraEco Token](https://etherscan.io/address/0x63bcb9818cdd0c21dea6c83ce89f7138297b5fe2#contracts).

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

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
## 3.2. Possible Overflow

### Severity: low

### Description

A possible overflow can occure if `_addedValue` is too high either by mistake or by intention.

### Recommmendation

Always use `SafeMath` when performing such operations.

### Code snippet

```
    function increaseApproval(address _spender, uint _addedValue) public returns (bool) { 
        allowed[msg.sender][_spender] = allowed[msg.sender][_spender] + _addedValue; 
        emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]); 
        return true; 
    } 
```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5a87ebd64c0a723645185266f742c4f1

https://gist.github.com/yuriy77k/d0f64ed894d80a847a431e212083e7b8

https://gist.github.com/yuriy77k/938aaa3cb6d5ff306235667c96d6a202
