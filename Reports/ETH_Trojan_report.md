# 1. Summary

[Trojan tokens crowdsale](https://github.com/TROJANFOUNDATION/Private-sale-contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> This is the smart-contract that will manage the crowdsale of Trojan tokens. Trojan is a community currency for the cultural commons in Athens, Greece, and the native token of the Trojan Foundation.

http://www.contemporaryartshowcaseathens.com/

# 2. In scope

Commit hash: `1d00b36c3661d3e72e824c1a2813007289188571`

1. [CustomAdmin.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/CustomAdmin.sol)

2. [CustomCappedCrowdsale.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/CustomCappedCrowdsale.sol)

3. [CustomPausable.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/CustomPausable.sol)

4. [Migrations.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/Migrations.sol)

5. [TrojanSale.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol)

6. [WhiteList.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/WhiteList.sol)

7. [mocks/ERC20Mock.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/mocks/ERC20Mock.sol)

# 3. Findings

In total, **7 issues** were reported including:

- 1 high severity issues.

- 2 medium severity issues.

- 3 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to:

- change the buy and sell price of the tokens at any moment;
- change value of MaxCap and MinCap;
- the pause functions of contract.

This contract is managed manually by the owner which is not good for investors.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L55

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L65

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L76

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/CustomPausable.sol#L35

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.3. Anyone can update min and max capitalization values.

### Severity: medium

### Description

The functions `changeMaxCap` and `changeMinCap` members of `TrojanSale` have `onlyAdmin` modifier but they call `updateMinCap` and `updateMaxCap` functions members of `CustomCappedCrowdsale` which are `public` and don't have any access restrictions and everyone can call them.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L65-L81

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts//CustomCappedCrowdsale.sol#L68-L80

### Recommendation

Change the functions `updateMinCap` and `updateMaxCap` members of `CustomCappedCrowdsale` from `public` to `internal`.

## 3.4. The deposit value of investor will be reset at each purchase.

### Severity: high

### Description

It doesn't take into user previous payments and the user will not be able to refund them.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L136

### Recommendation

Use `_deposits[beneficiary] = _deposits[beneficiary].add(weiAmount);`

## 3.5. Check for an empty input value of `allocateIntoBondingCurve()`.

### Severity: low

### Description

There is no check for an empty input value of `allocateIntoBondingCurve()`. It is possible to send funds to `0x0` address by accidentally.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L209

### Recommendation

```solidity
require(_bondingCurveToken != address(0));
```

### 3.6. Whitelist issues.

### Severity: medium

### Description

1) The function `_deliverTokens` member of `TrojanSale` call `addWhitelist` function member of `WhiteList` but anybody can all it directly and add anyone to whitelist with any amount of tokens.
2) `whitelist[_account] += _tokens;` overflow is possible. Use SafeMath.
3) `ifWhitelisted` modifier is never used.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L138

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/WhiteList.sol#L31

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/WhiteList.sol#L39

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/WhiteList.sol#L19

### Recommendation

Change the function `addWhitelist` member of `WhiteList` from `public` to `internal`.

## 3.7. Wrong comments

### Severity: minor observation

### Description

In comment above function `allocateIntoBondingCurve` member of `TrojanSale` written that should be allocated 50% of funds, but there will be only 40% of them.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/1d00b36c3661d3e72e824c1a2813007289188571/contracts/TrojanSale.sol#L202-L204

# 4. Conclusion

Smart contracts have many issues varying severity, which should be fixed. In the current state, it is not safe and can not be used. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ceef6368d1ecb502bb521ae3dbd9ca18

https://gist.github.com/yuriy77k/9c33bd8774233c133b318232c28f7e0f

https://gist.github.com/yuriy77k/8c28717a60cff14a652714046603ff3e
