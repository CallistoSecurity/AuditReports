# Huobi Token (HT) Security Audit Report

# 1. Summary

[Huobi Token (HT)](https://www.hbg.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Audit Top 200 CoinMarketCap tokens.

https://www.hbg.com/

# 2. In scope

1. [HT.sol](https://etherscan.io/address/0x6f259637dcd74c767781e37bc6133cd6a68aa161#contracts)

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

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

## 3.2. ERC20 Compliance: `false` instead of `throw`

### Severity: medium

### Description

From ERC-20 specification:
> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

But in this implementation it just returns `false`. This can lead to serious consequences. Because checking the return value of this function is rare.
For example, external contract may use this token contract as:
```solidity
HTToken.transferFrom(recipient, this, value);
points[recipient] += value;
```
In this case recipient can get any value of points, but he may not have enough money and the code will succeed.

### Code snippet

* `transfer()`, line 53
* `transferFrom()`, line 63

### Recommendation

> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

## 3. Zero address checking

### Severity: low

### Description

There is no zero address checking in functions `transfer` and `transferFrom`.

# 4. Conclusion

The audited smart contract has medium severity issue and can not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/e0fe9298cf3330928d79d241f1b196a5

https://gist.github.com/yuriy77k/6e75d4bf6a55db266359de2c77781ce5

https://gist.github.com/yuriy77k/cc9231e2b5ad610bc2bc197971184868
