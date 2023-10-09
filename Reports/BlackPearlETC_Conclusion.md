# 1. Summary

[Black Pearl ETC](https://etherhub.io/addr/0xc2dc5e825faeb9bd274b61a2993f4f8949af4a21) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BlackPearlETC.sol](https://gist.github.com/yuriy77k/787e38d7dac94ed90935f3a960a11c9b)

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerability of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://gist.github.com/RideSolo/c79e1ca77a2a78b9a17c67745522fc12#file-blackpearletc-sol-L205#L242

## 3.2. Zero address transfer. 

### Severity: low

### Description

There are possibility of sending to zero address at function [`transfer`](https://gist.github.com/yuriy77k/787e38d7dac94ed90935f3a960a11c9b#file-blackpearletc-sol-L205)

### Recommendation

Add zero address checking
```solidity
require(_toAddress != address(0))
```

## 3.3. Wrong event name. 

### Severity: minor observation

### Description

All the events name should start with upper letter. 
Examples: [`onTokenPurchase`](https://gist.github.com/yuriy77k/787e38d7dac94ed90935f3a960a11c9b#file-blackpearletc-sol-L27), [`onTokenSell`](https://gist.github.com/yuriy77k/787e38d7dac94ed90935f3a960a11c9b#file-blackpearletc-sol-L36), [`onReinvestment`](https://gist.github.com/yuriy77k/787e38d7dac94ed90935f3a960a11c9b#file-blackpearletc-sol-L44), [`onWithdraw`](https://gist.github.com/yuriy77k/787e38d7dac94ed90935f3a960a11c9b#file-blackpearletc-sol-L50)

# 4. Conclusion

The audited contract is safe.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c21bf6a8d276867bd091616a34b4d35c

https://gist.github.com/yuriy77k/55ca39b3d9014045783e023f24f8bdc7

https://gist.github.com/yuriy77k/852eb6ec5d87ae0faf521ef738d3de09
