# 1. Summary

[BPER Token](https://etherscan.io/address/0x278ae6f52ce72b0df6cba451534b87846acc9d65#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

https://blackpanthercrypto.com/

# 2. In scope

* [BPER.sol](https://gist.github.com/yuriy77k/956d26c5e91e7fd13f5f2268ccb12922#file-bper-sol)

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.2. Required check for an `0x0` address

### Severity: low

### Description

It is possible to send tokens to 0x0 address by anciently.

### Code snippet

https://gist.github.com/yuriy77k/956d26c5e91e7fd13f5f2268ccb12922#file-bper-sol-L165
https://gist.github.com/yuriy77k/956d26c5e91e7fd13f5f2268ccb12922#file-bper-sol-L133

### Recommendation

Use condition like `require(to != address(0))`.

### 3.3. Anyone can get owner's rights

### Severity: medium

### Description

`Owned2()` is not a constructor of the contract and it's should be called manually. But it is `public` method and anyone can call it and become an owner. And owner can [get any accidentally sent ERC20 tokens](https://gist.github.com/yuriy77k/956d26c5e91e7fd13f5f2268ccb12922#file-bper-sol-L208).

### Code snippet

https://gist.github.com/yuriy77k/956d26c5e91e7fd13f5f2268ccb12922#file-bper-sol-L64

### Recommendation

Use `constructor` for this purpose.

## 3.4. Version of solidity not specified.

### Severity: minor observation

### Description

The contracts use unknown version solidity. It is suggested to use the latest version and fix all compiler warnings that arise. 

## 3.5. Deprecated method.

### Severity: minor observation

### Description

The fallback `function () payable { revert(); }` was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

### Code snippet

https://gist.github.com/yuriy77k/956d26c5e91e7fd13f5f2268ccb12922#file-bper-sol-L199-L201

# 4. Conclusion

The audited smart contract can be used, however some issues were found.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f9b79c53ad317d103190b45952b8cb5f

https://gist.github.com/yuriy77k/6984e42571f46426264aea8a408b7129

https://gist.github.com/yuriy77k/f703ffaa3c7a472e1901ed9b7218196c

