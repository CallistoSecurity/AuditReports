# 1. Summary

[PlayT Token](https://etherscan.io/address/0x677294c0e019145f595914be0ea5e5dc27974cc6#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [PlayTSmartVideoToken.sol](https://gist.github.com/yuriy77k/006c2c285f318b459fa1acfff7b9b1e0)

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. Burn Mechanism

### Severity: low

### Description

The Token implementation uses both `burn`, `burnFrom` functions and normal transfer to address 0x0 as burn mechanisms.
Sending token to address 0x0 can be done by mistake, the destination address should be checked to be different than 0x0 before allowing the transfer, especially with this special implementation case since there is a burn functionality.

### Code snippet

https://gist.github.com/yuriy77k/006c2c285f318b459fa1acfff7b9b1e0#file-playtsmartvideotoken-sol-L94#L96

https://gist.github.com/yuriy77k/006c2c285f318b459fa1acfff7b9b1e0#file-playtsmartvideotoken-sol-L141

https://gist.github.com/yuriy77k/006c2c285f318b459fa1acfff7b9b1e0#file-playtsmartvideotoken-sol-L150

https://gist.github.com/yuriy77k/006c2c285f318b459fa1acfff7b9b1e0#file-playtsmartvideotoken-sol-L104

https://gist.github.com/yuriy77k/006c2c285f318b459fa1acfff7b9b1e0#file-playtsmartvideotoken-sol-L119

### Recommendation

Add zero address checking into functions `transfer` and `transferFrom`
```solidity
require(to != address(0));
```

## 3.2. Known vulnerabilities of ERC-20 token

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

There are no severe vulnerabilities found in this contract, and it's safe for deploy. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b289e091f2176ccf85dcf6adb6d5f72d

https://gist.github.com/yuriy77k/dba4a0f5e58efead0c2575f77ebc7f05

https://gist.github.com/yuriy77k/3d5690e8bdb0ae9f9ba4d21820302953
