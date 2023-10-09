# 1. Summary

[Diligence](https://github.com/diligenceitoken/Diligence/blob/master/smartcontract.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [smartcontract.sol](https://github.com/diligenceitoken/Diligence/blob/master/smartcontract.sol) github commit hash 84cb5318231c8c6c080242ff0519d297c078bb03.

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

## 3.2. Token Transfer to 0x0 address

### Severity: low

### Description

The implemented token uses transfer to address 0x0 as basic burn mechanism, however accidental token loss issue can be applied.

### Code snippet

https://github.com/diligenceitoken/Diligence/blob/84cb5318231c8c6c080242ff0519d297c078bb03/smartcontract.sol#L128#L130

https://github.com/diligenceitoken/Diligence/blob/84cb5318231c8c6c080242ff0519d297c078bb03/smartcontract.sol#L146#L151

https://github.com/diligenceitoken/Diligence/blob/84cb5318231c8c6c080242ff0519d297c078bb03/smartcontract.sol#L178#L184

## 3.3. Deprecated method.

### Severity: minor observation

### Description

The `function () payable { revert(); }` was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

### Code snippet

https://github.com/diligenceitoken/Diligence/blob/84cb5318231c8c6c080242ff0519d297c078bb03/smartcontract.sol#L212

# 4. Conclusion

The audited contract can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5a510ff72f212bc49360849c0895cba6

https://gist.github.com/yuriy77k/ca953854a883277b042e9bf8af441bda

https://gist.github.com/yuriy77k/8651908f812a0cedd1622b89375717ca
