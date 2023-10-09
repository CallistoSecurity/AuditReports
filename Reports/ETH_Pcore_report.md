# Security Audit Report

# 1. Summary

[Pcore Token](https://etherscan.io/address/0x6aa27b3a8aab51745b7eaf53e61aba833b0f9400#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 3ebca579f6c1d65de75463736f0af193c2d7f153.

- [PPS.sol](https://github.com/PcoreOfficial/PcoreToken/blob/master/PCC.sol).

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 2 minor observation.

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
## 3.2. Anyone can get owner's rights

### Severity: medium

### Description

Owned2() is not a constructor of the contract and it's should be called manually. But it is public method and anyone can call it and become an owner. And owner can get any accidentally sent ERC20 tokens.

### Code snippet

https://github.com/PcoreOfficial/PcoreToken/blob/3ebca579f6c1d65de75463736f0af193c2d7f153/PCC.sol#L64
 
### Recommendation

Use constructor `Owned()` for this purpose.

## 3.3. Token Transfer to 0x0 address

### Severity: low

### Description

### Description

`Transfer` & `transferFrom` functions do not prevent from sending tokens to address 0x0.

### Code snippet

https://github.com/PcoreOfficial/PcoreToken/blob/3ebca579f6c1d65de75463736f0af193c2d7f153/PCC.sol#L136

https://github.com/PcoreOfficial/PcoreToken/blob/3ebca579f6c1d65de75463736f0af193c2d7f153/PCC.sol#L169

### Recommendations

Add zero address checking
```solidity
require(to != address(0));
```

## 3.4. Deprecated method.

### Severity: minor observation

### Description

The `function () payable { revert(); }` was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

### Code snippet

https://github.com/PcoreOfficial/PcoreToken/blob/3ebca579f6c1d65de75463736f0af193c2d7f153/PCC.sol#L200-L202

## 3.5. Unknown version of solidity.

### Severity: minor observation

### Description

Source file does not specify required compile version. 

# 4. Conclusion

The review did not show any critical issues, some of medium and low severity issues were found.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/031f96c602b379d9802f4059f5961111

https://gist.github.com/yuriy77k/22cc631b0a8ab38f3ceacf728ca89a87

https://gist.github.com/yuriy77k/d551928bd46247f667d33f208d53b716
