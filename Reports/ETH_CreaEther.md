# 1. Summary

[CreaEther](https://etherscan.io/address/0x7c0a3f749ed88a10015d553dfdadd8bdcdb784fa#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [CreaEther.sol](https://etherscan.io/address/0x7c0a3f749ed88a10015d553dfdadd8bdcdb784fa#code).

# 3. Findings

In total, **4 issues** were reported including:

- 2 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Token Transfer to 0x0 address

### Severity: low

### Description

The implemented token uses transfer to address 0x0 as basic burn mechanism, however accidental tokens loss issue can be applied.

### Code snippet

https://gist.github.com/yuriy77k/c880b07faac98515f58d43f944b76cf8#file-creaether-sol-L128

https://gist.github.com/yuriy77k/c880b07faac98515f58d43f944b76cf8#file-creaether-sol-L146

## 3.3. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.18. It is suggested to use the latest version and fix all compiler warnings that arise.

## 3.4. Deprecated method.

### Severity: minor observation

### Description

The function () payable { revert(); } was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

# 4. Conclusion

The audited contract can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/8934b038e17ea62f2f1f146913e2866e

https://gist.github.com/yuriy77k/31ac53fb00d1b19eede61c11efce1796

https://gist.github.com/yuriy77k/92f407e88930ef6e30297a2f289e7b01
