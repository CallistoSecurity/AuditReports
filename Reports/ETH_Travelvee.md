# 1. Summary

[Travelvee](https://github.com/travelvee/TravelToken/blob/master/travel.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [travel.sol](https://github.com/travelvee/TravelToken/blob/master/travel.sol) github commit hash 565fd5f7ae42de2c5647fb89e8b0406483bc77b9.

# 3. Findings

In total, **8 issues** were reported including:

- 1 high severity issues.

- 1 medium severity issues.

- 4 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Stages GAP Exploit

### Severity: HIGH

### Description

Between every end of a stage and beginning of another stage there is a 24 hour gap where `currentStage` will return 0 which will be the presale 50% bonus as defined in `_bonuses` state variable, any attacker that exploit this will get that bonus.

### Code snippet

https://github.com/travelvee/TravelToken/blob/master/travel.sol#L126#L128

https://github.com/travelvee/TravelToken/blob/master/travel.sol#L253#l259

https://github.com/travelvee/TravelToken/blob/master/travel.sol#L283

## 3.2. Wrong calculation of sold tokens

### Severity: medium

### Description

In the function `buyTokens` the variable `_soldTokens` contain tokens amount without bonuses. But function `_processPurchase` uses `total` tokens with bonuses and in following steps it compares with `_soldTokens`. It may cause oversold of tokens. 

### Code snippet

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L294-L311

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L323-L324

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L329-L330

### Recommendation

Add to `_soldTokens` tokens with bonuses. For example:

```solidity
_soldTokens = _soldTokens.add(total);
```

## 3.3. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to:

- change the price of the tokens at any moment in or after the presale phase.
- forward all fonds 
- burn tokens or not after crowdsale, depends on the owner's wish

This contract is managed manually by the owner, without softcap and withdraw functions which is not good for investors.

## 3.4. Not restrictions for airdrop tokens

### Severity: low

### Description

There is not restrictions of the amount airdrop tokens in the function `sendBatchCS`. The owner can transfer more then 2000000 tokens.

### Code snippet

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L143-L156

## 3.5. Constructor parameters. 

### Severity: low

### Description

Beginning and ending dates in constructor are not correct (start from 14 Oct 2018). 

### Code snippet

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L126-L127

### Recommendation

Update the beginning and ending dates of crowdsale.

## 3.6. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

## 3.7. The function `_burnFrom` unusable. 

### Severity: minor observation

### Description

Internal function _burnFrom is not used.

### Code snippet

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L226-L232

## 3.8. Extra checking.

### Severity: minor observation

### Description

Extra checking in 171, 187-187 lines. SafeMath library checks it anyway.

### Code snippet

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L171

https://github.com/travelvee/TravelToken/blob/565fd5f7ae42de2c5647fb89e8b0406483bc77b9/travel.sol#L187-L188

### Recommendation

Those lines may be deleted.

# 4. Conclusion

The audited smart contract can be exploited to get tokens with high bonus even in advanced stages. Also, some other vulnerabilities were detected. We highly recommend completing bugs bounty program before use.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/011b0d2de02fd6059a239fb4542e84c7

https://gist.github.com/yuriy77k/5c4fe3e89449937da2c26b8b48a129f5

https://gist.github.com/yuriy77k/5c6466be51d69e832aa50b79bfe08ced
