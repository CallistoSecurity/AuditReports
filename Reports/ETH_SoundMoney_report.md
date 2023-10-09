# Sound Money Coin (SOV) Security Audit Report

# 1. Summary

[Sound Money Coin (SOV)](http://www.soundmoneycoin.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Sound Money Coin (SOV) is a sound money and a secure store of value. It's also the hardest money in existence - even harder than gold which is physically indestructible. As the hardest money, Sound Money Coin is on an unstoppable trajectory to become a global reserve currency used by the poor and wealthy alike. Its transactions are settled on the Ethereum blockchain.

# 2. In scope

Commit hash: `a65ee6d2a19a098e075502c7d19f5705f895d619`

1. [SoundMoneyCoin.sol](https://github.com/soundmoneycoin/SoundMoneyCoin/blob/a65ee6d2a19a098e075502c7d19f5705f895d619/contracts/SoundMoneyCoin.sol)

# 3. Findings

In total, **3 issues** were reported including:

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

## 3.2. Exceeding the `MAX_SUPPLY`

### Severity: low

### Description

The total number of tokens after mining is completed will be equal to 21000000.0**5 100 000** which is a little more than `MAX_SUPPLY`. This can lead to misunderstandings in some cases.

### Code snippet

https://github.com/soundmoneycoin/SoundMoneyCoin/blob/a65ee6d2a19a098e075502c7d19f5705f895d619/contracts/SoundMoneyCoin.sol#L158-L163

### Recommendation

Add checking, that total supply after minting will be less or equal `MAX_SUPPLY`.

## 3.3. Decrease Allowance

### Severity: low

### Description

The function `decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

https://github.com/soundmoneycoin/SoundMoneyCoin/blob/a65ee6d2a19a098e075502c7d19f5705f895d619/contracts/SoundMoneyCoin.sol#L112

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c3b1c7c336cc73cf52189ac40ab7aea4

https://gist.github.com/yuriy77k/cbe59c63f5701b497c21622d9ab0397d

https://gist.github.com/yuriy77k/1dbbaeb04680ba291cd916e5cacea987
