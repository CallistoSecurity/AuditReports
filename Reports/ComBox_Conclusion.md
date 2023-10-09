# 1. Summary

[ComBox](https://github.com/combox-io/smart-contracts/) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token description:

	Symbol      : CBP
	Name        : ComBox.io ERC20 token
	Total supply: 96,984,643
	Decimals    : 4
	Standard    : ERC20

Basic interface extensions:

 - Pausable smart-contract
 - Mass sending: an ICO management software sends tokens using mass sending function. 
 - Token burning procedure: the token developers allow 0x0 transfer as token burning procedure.

# 2. In scope

- [CBPtoken.sol](https://github.com/combox-io/smart-contracts/blob/master/CBPtoken.sol) github commit hash 09c658603fc27a9a35c1f150bd74184a2afd7efd.

# 3. Findings

In total, **3 issues** were reported including:

- low 3 severity issues.

- minor observation.

No critical security issues were found.


## 3.1. Extra Requirement to be Added

### Severity: low

### Description

The lengths of `recipients` and `values` parameters of `masssend` function should be checked otherwise investors might receive a possible amount of other investors if one array is longer than the other one.

### Code Snippet

https://github.com/combox-io/smart-contracts/blob/master/CBPtoken.sol#L130#L136

## 3.2. Token Transfer to Address 0x0

### Severity: low

### Description

CBP Token do not require the `to` address to be non null before `transfer`. Accidental token loss to address 0x0 can be applicable.

The version of ERC20 used in this contract, use a basic burn mechanism where anyone can send tokens to 0x0 address to burn them. However, this mechanism leads to the above mentioned issue.

### Code snippet

https://github.com/combox-io/smart-contracts/blob/master/CBPtoken.sol#L188#L193

## 3.3. Known Issues of ERC20 Standard

### Severity: low

### Description

ERC20 Tokens have some well-known issues (listed below), This is just a reminder for the contract developers.

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 4. Conclusion

The contract is safe to be deployed.


# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f481d70996f32e4fb0f7636bbd064560

https://gist.github.com/yuriy77k/bc2f7b6b3b01699b6e13f3f94225c84b

https://gist.github.com/yuriy77k/c26f060c672b17d566e9aa9a99e9abab
