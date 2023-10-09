# 1. Summary

[mHealthCoin](https://github.com/mhealthcoin-ICO/MHEC-token/blob/master/MHEC.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [MHEC.sol](https://github.com/mhealthcoin-ICO/MHEC-token/blob/master/MHEC.sol) github commit hash f5db9bbd271c01ddcdacc5e49e8e865dfc6530cb.

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. Non-Emitted Event

### Severity: minor

### Description

`Transfer` event should be emitted in the constructor after assigning the `totalSupply` to the msg.sender.

### Code snippet

https://github.com/mhealthcoin-ICO/MHEC-token/blob/master/MHEC.sol#L31#L40

# 4. Conclusion

The audited contract is safe.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b916f45412ae41b0c21e22ea82ed6010

https://gist.github.com/yuriy77k/f753234330caec804ec4a04bad757e49

https://gist.github.com/yuriy77k/dd73aac1a3413e46dc488c891a53c48f
