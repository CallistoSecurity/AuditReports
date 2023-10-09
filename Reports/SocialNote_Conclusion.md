# 1. Summary

[SocialNote](https://github.com/socialnoteio/socialnote/blob/master/SocialNote.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [SocialNote.sol](https://github.com/socialnoteio/socialnote/blob/master/SocialNote.sol) github commit hash 78e2eeefc7a873defa86e8b2c2c68c10468bdbc5.

# 3. Findings

In total, **4 issues** were reported including:

- 4 low severity issues.

## 3.1. Wrong Requirement

### Severity: low

### Description

`batchTransfer` function require `p_tokens <= balances[msg.sender]` when in reality `p_tokens` amount is transfered muliple times following the lenght of `_recipients` address array.

### Code snippet

https://github.com/socialnoteio/socialnote/blob/78e2eeefc7a873defa86e8b2c2c68c10468bdbc5/SocialNote.sol#L166

### Recommendation

```solidity
require(p_tokens * _recipients.length <= balances[msg.sender]);
```

## 3.2. Transfer to Address 0x0 and Burn Mechanism

### Severity: low

### Description

Transfer to address zero in the audited contract is used as basic burn mechanism (check `totalSupply`, `balances[address(0)]` is deducted from the total supply), this mechanism open the doors also for sending tokens by mistake to address 0x0, `transfer` and `transferFrom` functions do not require `to` address to be non null.

### Code snippet

https://github.com/socialnoteio/socialnote/blob/78e2eeefc7a873defa86e8b2c2c68c10468bdbc5/SocialNote.sol#L198-L203

https://github.com/socialnoteio/socialnote/blob/78e2eeefc7a873defa86e8b2c2c68c10468bdbc5/SocialNote.sol#L230-L236

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.4. Unlock tokens mechanism don't work properly.

### Severity: low

### Description

In whitepapper stated:

```
Airdrops - 500,000,000 tokens (5%) - Locked for 3 months to prevent the dumping after exchange listing

Devs & Advisors - 1,500,000,000 tokens (15%) - Locked for 6 months

Future Development - 2,500,000,000 tokens (25%) - Locked for 9 months

Future Marketing - 1,000,000,000 (10%) - Locked for 12 months

Future Partnerships - 1,500,000,000 (15%) - Locked for 15 months
```

But current realization of Unlock Tokens mechanism has some issues:

* new locked period starts from the time of last call function ```withdrawLockedTokens()``` and not as scheduled;

* unlocked amounts don't match with Whitepaper.

### Code snippet

https://github.com/socialnoteio/socialnote/blob/78e2eeefc7a873defa86e8b2c2c68c10468bdbc5/SocialNote.sol#L103-L110

https://github.com/socialnoteio/socialnote/blob/78e2eeefc7a873defa86e8b2c2c68c10468bdbc5/SocialNote.sol#L131-L135

https://github.com/socialnoteio/socialnote/blob/78e2eeefc7a873defa86e8b2c2c68c10468bdbc5/SocialNote.sol#L137-L158


## 4. Conclusion

No critical vulnerabilities were detected, but need to fix some issues before deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/66d92e0fb8a256d39041f82558a287a7

https://gist.github.com/yuriy77k/2ebdba824d9caf3c27d8fce8d264af4f

https://gist.github.com/yuriy77k/efa6ca9722ca250bed4249e7b824c885
