# 1. Summary

[Farm2Kitchen](https://1drv.ms/u/s!AuCtPn9dirqajIlcjhvzPWHrgvJo5g) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token description:

	Symbol      : F2K
	Name        : Farm2Kitchen
	Total supply: 280,000,000
	Decimals    : 18
	Standard    : ERC20

# 2. In scope

Farm2Kitchen.sol

# 3. Findings

In total, **4 issues** were reported including:

- 2 medium severity issues.

- 2 low severity issues.

No critical security issues were found.

## 3.1. Token Distribution 

### Severity: medium

### Description

The totalSupply is given to ` balances[owner]` inside the constructor, however `distribute` function member `F2KToken` contract mints tokens and set them into `balances[to]` at each call, making the real token supply since `circulatingSupply < totalSupply` to 2 times the described token supply in the audit request and the website description.

For example if `distribute` allocate all the possible tokens, the real total token supply will be 560m tokens, the total possible to burn is `totalSupply = 280m`, which means that the contract will show a totalSupply of zero while 280m tokens are circulating (this is supposing that the contract owner burn all his tokens, even in this case the contract won't be compatible with ERC20 standard).

### Code snippet

https://gist.github.com/yuriy77k/cb5a89bb8aa4ceff62b6590a07a550fa#file-farm2kitchen-sol-L131#L135

https://gist.github.com/yuriy77k/cb5a89bb8aa4ceff62b6590a07a550fa#file-farm2kitchen-sol-L152#L164

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Code snippet

* [ERC20Token.sol, line 154](https://github.com/vpomo/RapidProfit/blob/f7aa43eff6ba66b8651ab74802823ea5929cc153/contracts/ERC20Token.sol#L154)

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.3. Burn 

### Severity: medium

### Description

`burn` function member of `F2KToken`, give the power to the token owner to burn from any wallet address. Giving himself a high administration power of the users asserts can hurt the project and users holdings.

### Code snippet

https://gist.github.com/yuriy77k/cb5a89bb8aa4ceff62b6590a07a550fa#file-farm2kitchen-sol-L184#L195

## 3.4. Token Transfer to Address 0x0

### Severity: low

### Description

CBP Token do not require the `to` address to be non null before `transfer`. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://gist.github.com/yuriy77k/cb5a89bb8aa4ceff62b6590a07a550fa#file-farm2kitchen-sol-L263#273

# 4. Conclusion

Multiple issues have been presented, the contract developers should consider fixing them. Contract Token is not compatible with ERC20 standard due issue 3.1.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b6e04a927f8e42950dbbc36c0b6421ce

https://gist.github.com/yuriy77k/dcdc9066a5a5313da80eadb97d500473

https://gist.github.com/yuriy77k/653d689303676280b3cab7544fb27b62
