# 1. Summary

[btcex.pro ICO](https://github.com/ehussain/btcex-contracts-flat/blob/master/BXC.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BXC.sol]( https://github.com/ehussain/btcex-contracts-flat/blob/master/BXC.sol) github commit hash d878fea326892f8136d90c31f792618828715601.

# 3. Findings

In total, **4 issues** were reported including:

- 2 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```
## 3.2. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.24. It is suggested to use the latest version and fix all compiler warnings that arise.

## 3.3. Deprecated method.

### Severity: minor observation

### Description

The function () payable { revert(); } was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded.
 
## 3.4. No zero address checking in `mint` function

### Severity: low

### Description

There is no zero address checking in the function [`mint`](https://github.com/ehussain/btcex-contracts-flat/blob/master/BXC.sol#L351).

### Recommendation

Add zero address checking
```solidity
require(to != address(0));
```

# 4. Conclusion

The audited contract has no any critical issues. Some low severity issues have to be addressed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/d95f8c7e7fe529058b5ddc97984ef726

https://gist.github.com/yuriy77k/ef303cdf836b3acccf3015eef4492362

https://gist.github.com/yuriy77k/2e389548a1e811041dad11cacea6b7de
