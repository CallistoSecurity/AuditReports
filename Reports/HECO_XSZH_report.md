# XSZH Token Security Audit Report

# 1. Summary

[XSZH Token](https://hecoinfo.com/address/0x53d97ADC423D49813dC874D9D4EB1fD8D8c703EE#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> XSZH is a decentralized Extra Strong Zero Hora game platform token supported by the Huobi ecological chain. It is also truly applied to online games and solves the problems of high fees and low trust in games. In fact, games are the most important vertical application field of smart contract technology. XSZH introduces blockchain technology into the game field.  

# 2. In scope

https://hecoinfo.com/address/0x53d97ADC423D49813dC874D9D4EB1fD8D8c703EE#code

# 3. Findings

In total, **1 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.
