# TheWALL v2 Security Audit Report

# 1. Summary

[TheWALL v2](https://thewall.global/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19.

- [thewall.sol](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewall.sol).
- [thewallbeneficiaries.sol](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallbeneficiaries.sol).
- [thewallcore.sol](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol).
- [thewallcoupons.sol](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcoupons.sol).
- [thewallusers.sol](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallusers.sol).

# 3. Findings

In total, **4 issues** were reported including:


- 1 medium severity issues.

- 3 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1 Cluster Size

### Severity: medium

### Description

The issue described [here](https://gist.github.com/RideSolo/6803297f5bd12bca834269c2dea3e88f#31-cluster-size) is still applicable, if a user wants to remove an area from the cluster that is located in the end of the cluster array, then it will be impossible since the array size might be too large.

The loop does not require the user to go through the full array, but like this the user will be forced to remove areas in the beginning of the cluster (even if he wants to keep them) just to remove an area in the latest positions, then add them back later on.

### Code snippet

https://github.com/ridesoloAudit/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol#L422

## 3.2 Premium Computing

### Severity: owner privileges

### Description

The owner can `commitSecret` with various frequency or don't commit at all. 
The frequency at with the [commitSecret](https://github.com/ridesoloAudit/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol#L145) and [updateSecret](https://github.com/ridesoloAudit/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol#L153) is called is important to the users. If a user that created new area want to rent or sell it and the owner didn't call `commitSecret` for his hash then [isPremium](https://github.com/ridesoloAudit/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol#L347) will return false, resulting in a loss of 30% (fee charged) even if his nonce and secret will give him a premium area. If the secret is committed later, a possible new owner will have access to the premium privilege.

### Recommendation

- If commit secret periodically, then put the schedule on the website to inform users when they may get Premium.
- Lock the sell and rent functionalities and unlock them after the secret commit.
- Automate the process using an oracle callback such as oracalize or chainlink.

## 3.3. Owner privileges

### Severity: owner privileges

### Description

The owner has rights that can harm users:

1. Area price change.
2. Make wall size unlimited greater.

### Code snippet

* [1](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol#L134)
* [2](https://github.com/isvirin/TheWall/blob/8bd03c94dfc8ad9e1429b48da6cf3b51a7e59f19/thewallcore.sol#L127-L128)


# Conclusion

There are no critical issues. The highlighted issues have to be fixed.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/4eaceb82481bef06f1b48a2a6d2cf3e0

https://gist.github.com/gorbunovperm/7859498d0f78b0df0966961f63e91232

https://gist.github.com/MrCrambo/f207b7ff56d7a6b0e988003b3dec4c52

https://gist.github.com/danbogd/6ea8a1e09b556554b72f5c5a67603088
