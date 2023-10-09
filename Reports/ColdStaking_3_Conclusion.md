# 1. Summary

[Callisto: Cold Staking](https://github.com/EthereumCommonwealth/Cold-staking/tree/863846e510299b8cb07bab38c0b60d1bd78e9947) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

ColdStaking.sol

# 3. Findings

In total, **1 issues** were reported including:

- minor 1 observation.

No critical security issues were found.

## 3.1. `Timestamp` may have not right meaning. A round can go longer than 27 days.

### Severity: minor observation.

### Code snippet

* [Lines 123-128 ](https://github.com/EthereumCommonwealth/Cold-staking/blob/863846e510299b8cb07bab38c0b60d1bd78e9947/ColdStaking.sol#L125-128)

### Description

The round can go longer than 27 days in case of an increase block generation time to over 25 seconds for a long time.

In case when blocktime is more than 25 seconds the `Timestamp` will have not "timestamp of the last interaction" value (look at [here](https://github.com/EthereumCommonwealth/Cold-staking/blob/863846e510299b8cb07bab38c0b60d1bd78e9947/ColdStaking.sol#L128)). This will lead to a distortion of the flow of staking time.

Consider the problem by example:

Block id|Block time, sec|`_seconds` variable|now|`Timestamp` variable|`now - Timestamp =`|Recommendation
--------|---------------|-------------------|----|--------------------|----------------|----
start value||||1539260000||Mike make a stake.
1200000|35|25|1539260035|1539260025|10|
1200001|35|25|1539260070|1539260050|20|
1200002|35|25|1539260105|1539260075|30|
...|...|...|...|...|...|**An hour has passed**
1200103|35|25|1539263640|1539262600|1040|Passed 1 hour of real time, but the contract "thinks" that 43 minutes have passed.

**I don't know** whether a continuous change in the block generation time by a value greater than 25 seconds is possible. But if it is possible then the time inside the contract will differ from the real time. What will affect the reward.

# 4. Conclusion

No critical vulnerabilities were detected.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0074bd128bb29b601702e080c95b1fa4

https://gist.github.com/yuriy77k/28b349a4f4fbf71639d6d35b6d4357a7

https://gist.github.com/yuriy77k/1c97d787d60faa6c2e82f1b30529bb51


@gorbunovperm

Notes regarding the https://gist.github.com/gorbunovperm/baec3a9b533b922c34a32a6a8d4f579f report.

* [A staker will lose the deserved reward if he makes an additional deposit before the end of the round.](https://gist.github.com/yuriy77k/0074bd128bb29b601702e080c95b1fa4#1-a-staker-will-lose-the-deserved-reward-if-he-makes-an-additional-deposit-before-the-end-of-the-round) This situation is part of [Cold Staking rules](https://callisto.network/blog/post/callisto-network-cold-staking-protocol/). 

> A staker MUST NOT deposit funds into the staking contract during the locking period.  Depositing funds during the locking period will restart the locking period and staking contract.

* [Reward depends on StakingRewardPool value](https://gist.github.com/yuriy77k/0074bd128bb29b601702e080c95b1fa4#2-reward-depends-on-stakingrewardpool-value-and-this-is-the-motivation-to-manipulate-the-order-of-transactions), but the reward does not depend on the order of transactions, because when user claim reward it changed StakingRewardPool and TotalStakingWeight.

For example: 
```
StakingRewardPool = 100
Alice's StakerWeight = 500
Bob's StakerWeight = 300
Carol's StakerWeight = 200
TotalStakingWeight = 500 + 300 + 200 = 1000

Lets Alice claim first. Her reward = 100 * 500 / 1000 = 50.
StakingRewardPool = 100 - 50 = 50
TotalStakingWeight = 1000 - 500 = 500

After Alice, Bob claim his reward = 50 * 300 / 500 = 30
StakingRewardPool = 50 - 30 = 20
TotalStakingWeight = 500 - 300 = 200


Lets change transactions order:
Bob claim first. His reward = 100 * 300 / 1000 = 30.
StakingRewardPool = 100 - 30 = 70
TotalStakingWeight = 1000 - 300 = 700

After Bob, Alice claim her reward = 70 * 500 / 700 = 50
StakingRewardPool = 70 - 50 = 20
TotalStakingWeight = 700 - 500 = 200
```


* [Stake time not updated after withdrawal](https://gist.github.com/yuriy77k/0074bd128bb29b601702e080c95b1fa4#4-stake-time-not-updated-after-withdrawal). This situation [described here](https://github.com/EthereumCommonwealth/Cold-staking/issues/9#issuecomment-429212398).

> By keeping this variable we will be able to see when the staker performed actions with the contract for the last time, which can be useful in some cases. I know that it is possible to review transaction history, but watching the variable in the contract is easier.

* [A round can go longer (not faster) than 27 days.](https://gist.github.com/yuriy77k/0074bd128bb29b601702e080c95b1fa4#3-timestamp-may-have-not-right-meaning-a-round-can-go-faster-than-27-days). In described situation:

> Passed 1 hour of real time, but the contract "thinks" that 43 minutes have passed.

So when contract "thinks" that passed 1 hour, in real time may be passed more than 1 hour. Therefore a round can go longer by real time. But I do not think that such a situation is possible for a long time. Also, it will not lead to loss of the stakers reward.


@pro100skm

Notes regarding the https://gist.github.com/pro100skm/7316153c530f600090a1ac2fb1f79e53 report.

* No [wrong calculation](https://gist.github.com/yuriy77k/28b349a4f4fbf71639d6d35b6d4357a7#1-wrong-calculation). TotalStakingWeight is a sum of all stakers weight, not a single staker. And must to use TotalStakingAmount.

```solidity
TotalStakingWeight += _seconds.mul(TotalStakingAmount);
```

* [Testing data](https://gist.github.com/yuriy77k/28b349a4f4fbf71639d6d35b6d4357a7#3-testing-data) are commented out.