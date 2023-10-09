# 1. Summary

[Bitcademy V4](https://github.com/bitcademyfb/Bitcademy_ICO) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BitcademyToken.sol](https://github.com/yuriy77k/Bitcademy_ICO/blob/patch/contracts/BitcademyToken.sol) github commit hash 3d5e341ca04b20e4ea4f93a9a28e106a38571e36.
- [BitcademyVesting.sol](https://github.com/yuriy77k/Bitcademy_ICO/blob/patch/contracts/BitcademyVesting.sol) github commit hash 072d747e38ba5da87a021bcf2758be62b9795477.
- [Crowdsale.sol](https://github.com/yuriy77k/Bitcademy_ICO/blob/patch/contracts/Crowdsale.sol) github commit hash ff59c42210cf5f8c336de7e36ffc76e31c38e7d7.
- [PreICOBitcademyGold.sol](https://github.com/yuriy77k/Bitcademy_ICO/blob/patch/contracts/PreICOBitcademyGold.sol) github commit hash ff59c42210cf5f8c336de7e36ffc76e31c38e7d7.
- [RefundVault.sol](https://github.com/yuriy77k/Bitcademy_ICO/blob/patch/contracts/RefundVault.sol) github commit hash ff59c42210cf5f8c336de7e36ffc76e31c38e7d7.

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

## 3.1. ICO Autonomy

### Severity: low

### Description

- `updateReleaseDate` function allow the contract owner to extend the token locking period, making the investors wait more to be able to withdraw their tokens.

- `adjustCloseDate` function allow the contract owner to extend the ICO close time. For example if the owner private key is hacked, an attacker can set the `closingTime` value so far into the future that `finalize` function will throw at each call. all the fund collected fund and tokens will be locked.

Many consequences can be described concerning the above issue, contract developers should make the ICO as autonomous as possible.

This issue is applicable for both `Crowdsale` and `PreICOBitcademyGold` contracts.

### Code snippet

https://github.com/yuriy77k/Bitcademy_ICO/blob/ff59c42210cf5f8c336de7e36ffc76e31c38e7d7/contracts/Crowdsale.sol#L546

https://github.com/yuriy77k/Bitcademy_ICO/blob/ff59c42210cf5f8c336de7e36ffc76e31c38e7d7/contracts/Crowdsale.sol#L555

https://github.com/yuriy77k/Bitcademy_ICO/blob/ff59c42210cf5f8c336de7e36ffc76e31c38e7d7/contracts/PreICOBitcademyGold.sol#L429

https://github.com/yuriy77k/Bitcademy_ICO/blob/ff59c42210cf5f8c336de7e36ffc76e31c38e7d7/contracts/PreICOBitcademyGold.sol#L438


## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 4. Conclusion

The audited contracts are safe to deploy.

