# 1. Summary

[Metadollar MFG](https://etherscan.io/address/0x563b33bcb909a2ca43d4b8f3a550f49de1226766#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Metadollar_MFG.sol](https://etherscan.io/address/0x563b33bcb909a2ca43d4b8f3a550f49de1226766#code)

# 3. Findings

**3 issues** were reported including:

- 1 medium severity issue.

- 2 low severity issues.

### 3.1. Deception Scheme

### Severity: medium

### Description

Following the description "METADOLLAR FUND GOLD 50% entry fee distributed to token holders. Maximized dividend distribution. Focus on earning more dividends".
Maximized dividend distribution isn't true, from the 50% entry fee we have 24% that goes as dividends, 25% for the referrals logic, and 1% for the owner.

The referrals logic include three referrals levels, if the buyer do not set a referral address the 25% will go to the owner, and at each other level if the referral address was not set a certain percent goes to the owner.

Adding another token fee of 1% that goes to the owner when a transfer is made, and 1% more when selling tokens.

The implemented scheme tend to maximize the contract owner earnings not the dividends. Please note that in the original implementation, if a referral reward is not giving, the reward is giving as dividends to maximize users earnings.

### Code snippet

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L248#L250

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L258

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L277

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L280#L281

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L284

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L164#L167

https://gist.github.com/yuriy77k/d55ed73557a1a9c4347c1f49da423897#file-metadollar_mfg-sol-L134#L138

### 3.2. Token Transfer to Address 0x0

### Severity: low

### Description

`transfer` function do not require the `_toAddress` to be non null before transfer. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://gist.github.com/RideSolo/af809ba9ce20df16c8ac26102b43e6c9#file-metadollar_mfg-sol-L156

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://gist.github.com/RideSolo/af809ba9ce20df16c8ac26102b43e6c9#file-metadollar_mfg-sol-L156


# 4. Conclusion

Contract is safe, but do not follow its description.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7203afdb7f50fcafc4921f0aeb1df28b

https://gist.github.com/yuriy77k/9bd01fb3130c73a42c1aafd485dba448

https://gist.github.com/yuriy77k/b657f74c4b4571364b59422ce2102dcd
