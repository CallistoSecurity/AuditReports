# Clickscrow token (CSW) Security Audit Report

# 1. Summary

[Clickscrow token](https://etherscan.io/address/0xeb342b37b897c7a2f451f39e0a6725461804b93d#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> At a high level the Clickscrow token (CSW) is designed to serve as a unit of account within the clickscrow ecosystem. Due to the framework under which ERC-20 token operate, the CSW token functions strictly as a utility measure within the Clickscrow ecosystem and by no means a representation of any underlying ownership outside of the ecosystem.

	Symbol       : CSW
	Name         : Clicks
	Capped supply: 500,000,000
	Decimals     : 18 
	Standard     : ERC20

# 2. In scope

1. [Clickscrow Token](https://etherscan.io/address/0xeb342b37b897c7a2f451f39e0a6725461804b93d#code)

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

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

## 3.2. Allowance Decrease

### Severity: low

### Description

The value to be subtracted is decreased using safemath `sub` function meaning that if the value is higher than the remaining allowance the transaction will throw when it could be set to zero instead. multiple scenario can be imagined for the end user.

### Code snippet

```
    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
        _approve(msg.sender, spender, _allowed[msg.sender][spender].sub(subtractedValue));
        return true;
    }
```

### Recommendation

Check if the allowance is lower than the value to be subtracted and set the allowance to zero if the condition is met.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/969a1bbfdec932245e8237410953a81a

https://gist.github.com/yuriy77k/f815b599f5e79397f8af470071610138

https://gist.github.com/yuriy77k/780d7c35526b46301118980f62565f16
