# 1. Summary

[PHI Token](https://github.com/vpomo/TokenPHI/tree/master/contracts) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [PHICrowdsale.sol](https://github.com/vpomo/TokenPHI/blob/master/contracts/PHICrowdsale.sol) github commit hash c0eeedc616935ce2cf72191567c05bc705e983a1.

# 3. Findings

In total, **6 issues** were reported including:

- 2 medium severity issues.

- 3 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Token Minting

### Severity: Medium

### Description

- `mint(address _to, uint256 _amount, address _owner)` function does not mint tokens but rather transfer tokens from `_owner` address to `_to` this allow transfer of tokens from any address to another address.
`mint` is marked as internal, its usage is limited inside the contract and it won't harm any investor.

- When the ICO end, if owner doesn't call `ownerBurnToken` the allocated tokens for the crowdsale will be kept by the owner in his wallet, since mint do not really mint but just transfer tokens from `addressFundReferal` or `owner` addresses.

- `mintingFinished` member of `MintableToken` contract, is not intended be set to `true` at any moment inside all the Token and ICO logic.

### Code snippet

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L243#L253

## 3.2. ICO Rates

### Severity: medium

### Description

If a user buy tokens during the pre-ICO expecting  `ratePreIco` to be applied and  the `tokenAllocated` is higher than `limitPreIco` than the used rate will be `rateIco`, resulting in an `amountOfTokens` lower than expectations.

### Code snippet

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L385#L395

## 3.3. ICO Phases Time

### Severity: low

### Description

ICO phases can be started, extended or stoped at the owner will.

### Code snippet

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L495#L500

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L507#L512

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L518#L523

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L529#L533

## 3.4. Minting Event

### Severity: low

### Description

`mintForFund` should emit `Mint` event after adding fund value to every address.

### Code snippet

https://github.com/RideSolo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L443#L456

## 3.5. Known Issues of ERC20 Standard

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.6. Different data in docs and code. 

### Severity: minor observation

### Description

According to [documentation](https://github.com/EthereumCommonwealth/Auditing/issues/62) there is 60 000 000 tokens for sale, but in code there is [60 250 000](https://github.com/vpomo/TokenPHI/blob/master/contracts/PHICrowdsale.sol#L305)

### Recommendation

Please provide correct data and re-check all the values.

# 4. Conclusion

Smart contracts are intended to be more autonomous than centralized applications, Crowdsale functions should be more decentralized to fully benefit from the trustless nature of the ethereum blockchain.

Multiple issues have been raised, the contract developers should fix them before deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ed0c84ab3aab76310a94268b3115e6b9

https://gist.github.com/yuriy77k/05801fb042a3ad801f75e3ba7e55d390

https://gist.github.com/yuriy77k/6f4652404b25815ce2e7e6b59be9c4e8
