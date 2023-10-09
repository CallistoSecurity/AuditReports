# Roulette.tech Security Audit Report

# 1. Summary

[Roulette.tech](https://github.com/smartgametech/callistoroulette/tree/master/contract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> A smart contract of Roulette game deployed on Callisto network. User can place multi bet at the same time, the maximum unique bets per time is 64 and maximum 64 credit on one bet.
> By default, each credit is 20 CLO. If user places under 200 CLO then they have a chance to win Jackpot.
> The system will deduct 1% value for jackpot

# 2. In scope

Commit hash: `c63e338983878924c266e54d07412d3be3842aff`

1. [FlatBetherRoulette.sol](https://github.com/smartgametech/callistoroulette/blob/c63e338983878924c266e54d07412d3be3842aff/contract/FlatBetherRoulette.sol)
2. [RouletteMatrix.sol](https://github.com/smartgametech/callistoroulette/blob/c63e338983878924c266e54d07412d3be3842aff/contract/RouletteMatrix.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 1 Critical severity issues.

- 1 high severity issue.

## 3.1. The Owner or Croupier can play drawings in their favor

### Severity: high

### Description

The calculation of a random number to determine the fallen number on the roulette depends on: bet block hash, player address, hash of block before the settle block. Settle block is the  block when the owner(or operator) has called the `settleBet(uint256)` function. Thus, the owner knows all the variables and can influence the result of the draw by calling the function in the block which will lead to the player's loss.

### Code snippet

* [FlatBetherRoulette.sol](https://github.com/smartgametech/callistoroulette/blob/c63e338983878924c266e54d07412d3be3842aff/contract/FlatBetherRoulette.sol#L466)

### Recommendation

Don't use `blockhash(settleBlock - 1)` variable to calculate a random number.

## 3.2. Game without losses

### Severity: Critical

### Description

An attacker may not pay for losing bets. Two possibilities of the contract allow to do it: refund of unsettled bets and using the `transfer` function upon payment of the winnings to the winner. Let's look at an example of a possible attack.

The attacker makes the following bets: 0, red, black. He spends 3 Сredit(`3 * 20 = 60 CLO`) for this. 

![3 bets](https://pp.userapi.com/c850632/v850632052/15f9ea/6k05PD90LYU.jpg)

This way covers all the variants of the number drawn on the roulette. That is, the attacker in any case will receive a prize. If red or black number appears on the roulette wheel, the attacker will get x2 (`40 CLO`). If zero appears then an attacker will get x36 (`720 CLO`). In both cases an attacker will get a reward by `transfer` function. The peculiarity of this function is that in the case of `throw` on the recipient's side the entire transaction will be rollback. `throw` can be done intentionally by an attacker, if the recipient is another smart contract. Thus, if the attacker receives a payment of `40 CLO`(black or red bet), he can cancel it and the owner will not be able to make this draw. And after 256 blocks after the bet, he will be able to [request a refund](https://github.com/smartgametech/callistoroulette/blob/c63e338983878924c266e54d07412d3be3842aff/contract/FlatBetherRoulette.sol#L446). Thus, in case of loss, the attacker will not lose anything, and in case of winning will receive a x12 win — 60 CLO spend and get 720 CLO.

Also, the attacker can increase the probability and reduce the gain, such as a bet: 

![9 bets](https://pp.userapi.com/c850020/v850020052/1c385a/dDTU_boxA04.jpg)

### Code snippet

* [FlatBetherRoulette.sol, payment for winner](https://github.com/smartgametech/callistoroulette/blob/c63e338983878924c266e54d07412d3be3842aff/contract/FlatBetherRoulette.sol#L416)
* [FlatBetherRoulette.sol, refund](https://github.com/smartgametech/callistoroulette/blob/c63e338983878924c266e54d07412d3be3842aff/contract/FlatBetherRoulette.sol#L446)

### Recommendation

Use `send` method instead a `transfer`.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b559cc63f98c146e6815c3695d72b8fd

https://gist.github.com/yuriy77k/85713086fdbe6de2b392a8e152729f53

https://gist.github.com/yuriy77k/f5d40a9bb9c74707f8ed352d03db7f5b
