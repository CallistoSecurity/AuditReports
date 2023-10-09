# Security Audit Report

# 1. Summary

[Maker (MKR)](https://etherscan.io/address/0x9f8f72aa9304c8b593d555f12ef6589cc3a579a2#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Audit Top 200 CoinMarketCap tokens.

Maker | MKR
--|--
Circulating Supply | 1 000 000
Total Supply | 1 000 000
Max Supply | No Data

https://makerdao.com/

# 2. In scope

1. [DSToken.sol](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol)

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

### Code snippet

1. [initial supply](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L333); [mint function](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L454)
2. [burn function and Transfer event](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L458)

### Description

1. According to ERC20 standard when coins are minted a `Transfer` event should be emitted.
2. The `burn` function also should emit the `Transfer` event.

## 3.3. It is necessary to check the input address of `transfer` function.

### Severity: low

### Code snippet

* [transferFrom](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L360)
* [setOwner](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L42)

### Description

1. In the `transfer` and `transferFrom` functions, input destination address is not checked for a null value and the funds can be transferred to a `0x0`-address.
2. Also it is needed to check input address for `setOwner` and `setAuthority` function.

### 3.4. Default `approve` value

### Severity: low

### Code snippet

* [default approve value](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L413)

### Description

In case if the `approve` function is called with only "beneficiary" address parameter then max-uint value(!) of token will be approved to recipient.

Also the approved value [doesn't decrease](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L425) when `trnsferFrom` called in case of max-uint approved value. It is some sort of ERC20 discrepancy.

## 3.5. Owner's Privileges

### Severity: low

### Code snippet

* [stoppable](https://gist.github.com/yuriy77k/527895b7b7a1c00c867970cc9450aa60#file-dstoken-sol-L259-L268)

### Description

The contract owner allow himself to pause functions of contract (`transfer`, `transferFrom`, `approve`, `mint`, `burn`).

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/02f341422e1fef9497e952914df2094f

https://gist.github.com/yuriy77k/d79699c49391902276555b5c731c8ead

https://gist.github.com/yuriy77k/1a7589c329e88736d9066effde0fc902
