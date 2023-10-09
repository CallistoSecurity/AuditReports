# 1. Summary

[Bitcademy V3](https://github.com/bitcademyfb/Bitcademy_ICO) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BitcademyToken.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol) github commit hash 9ae1acf20ed619e0aab034bafe7d6b37dcbb82e9.
- [BitcademyVesting.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol) github commit hash 9706db9c1b02a172c3eec2f33d57ea3c0fcd0437.
- [Crowdsale.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol) github commit hash 4131c02f09de1eed70d1560df1ed5adddeecdcbb.
- [PreICOBitcademyGold.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol) github commit hash 9a766bed266dd457fccbca9da298113c88e5d78f.
- [RefundVault.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/RefundVault.sol) github commit hash 695932da33c7ac090a0d6593bdf372977722ac87.

# 3. Findings

In total, **9 issues** were reported including:

- 3 medium severity issues.

- 5 low severity issues.

- 1 minor observation.


## 3.1. The investor removed from whitelist won't get refund if goal is reached.

### Severity: medium

### Description

If the goal is reached the `blacklistClaimRefund()` won't work because `vault.refund()` method work only for the refunding state.

### Code snippet

* [Crowdsale.sol, line 119](https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/Crowdsale.sol#L119)

* [PreICOBitcademyGold.sol, line 123](https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/PreICOBitcademyGold.sol#L123)

## 3.2. The `blacklistClaimRefund` function issues.

### Severity: medium

### Description

  1. `blacklistClaimRefund` function should be possible to call only for the "blacklisted" users. Correction of the previous point (1) may cause that **whitelisted** users could get refund even the goal is reached. In any case, should be allowed to call only people from the "black list".

  2. `tokenToClaim` should be reset for blacklisted user after refund. To prevent the theoretical possibility to get both - the refund and receiving tokens.

### Code snippet

* [Crowdsale.sol, line 116](https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/Crowdsale.sol#L116)

* [PreICOBitcademyGold.sol, line 120](https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/PreICOBitcademyGold.sol#L120)

## 3.3. ICO Autonomy

### Severity: low

### Description

- `updateReleaseDate` function allow the contract owner to extend the token locking period, making the investors wait more to be able to withdraw their tokens.

- `adjustCloseDate` function allow the contract owner to extend the ICO close time. For example if the owner private key is hacked, an attacker can set the `closingTime` value so far into the future that `finalize` function will throw at each call. all the fund collected fund and tokens will be locked.

- Adding to and removing from whitelist should be not possible after finalization. 

Many consequences can be described concerning the above issue, contract developers should make the ICO as autonomous as possible.

This issue is applicable for both `Crowdsale` and `PreICOBitcademyGold` contracts.

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L536

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L545

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L431

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L440

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/Crowdsale.sol#L236-L256

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/PreICOBitcademyGold.sol#L239-L259

## 3.4. Token Conversion

### Severity: medium

### Description

The `rate` value is the `number of token per ether`. Following from this in the code are not correct calculation formulas. 

1. If rate is 50 then I should get `50*10**18` tokens if I pay `1*10**18 wei`. But following the formula I will get `tokensInCondition = ((10**18).div(50))*(10**18)` = 2*10**34 tokens.

2. Bonus calculation is wrong. If rate is 50 and preSale bonus is 60% then I should get 80 tokens per 1 ETH (rate must be 80). But following the [formula](https://github.com/RideSolo/Bitcademy_ICO/blob/97fb75364a9c5a222cc0a37e208106c741fedf9d/contracts/PreICOBitcademyGold.sol#L372-L373) I will get `currentRate = 50.mul(10).div(16)' = 31.25 (actually 31) tokens per 1 ETH.

3. Is not clear which size value is stored in `rate`. For example, it may be 50 (tokens) or 50*10**18 (tokens with decimals). From this depends all others calculations.

These issues are applicable for both `Crowdsale` and `PreICOBitcademyGold` contracts.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L370

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L374

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L369

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L387

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L404

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L421

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L456

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L471

## 3.5. State Variable initialization

### Severity: low

### Description

Price state variable is intended to be set inside the constructor since the constructor has an input named `_price`, but it is not set.

This issue is applicable for both contract `Crowdsale.sol` and `PreICOBitcademy.sol`

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L163

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L166

## 3.6. Investors Count

### Severity: low

### Description

`_forwardFunds` function pushes investors address at every call, if an investor make multiple investment his address will be pushed twice. In order for the `investorsCount` function to return the right number, every investor address should be pushed once.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L488

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L385

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol

## 3.8. Transfer Event

### Severity: Minor observation

### Description

Emit an event with wrong value:
```
emit Transfer(this,_reserve,totalSupply_);
```

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol#L15

### Recommendation

the Transfer value should be `balances[_reserve]`.
```
emit Transfer(this,_reserve,balances[_reserve]);
```

## 3.9. It is necessary to add parameter checks for the constructor of `BitcademyVesting` contract.

### Severity: low

### Code snippet

* [BitcademyVesting.sol, lines 51-52](https://github.com/bitcademyfb/Bitcademy_ICO/blob/9a766bed266dd457fccbca9da298113c88e5d78f/contracts/BitcademyVesting.sol#L51-L52)

### Description

It is possible do not set any parameters in the constructor by accidentally. And it is possible not to pay attention to it. And they will be initialized with zero. And that would break the contract work.

# 4. Conclusion

The audited contracts are not safe and cannot be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/39b19fed0fc6d759bbda18f5d31bd182

https://gist.github.com/yuriy77k/1eab7d990d35bffe774ad4566217421b
