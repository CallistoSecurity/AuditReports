# 1. Summary

[JarvisPlus Token](https://github.com/x-contract/JarvisPlusToken/blob/master/flats/JarvisPlusToken_flat.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token description:

	Symbol      : JAR
	Name        : Jarvis+ Token
	Total supply: 600,000,000
	Decimals    : 18
	Standard    : ERC20

# 2. In scope

- [JarvisPlusToken_flat.sol)](https://github.com/x-contract/JarvisPlusToken/blob/master/flats/JarvisPlusToken_flat.sol) github commit hash b1e9458f8a77fe13d09940547da7824f3066cf17.

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Code snippet

* [ERC20Token.sol, line 154](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/ERC20Token.sol#L154)

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Extra checking.

### Severity: minor observation

### Description

Extra checking in 96, 165, 166 lines of BasicToken and StandardToken contracts. SafeMath library checks it anyway.

### Code snippet

https://github.com/x-contract/JarvisPlusToken/blob/8dca09084a7e9178ee5770228219bf901ecaf21e/flats/JarvisPlusToken_flat.sol#L96

https://github.com/x-contract/JarvisPlusToken/blob/8dca09084a7e9178ee5770228219bf901ecaf21e/flats/JarvisPlusToken_flat.sol#L165

https://github.com/x-contract/JarvisPlusToken/blob/8dca09084a7e9178ee5770228219bf901ecaf21e/flats/JarvisPlusToken_flat.sol#L166

# 4. Conclusion

The token contract is safe to be deployed, developers should consider checking ERC20 known issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/6d6b617a907b4454ddf05661d5b66f91

https://gist.github.com/yuriy77k/d1f01c441cb6ac96d25368a25cf4407d

https://gist.github.com/yuriy77k/141fc6fc44b69cfeb06fe0335c72475d
