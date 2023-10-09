# LuckyStrike V7 Security Audit Report

# 1. Summary

[LuckyStrike](https://lucky-strike.io/game/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [LuckyStrike](https://ropsten.etherscan.io/address/0xc0f92e31070460d310c81689199c9b496fc4a84e#code).
- [LuckStrike Token](https://ropsten.etherscan.io/address/0x71a92934c32f1c6cdf776fe411728c164c473309#code).

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Truncated Value (Invest & Play)

### Severity: low

### Description

Since the token does not have decimals, the truncated value issue will happen in line 1859. But the loss value will be less than the price of the token, which is less than the transaction fee.

```solidity
uint256 tokensToMint = sumToMarketingFund / tokenPriceInWei;
```

## 3.2. Owner privileges

### Severity: owner privileges

### Description

- adjustAllocation function allows the owner to reset the rates of the different jackpots and income rate as wished.
- 70M tokens are first distributed by the owner that represent 10500 ether, the token sale hardcap is 4500 ether, meaning that the developers allow them self more than a third of the total income of the bet game, investors have to be aware of such usage.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7ec5a6253cdf01d3b81e63eed16caf9f

https://gist.github.com/yuriy77k/cb7167e1a1a82c95a3af50c55c076879

https://gist.github.com/yuriy77k/1b207c88f6be849189223ff5aaab71b6
