# Security Audit Report

# 1. Summary

[Egypt Token](https://etherscan.io/address/0xa8f74d0ddfa590ba7e0794e9742846eb52f2acac#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : EGY
	Name        : EGYPT
	Total supply: 1,000,000,000
	Decimals    : 18
	Standard    : ERC20

# 2. In scope

- [Egypt.sol](https://gist.github.com/yuriy77k/f5306a97ffa3fb65216c3a68c9e144fe)

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

## 3.2. Block Reward

### Severity: low

### Description

`giveBlockReward` by itself is raising multiple ERC-20 compliance issues:

- `totalSupply` is not updated when adding the token value to the `balanceOf[block.coinbase]`.
- Transfer event is not emitted when adding the token value to the `block.coinbase` address.

This function is public and could be called anyone and any amount of time.

Developers should explain their intentions when adding such functionality to the token contract.

If it was intended to give 1 token, the correct value to be added will be `1*10e18`. and developers should add a mint event to the function for a more general compliance with the different block explorers.

### Code snippet

https://gist.github.com/yuriy77k/f5306a97ffa3fb65216c3a68c9e144fe#file-egypt-sol-L131

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1c99d56dfaae42197758efed6c57bbb5

https://gist.github.com/yuriy77k/5043181234f4bd80bc86e3cf0f5016ba

https://gist.github.com/yuriy77k/5ba6557b9d99aff406569e8c81016378
