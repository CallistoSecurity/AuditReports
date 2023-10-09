# 1. Summary

[TAN Token](https://etherscan.io/address/0x2c36204a0712a2a50e54a62f7c4f01867e78cb53#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [TANToken.sol](https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852)

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

No critical security issues were found.

## 3.1. Transfer to Address 0x0

### Severity: low

### Description

`transfer` and `tranferFrom` functions allow transfers to 0x0 address.

### Code snippet

https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852#file-tantoken-sol-L131

https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852#file-tantoken-sol-L190

### Recommendation

Use condition like `require(to != address(0))`.

## 3.2. Naming Convention

### Severity: minor

### Description

`destroy` function name should be changed to `burn`, `Destruction` event name should be changed to `Burn`.

### Code snippet

https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852#file-tantoken-sol-L233

## 3.3. Multi Transfer overflow

### Severity: medium

### Description

Overflow is possible. For example, there are two input amounts: `2^128` and `2^128`. In this case `totalAmount` will be `2^256` and overflowed to `1`. Thus, the owner will spend just 1 token, but will accrue to two recipients of `2^128` tokens for each.

### Code snippet

https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852#file-tantoken-sol-L265

https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852#file-tantoken-sol-L275

### Recommendation

Add and use `safeMath` library instead of `+` and `-`.

## 3.4. Multi Transfer arrays length check.

### Severity: low

### Description

There are two input arrays, but no check for the same length. In this case it is possible to skip some recipient or token amount and to transfer wrong amount to wrong recipient.

### Code snippet

https://gist.github.com/yuriy77k/42460f95812ed48272e7a9d2a0fe1852#file-tantoken-sol-L259-L265

### Recommendation

Check the length of both input arrays. For example: `require(_recipients.length == _tokenAmounts.length);`

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

# 4. Conclusion

There are some vulnerabilities were found in this contract. They have to be fixed before deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/fa64e9bfdbf31b6ba8eccfb2b6dae18d

https://gist.github.com/yuriy77k/50b549c667eec53693bd5116c9ef199f

https://gist.github.com/yuriy77k/88636e3d3b05cdff491d2fe567a65282
