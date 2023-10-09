# Wildfire Token (v2) Security Audit Report

# 1. Summary

[Wildfire (WDF) Token](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/main/Wildfire.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Telegram https://t.me/WildfireOfficial
- Website https://wildfire-coin.com/

# 2. In scope

Commit `d67d766653a5b403d3836209b955b7f7a5a94c5c`

- [Wildfire.sol](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/d67d766653a5b403d3836209b955b7f7a5a94c5c/Wildfire.sol)

# 2.1 Excluded

The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.


# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 1 owner privileges.

- 1 note.

## 3.1. Known vulnerabilities of ERC-20 and BEP-20 token

### Severity: low

### Description

1. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Owner privileges

### Severity: owner privileges

### Description

- Owner can [include](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/d67d766653a5b403d3836209b955b7f7a5a94c5c/Wildfire.sol#L426-L437) and [exclude](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/d67d766653a5b403d3836209b955b7f7a5a94c5c/Wildfire.sol#L414-L424) any account to/from reward.
- Owner can [include](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/d67d766653a5b403d3836209b955b7f7a5a94c5c/Wildfire.sol#L409-L411) and [exclude](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/d67d766653a5b403d3836209b955b7f7a5a94c5c/Wildfire.sol#L464-L466) any account to/from fees.

## 3.3. Unused variables

### Severity: note

### Description

The variables [_maxTxAmount, _maxWalletToken, _numTokensSellToAddToLiquidity](https://github.com/Wildfire-new/Wildfirebeb20fix/blob/d67d766653a5b403d3836209b955b7f7a5a94c5c/Wildfire.sol#L222-L224) declared but unused in the code.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

