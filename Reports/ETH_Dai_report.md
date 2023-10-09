# Dai Security Audit Report

# 1. Summary

[Dai](http://www.makerdao.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Audit Top 200 CoinMarketCap tokens.
> Dai (DAI) stablecoin.

http://www.makerdao.com/

# 2. In scope

1. [DaiToken.sol](https://etherscan.io/address/0x89d24a6b4ccb1b6faa2625fe562bdd9a23260359#code)

# 3. Findings

In total, **4 issues** were reported including:

- 3 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. Blocking transferring

### Severity: owner privileges

### Description

The contract owner allowed to block transfer functions( `transferFrom`, `approve`, `mint`, `burn`).

### Code snippet

* Line 234.

## 3.3. ERC20 Compliance — event missing

### Severity: low

### Description

According to ERC20 standard when coins are minted(or burned) a `Transfer` event should be emitted.

### Code snippet

* Lines 423, 428, 303.

## 3.4. Checking input addresses

### Severity: low

### Description

Incoming addresses should be checked for an empty value(`0x0` address) to avoid loss of funds or blocking some functionality.

### Code snippet

- **setOwner** function (lines 129-135)
- **transferFrom** function (lines 390-405)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/bf2ea7c611b07073262d216d05de3b30

https://gist.github.com/yuriy77k/8cc19398ee91c3dd236f30a5b91c2d97

https://gist.github.com/yuriy77k/fbdccd8b26e383f9592153263f1a7c4e
