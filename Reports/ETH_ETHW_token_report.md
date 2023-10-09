# 1. Summary

[ETHW Token](https://etherscan.io/address/0xfbd86312f156b0cc976e558b62da068bbafcaf9c#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [SmartzToken.sol](https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd)

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 5 minor observation.


## 3.1. Token Freezing

### Severity: medium

### Description

Adding too much Frozen transfer to an address with different values of `thawTS` and `isKYCRequired` parameters may extend the user `frozenBalances` array making the cost of every call to `transfer`and`transferFrom` more expensive if not throwing an out of gas error or in a less probable case a block out of gas error, since many reimplemented functions in `SmartzToken` contract call `thawSomeTokens` that manage the frozen tokens.

### Code snippet

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L968#L992

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L732#L740

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L750#757

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

## 3.3. Extra checking.

### Severity: minor observation

### Description

The require statements in functions Transfer, TransferFrom, Burn are unnecessary. These conditions are enforced by the SafeMath library. We recommend that these require statements be removed.

### Code snippet

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L474

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L512

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L536

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L537

## 3.4. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.18. It is suggested to use the latest version and fix all compiler warnings that arise.

## 3.5. Owner Reorganization

### Severity: minor observation

### Description

`reorganizeOwners` member of `multiowned` contract is a private function that is only called by `removeOwner` function that deletes one address at a time, however the implemntation `reorganizeOwners` is too complicated when only the last address in `m_owners` can be swapped with the deleted address since both indexes can be easily retrieved without need to search for their indexes. The implemented function do not optimize gas consumption.

### Code snippet

https://gist.github.com/yuriy77k/ca6a9fade235f0f485ff6e862d4b43fd#file-smartztoken-sol-L212#L230

# 4. Conclusion

The audited contract has issues which have to be addressed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/723deb9e7a310c34af3f19f94daddb44

https://gist.github.com/yuriy77k/23b43bc292858411e78560dd11dc4437

https://gist.github.com/yuriy77k/2013566d3267419d823fc5cec409bb71
