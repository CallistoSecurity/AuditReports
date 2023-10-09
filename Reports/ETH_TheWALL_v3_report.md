# TheWALL v3 Security Audit Report

# 1. Summary

[TheWALL v3](https://thewall.global/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 4d7b98e06def9a605aba06663d5c90d5a5e55c54.

- [thewall.sol](https://github.com/isvirin/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewall.sol).
- [thewallbeneficiaries.sol](https://github.com/isvirin/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallbeneficiaries.sol).
- [thewallcore.sol](https://github.com/isvirin/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallcore.sol).
- [thewallcoupons.sol](https://github.com/isvirin/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallcoupons.sol).
- [thewallusers.sol](https://github.com/isvirin/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallusers.sol).

# 3. Findings

In total, **2 issues** were reported including:

- 2 owner privileges.

No critical security issues were found.

## 3.1. Premium Computing

### Severity: owner privileges

### Description

The owner can `commitSecret` with various frequency or don't commit at all.
The frequency at with the [`commitSecret`](https://github.com/ridesoloAudit/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallcore.sol#L146) and [`updateSecret`](https://github.com/ridesoloAudit/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallcore.sol#L154) is called is important to the users. If a user that created a new area wants to rent or sell it and the owner didn't call commitSecret for his hash then `isPremium` will return false, resulting in a loss of 30% (fee charged) even if his nonce and secret will give him a premium area. If the secret is committed later, a new owner may have access to the premium privilege.


## 3.2. Area price

### Severity: owner privileges

### Description

Owner can [change](https://github.com/isvirin/TheWall/blob/4d7b98e06def9a605aba06663d5c90d5a5e55c54/thewallcore.sol#L133) area price.

# 4. Conclusion

The audited smart contract can be deployed. Investors and users should be informed of owner privileges.


# 5. Revealing audit reports

https://gist.github.com/RideSolo/38e59572c49225cddeddeae8c3fc82b5

https://gist.github.com/gorbunovperm/e56d8adfa6bbba0562d78ea7fcd1625c

https://gist.github.com/MrCrambo/20960d441ffdc3bb650cee8264e0ec9e

https://gist.github.com/danbogd/f0801e6c2e3154cbea547425dfa59ac6

