# 1. Summary

[LuckyStrike Project](https://lucky-strike.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [LuckyStrikeTokens.sol](https://gist.github.com/yuriy77k/8111757d30637066b3b4bdb60b3525d0)
- [LuckyStrike.sol](https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305)


# 3. Findings

In total, **8 issues** were reported including:

- 2 medium severity issues.

- 4 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Fallback function 

### Severity: medium

### Description

Users might not understand the sales functionality and deposit ether directly to contract like most ICO does and not get tokens. The token minting is done using `LuckyStrike` Contract address which means that if a user send ether by accident the team wouldn't be able to do anything to refund him since they don't have a direct access.

### Code snippet

https://gist.github.com/yuriy77k/8111757d30637066b3b4bdb60b3525d0#file-luckystriketokens-sol-L359#L361

### Recommendation

Only `owner` address should be allowed to deposit tokens (`owner` is set to be `LuckyStrike`). 

## 3.2. Truncated Dividends Value 

### Severity: medium

### Description

`takeDividends` function member of `LuckyStrikeTokens` contract compute the dividends value for a certain amount of token specified by the `msg.sender`, however the dividends value is truncated when the contract balance is first devided by the `totalSupply`.
for a correct computation the contract balance should be first multiplied by `valueInTokens` specified by the user then devided by the `totalSupply`.

### Code snippet

https://gist.github.com/yuriy77k/8111757d30637066b3b4bdb60b3525d0#file-luckystriketokens-sol-L168

## 3.3. Owner Withdrawal 

### Severity: low

### Description

`withdrawAllByOwner` function member of `LuckyStrikeTokens` is supposed to allow the owner to withdraw ether from the contract if `totalSupply` is null and the sales period has ended. 
however following `takeDividends` function no ether is supposed to be left if `totalSupply` is null.

The only way that this function is useful is if ether is sent to the contract through the fallback function, and as highlighted before the fallback function should be removed.

### Code snippet

https://gist.github.com/yuriy77k/8111757d30637066b3b4bdb60b3525d0#file-luckystriketokens-sol-L183#L189

## 3.4. Users Ether Loss (Invest & Play)

### Severity: low

### Description

If the amount of ether sent to the contract through `invest`, `investAndPlay` or `placeABet` is lower than `ticketPriceInWei` or not equal to the exact multiple of `ticketPriceInWei` the ether or part of it will be lost by the user since if its value is lower than `ticketPriceInWei` he won't get a ticket to play or a token as investment, `msg.value` is directly divided by `ticketPriceInWei`.

### Code snippet

https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1707

https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1766

https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1795

### Recommendation

The remaining ether after each operations should be sent back to the user.

## 3.5. placeABet, Invest and InvestAndPlay

### Severity: minor observation

### Description

- When using `investAndPlay` the fifth of the `msg.value` is added the he marketing fund, the bet value is set to be `msg.value` minus the fifth of it, however when minting `msg.value` is divided by `ticketPriceInWei` to get the tokens to be minted for that user knowing that 4/5 of `msg.value` was used for the bet only 1/5 should be used to compute the tokens amount to be minted not the whole `msg.value`.

- When investing using `invest` the user get 75% tokens bonus.

- When placeABet is used the whole amount is set for the bet.

Following the previous points each step allow different token bonuses and ticket number, contract developers should explain their intentions and the users should be informed to avoid confusion.

### Code snippet

https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1795#L1803

https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1754#L1780

https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1699#L1730

## 3.6. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.7. Zero address checking required

### Severity: low

### Description

No zero address checking in functions [transferFrom](https://gist.github.com/yuriy77k/8111757d30637066b3b4bdb60b3525d0#file-luckystriketokens-sol-L204), [init](https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1494)

## 3.8. Wrong event names

### Severity: minor observation

### Description

Event names should start with uppercase letter, but it start with lowercase.
Lines [1415](https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1415), [1454](https://gist.github.com/yuriy77k/2d80694c23b89c543e832715b0b89305#file-luckystrike-sol-L1454), etc.

# 4. Conclusion

The Audited contracts are not fully safe, the contracts developers should take the highlighted issues into consideration.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1b39d542b89c8462ad258089105db637

https://gist.github.com/yuriy77k/764471730493e66ca9979d7c8b2a65bb

https://gist.github.com/yuriy77k/9a8422217622491e4f2c50b73abebe1a
