# LuckyStrike v4 Security Audit Report

# 1. Summary

[LuckyStrike v4](https://lucky-strike.io/game/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [LuckyStrike](https://ropsten.etherscan.io/address/0xbce45fee20ebfa7ee8c0e6ee9755753883a48b05#contracts).
- [LuckStrike Token](https://ropsten.etherscan.io/address/0x830991dc0bd8250def572bacd01f4c4cacb1fdb7#contracts).

# 3. Findings

In total, **7 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 3 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Truncated Value (Invest & Play)

### Severity: medium

### Description

let's say that X is the invest and play amount:

- 1/5 will be used to mint tokens ( token price is 0.00015 eth).
- 4/5 will be used on bet (ticket price is 0.02)

- The value of tokens bought will be (1/5) * X /0.00015. In order to not have truncated value when computing the tokens bought, X has to be a multiple of A that is equal to 0.00015 / (1/5).
- The value of tickets bought will be (4/5) * X / 0.02.  In order to not have a truncated value when computing the number of ticket bought, X has to be a multiple of B that is equal to 0.02 / (4/5).

Both condition cannot be satisfied at the same time since B is not a multiple of A. the consequences is that either a truncation will happen when computing the bought tokens or when computing the number of tickets to be played.

Also as a reply the the previous comment of the developers ["The best way to reduce truncated ether is to solve it at the frontend (allowing user to buy certain amount of tickets), and not to make additional calculations in SC"](https://github.com/EthereumCommonwealth/Auditing/issues/152#issuecomment-479651484) even the frontend cannot solve this since there is no possible way to meet both conditions.

### Code snippet

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1819#L1829

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1827

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1757

## 3.2. Jackpot Play Logical Error

### Severity: note

### Description

Users that play first have more chances to win the jackpot since their addresses will always stay eligible for all kind of jackpot, meaning that new comers have less and less chances to win the any kind of jackpot since their address have been present for less time.

This issue is caused by not removing addresses once the a specific jackpot type is played.

The number of tickets playing in a jackpot is always set to ticketsTotal, that represent all the past tickets bought, for example a daily jackpot should include tickets that were bought that day only and not the whole tickets bought since the start of the game, the developers should include a range of tickets that can only win following the time when they were bought in accordance to the jackpot type.

The issue will demotivate users to play since they will have less chances to win the jackpot if the ticket list is long enough, also please note that a same ticket can be randomly picked multiple times and be eligible for unlimited jackpot types since the ticket is not removed once he wins.

### Code snippet

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L2055

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L2101

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L2112

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1779#L1781

## 3.3. Unplayed Bet

### Severity: low

### Description

If a bet is not played within 255 blocks since the bet was initiated the player won't be eligible to challenge the king of the hill but he will still be eligible for the different jackpots, however the player bet value is not distributed over all different jackpots using `allocateSum` function as used in [here](https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1968), this issue represent a direct logical error, and also loss for the players that are running for the jackpots.

### Code snippet

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1903#L1917

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1649#L1716

### Recommendation

The player bet should be distributed over all different jackpots using `allocateSum` if bet is unplayed. For example in line 1918.

## 3.4. Block Gas Limit

### Severity: note

### Description

The number of tickets that a user can buy is limited by the amount of gas allowed to the transaction and with a maximum of block gas limit since there is a loop that set the lottery tickets `theLotteryTicket`  to their owners one by one.

This issue can just cause transaction throw for out of gas, if the amount of ether to be played is too high.

### Code snippet

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1779#L1781

## 3.5. Owner Privileges

### Severity: owner privileges

### Description:

- `adjustAllocation` function allows the owner to reset the rates of the different jackpots and income rate as wished.
- 70M tokens are first distributed by the owner that represent 10500 ether, the token sale hardcap is 4500 ether,  meaning that the developers allow them self more than a third of the total income of the bet game, investors have to be aware of such usage.

### Code snippet

https://gist.github.com/RideSolo/6c250a3cd6df86f07c3b5459dbb92283#file-luckystrike-sol-L1591#L1629

https://gist.github.com/RideSolo/6bcabf27e0d738136c4187294b967008#file-lst-sol-L159#L164

## 3.6. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.7. Possibility of minting more than hardCap

### Severity: note

### Description

Function `mint` allows owner to mint more tokens than hardCap. 

### Code snippet

https://gist.github.com/RideSolo/6bcabf27e0d738136c4187294b967008#file-lst-sol-L353

# 4. Conclusion

The highlighted issues should be fixed before deploying the audited contracts.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0ab37d365e5a49864a92065ef977d4b7

https://gist.github.com/yuriy77k/062ccb91ccb0e1ac317969407e40d1c2

https://gist.github.com/yuriy77k/988cf0e4b5da6393442374944deb08ae
