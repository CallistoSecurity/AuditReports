# Scanetchain Security Audit Report

# 1. Summary

[Scanetchain](https://www.kchain.kr) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol       : SWC
	Name         : Scanetchain
	Capped supply: 1,000,000,000
	Decimals     : 18 
	Standard     : ERC20
   
# 2. In scope

- [scanetchaintoken_new_final.sol](https://github.com/Scanetchain/Scanetchain-ERC20-Token/blob/master/Contracts/scanetchaintoken_new_final.sol) github commit hash 2cd2a198781a58b0b5af2d60df2cbb2dab618cf9.

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. Owner Privileges

### Severity: owner privileges

### Description

Owner has the right to pause/unpause token transfers and approvals.

### Code snippet

https://github.com/Scanetchain/Scanetchain-ERC20-Token/blob/master/Contracts/scanetchaintoken_new_final.sol#L374#L431

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/24591a9f365f748d5451cdca94e1b3d7

https://gist.github.com/yuriy77k/3c2a92be22a3f0f1dada3b11e097c03f

https://gist.github.com/yuriy77k/2a199bc40fe873589b58155639a8e445
