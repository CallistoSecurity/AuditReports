# 1. Summary

[KIMEX Token](https://github.com/kimexofficial/token) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

KIMEX Token (KMX) and Sale Smart Contract (S.S.C) Our smart contract’s designed and developed based on ERC20 standard followed strict procedure of software development from requirement analyzing, designing, developing, unit and business testing, auditing and deploying. The code’s simplified and optimized for both of business and coding quality criteria.

https://www.kimex.io/

# 2. In scope

- [KIMEX.sol](https://github.com/kimexofficial/token-1/blob/master/KIMEX.sol) github commit hash 1d969f020236016b1759810ad4fe3adcc08ad458.

# 3. Findings

In total, **8 issues** were reported including:

- 2 medium severity issues.

- 5 low severity issues.

- 1 minor observation.

## 3.1. Owner Privileges

### Severity: medium

### Description

The contract owner allow himself to:

 - Set token price at any moment, investors should check the token price before buy and see if it is concordant with the ICO stage.
 - Missing burn function after crowdsale end, with `allocateSalesTokens` function allowing them to manually transfer sales allocation at any moment to any address.
 - Start/stop ICO at any moment which will allow the team and founder to withdraw their token even before the ICO, for more information check issue 3.4.
 - Activate/Deactivate the ICO.
 - Withdraw ETH funds before the end of sales and if the soft cap is not reached. 

### Code snippet

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L255

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L354

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L234

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L244

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L262

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L268

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L398

## 3.2. There is no mechanism for refund.

### Severity: medium

### Description

There is no mechanism for refund in case of the soft cap is not achieved. Also, you will not be able to return the funds transferred to `loadFund` function.

### Code snippet

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L184

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L395

## 3.3. Bonus Distribution

### Severity: low

### Description

Even if the whitepaper state different bonuses for different ICO stages, the contract do not implement any function to calculate those values. however, the bonus amount can be included in `icoStandardPrice`. 

## 3.4. Founder & Team Token Vesting Period

### Severity: low

### Description

As we can see [here](https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L88) the token allocated for the team and founders should be blocked for 1 year, however, the implemented function used to distribute the allocations do not follow the descriptions.

- `allocateTokensForTeam` allow 40% right after the ICO end, and 60% after 1 year.
- `allocateTokensForFounder` allow 100% after the IC end.

### Code snippet

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L306

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L321

## 3.5. Allocations Distribution

### Severity: low

### Description

Allocations distribution can be for different purpose than what was intended at first.

Even if the following amount of tokens are hardcoded in the contract (`reservedAllocation`,`salesAllocation`) for specific use, the functions used for distribution allow owner or admin to send them to any address.

### Code snippet

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L167#L168

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L344

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L354

## 3.6. Transfer Event

### Severity: low

### Description

All the following function should emit a `Transfer` event:

- `allocateReservedTokens`
- `allocateSalesTokens`
- `allocateTokensForTeam`
- `allocateTokensForFounder`
- `issueTokens`

Without such events most explorers won't show the internal transfers, and investors won't be able to track project team transfers.

### Code snippet

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L306

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L321

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L344

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L354

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L390

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.8. Total supply

### Severity: minor observation

### Description

Sum of all [allocations](https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L94-L97) doesn't give full amount of [`totalSupply`](https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L75)

### Code snippet

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L94-L97

https://github.com/kimexofficial/token/blob/2b67a34fa77a6cd9541e40b503352f06b9a74738/KIMEX.sol#L75

# 4. Conclusion

This smart contract does not provide any protection for investors. The ICO admins can spend invested ETH at any time (even before ICO end) and distribute reserved KIMEX tokens to any addresses manually. This is contrary to the principles of ICO. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/6e7799dd597b69e12703dfa21278c1e1

https://gist.github.com/yuriy77k/ee09b9bed9f31eb2b32f4bcca7fc3637

https://gist.github.com/yuriy77k/fd70fde0668f7c9c268db6793495ac88
