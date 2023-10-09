# 1. Summary

[FoodNation Presale]() security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [PreSale.sol](https://github.com/foodnation/contracts/blob/master/PreSale.sol) github commit hash 90cafc7d645bd34145663413125f588c1ab782c5.
- [USDPrice.sol](https://github.com/foodnation/contracts/blob/master/price/USDPrice.sol) github commit hash 90cafc7d645bd34145663413125f588c1ab782c5.
- [TimedCrowdsale.sol](https://github.com/foodnation/contracts/blob/master/crowdsale/validation/TimedCrowdsale.sol) github commit hash 90cafc7d645bd34145663413125f588c1ab782c5.
- [Crowdsale.sol](https://github.com/foodnation/contracts/blob/master/crowdsale/Crowdsale.sol) github commit hash 90cafc7d645bd34145663413125f588c1ab782c5.
- [MilestoneCrowdsale.sol](https://github.com/foodnation/contracts/blob/master/crowdsale/validation/MilestoneCrowdsale.sol) github commit hash 90cafc7d645bd34145663413125f588c1ab782c5.

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Pre-Sale Period Rate

### Severity: minor observation

### Description

Following the contract description the pre-sale consists of 3 milestones, every milestone provide important presale parameters like rate, cap and time interval:

- 1 - 21 sep 2018 - 21 oct 2018
- 2 - 21 oct 2018 - 21 nov 2018
- 3 - 21 nov 2018 - 21 dec 2018

The token amount returned by `_getTokenAmount` member of `PreSale` contract should be dependent only to the time interval where the request to buy tokens is made, so the user will get the amount of tokens expected following the rate of the current milestone time interval.

However, [getCurrentRate()](https://github.com/foodnation/contracts/blob/master/PreSale.sol#L120) is dependent to the time of the request and the total amount of tokens sold during the milestone, if the total of tokens sold on that period is superior than the cap [the next milestone index is used](https://github.com/foodnation/contracts/blob/master/crowdsale/validation/MilestoneCrowdsale.sol#L152) to get the new rate.

Users expecting to buy with a known rate, will be buying with next milestone rate even if its time interval is not yet reached and the cap is reached.

### Code snippet

https://github.com/foodnation/contracts/blob/master/PreSale.sol#L117#L121

https://github.com/foodnation/contracts/blob/master/crowdsale/validation/MilestoneCrowdsale.sol#L198#L200

https://github.com/foodnation/contracts/blob/master/crowdsale/validation/MilestoneCrowdsale.sol#L152#L154


## 3.2. USD/ETH rate

### Severity: low

### Description

`getPrice` function member of `USDPrice`, convert ether to USD cent, the decimal number of the returned value should be clearly defined otherwise We cannot confirm that the `_getTokenAmount` member of `PreSale` will return the right amount of tokens that the buyer just invested for.

Any conversion should be computed using nominator and denominator where rate = nominator/denominator.

### Code snippet

https://github.com/foodnation/contracts/blob/master/price/USDPrice.sol#L51#L55


# 4. Conclusion

Contract developers should investigate the highlight issues to avoid any conflict with the investors.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1e02b58d046495fb6c666dc52f358f0a

https://gist.github.com/yuriy77k/c578a87a9f98f56a5265ae52dc466687

https://gist.github.com/yuriy77k/f5e017b576149795cd388a66e266af5d
