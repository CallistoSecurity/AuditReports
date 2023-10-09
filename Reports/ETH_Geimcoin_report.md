# 1. Summary

[Geimcoin](https://etherscan.io/address/0x2e9ef342f50a10e87ddad06d0fc6d3f0223726c9#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

* [GEIMCOIN.sol](https://gist.github.com/yuriy77k/9a7719d0d7cc168da0b7eb22ed387c89#file-geimcoin-sol)

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

Add into a function `transfer(address _to, ... )` following code:

```
require( _to != address(this) );

```

## 3.2. No checking for zero address.

### Severity: low

### Description

Functions `transfer` and `transferFrom` do not prevent from sending tokens to address 0x0.

### Code snippet

https://gist.github.com/yuriy77k/9a7719d0d7cc168da0b7eb22ed387c89#file-geimcoin-sol-L43

https://gist.github.com/yuriy77k/9a7719d0d7cc168da0b7eb22ed387c89#file-geimcoin-sol-L53

### Recommendation
Add zero address checking

```
require(to != address(0));

```

## 3. Non-compliance with ERC-20 Token Standard.

### Severity: medium

### Description

1. [ERC-20 Token Standard](https://eips.ethereum.org/EIPS/eip-20) specifies for functions `transfer` and `transferFrom`:

> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

But in this implementation it just returns `false`. This can lead to serious consequences. Because checking the return value of this function is rare.

2. According to ERC20 standard when coins are minted a `Transfer` event should be emitted.

### Code snippet

https://gist.github.com/yuriy77k/9a7719d0d7cc168da0b7eb22ed387c89#file-geimcoin-sol-L50

https://gist.github.com/yuriy77k/9a7719d0d7cc168da0b7eb22ed387c89#file-geimcoin-sol-L61

https://gist.github.com/yuriy77k/9a7719d0d7cc168da0b7eb22ed387c89#file-geimcoin-sol-L101

#### Recommendation

1. Use `throw;` instead of `return false;` in `transfer` and `transferFrom` functions.

2. Add `Transfer` event to the constructor.


# 4. Conclusion

There are no direct exploitable issues, but a smart contract is not fully compatible with ERC-20 Token Standard. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/a6178ce37b19431b34a58d25d1e608d2

https://gist.github.com/yuriy77k/25770d7b05bbe332f048460d1caadd79

https://gist.github.com/yuriy77k/7d53f13f65bc9eb529cdfdb736cf0e81
