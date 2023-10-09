# Security Audit Report

# 1. Summary

[Binance token (BNB)](https://etherscan.io/address/0xB8c77482e45F1F44dE1745F52C74426C631bDD52#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

[bnb.sol](https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166)

# 3. Findings

In total, **5 issues** were reported including:

- 5 low severity issues.

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

## 3.2. ERC20 Compliance — event missing

### Severity: low

### Description

1. According to ERC20 standard when coins are minted a `Transfer` event should be emitted.

2. There is no `Approval` event call at `approve` function. And EIP20 says: 
> MUST trigger on any successful call to approve(address _spender, uint256 _value).

3. The `burn` function also should emit the `Transfer` event.

### Code snippet

1. [initial supply](https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166#file-bnb-sol-L68)
2. [Approval event](https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166#file-bnb-sol-L88)
3. [burn function and Transfer event](https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166#file-bnb-sol-L110)

## 3.3. ERC20 Compliance — zero-value transfers rejecting

### Severity: low

### Description

EIP20 says that:
> Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.
But in this contract, function `transfer` has a condition:
```solidity
if (_value <= 0) throw;
```
### Code snippet

* [0 value transfer issue](https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166#file-bnb-sol-L99)

## 3.4. ERC20 Compliance — `approve` issues

### Severity: low

### Code snippet

* [approve issue](https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166#file-bnb-sol-L90)

### Description

1. There is no way to reset approved value to 0, because `approve` function contains:
```solidity
if (_value <= 0) throw; 
```

2. Also it breaks the EIP20 security recommendation:
> To prevent attack vectors like the one described [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/) and discussed [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729), clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn't enforce it, to allow backwards compatibility with contracts deployed before

## 3.5. Fallback Function

### Severity: low

### Description

Any ether sent directly to the contract through the fallback function will not result in an automatic buy of tokens but instead is directly sent to the contract balance. 

### Code snippet

https://gist.github.com/yuriy77k/7612e9b1f915f169542e711dd6a29166#file-bnb-sol-L144

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b228e8df2aa47aef84e91423dd340894

https://gist.github.com/yuriy77k/ee463004023c4caee3b36bea30deb32f

https://gist.github.com/yuriy77k/b15d652f64211b51711f0648cdab6000
