# 1. Summary

[HuntToken](https://github.com/Steemhunt/HUNT-ERC20) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : HUNT
	Name        : HuntToken
	Total supply: 500,000,000
	Decimals    : 18
	Standard    : ERC20

# 2. In scope

- [HuntToken.sol](https://github.com/Steemhunt/HUNT-ERC20/blob/master/contracts/HuntToken.sol) github commit hash a0a22d5c582f5a50955f5835f22dfadc60dd6294.

# 3. Findings

In total, **1 issues** were reported including:

- 1 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

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

The contract is safe and can be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/af2dcb3aa490cf533388832ae6d7022a

https://gist.github.com/yuriy77k/ed073f679d2931be167a4cc70619d8b6

https://gist.github.com/MrCrambo/5609af3687bb3f0504bacc127923db53
