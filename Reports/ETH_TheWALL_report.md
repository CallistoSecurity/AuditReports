# TheWALL Security Audit Report

# 1. Summary

[TheWALL](https://github.com/isvirin/TheWall) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 163e39b888c2364c708139d0916db6e63c4acc59 .

- [thewall.sol](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol).
- [thewallbeneficiaries.sol](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallbeneficiaries.sol).
- [thewallcore.sol](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol).
- [thewallcoupons.sol](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcoupons.sol).
- [thewallusers.sol](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallusers.sol).

# 3. Findings

In total, **14 issues** were reported including:

- 2 high severity issues.

- 2 medium severity issues.

- 3 low severity issues.

- 7 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Multiple Areas at the same coordinates

### Severity: High

### Description

`sendValue()` is not protected from reentrancy [1](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/f90700f3f19390c373d9f46793d240f229080db2/contracts/utils/Address.sol#L54-L57). In case of creating of new Areas this vulnerability can be used to create multiple Areas at the same coordinates. An attacker's contract can gain control [2](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L504) of the flow after  `_core._canBeCreated(x, y)` check is passed. And [`create()` function](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L165) can be repeatedly called.

### Code snippet

* https://github.com/OpenZeppelin/openzeppelin-contracts/blob/f90700f3f19390c373d9f46793d240f229080db2/contracts/utils/Address.sol#L54-L57

* https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L504


## 3.2 Premium Pre-computing

### Severity: High

### Description

if a newly created area using `_create` function member of `TheWallCore` do not meet the following condition, `if (_abs(x) <= 100 && _abs(y) <= 100)` a random value is computed through `_rand` function implemented in the same contract to give the premium value to that area or not.

`_rand` is relying on the blockhash of the last block. An attacker can make an exploit contract with the same PRNG code in order to call the target contract via an internal message. The “random” numbers for the two contracts will be the same. thus guessing if the create block will be premium or not.

the previous hack can be combine with an offchain verification of the newly created block hash to check if the premium value will be met or not then calling the hacking contract to ensure that the transaction execution is done on that next block only.

## 3.3 Cluster Size

### Severity: medium

### Description

The number of area to be added in a cluster should be limited either when using [`createMulti`](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L178) or when adding area to cluster one by one using [`_addToCluster`](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L352) or `_create` since function such as `_removeCluster` or `_removeFromCluster` can break due to a for loop that iterate on the cluster area array.

Other issues can raise like if an owner want to transfer an area that is inside a cluster, [`_canBeTransferred`](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L122) will throw since it doesn't allow area transfer that is within a cluster, if the previously described issue happen it will lock the users area from being transferred or sold to another user since he won't be able to remove it from his cluster.

Also it can raise "Out of Gas" issue in many other places (see *Code snippet*).

### Code snippet

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L90

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L103

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L187

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L215

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L175

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L396

https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallusers.sol#L111

## 3.4 Delegate Call

### Severity: medium

### Description

whitelisted admins are allowed to make delegate calls to external contract from `TheWallCoupons`, `TheWall`, `TheWallBeneficiaries` and `TheWallCore`, knowing that `delegateCall` allow its user to execute code contained in functions of `a` contract in the context of the calling contract, meaning that a malicious or erroneous code that wasn't audited can be executed on the audited contracts. Multiple risks can be highlighted like:

- Execution of erroneous code that will end by breaking the contracts logic or result of fund loss.
- Admin private key hack.
- Malicious admin. 

In conclusion for this issue the audit cannot guarantee the safety of the users since external code can be applied to the actual contract variables without prior audit.

### Code snippet

https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L311#L314
https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallbeneficiaries.sol#L131#L134
https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L532#L535
https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcoupons.sol#L97#100

## 3.5 Delegate Call Return Value

### Severity: low

### Description

Please note that the compiler raised the following issue "Warning: Return value of low-level calls not used. a.delegatecall(b)", `delegateCall` return two variable (bool, bytes) that must be handled.

### Code snippet

https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewall.sol#L311#L314
https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallbeneficiaries.sol#L131#L134
https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L532#L535

## 3.6 Zero Address Check

### Severity: low

### Description

- There are no zero address checking in functions [`setCouponsContract`](https://github.com/isvirin/TheWall/blob/master/thewallusers.sol#L59), [`giveCoupons`](https://github.com/isvirin/TheWall/blob/master/thewallusers.sol#L101), [`setFundsReceiver`](https://github.com/isvirin/TheWall/blob/master/thewallcore.sol#L116).

## 3.7 Wall Size

### Severity: low

### Description

When setting the wall size using [`setWallSize`](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L103) member of `TheWallCore`, the new size should not exclude any area that was created in the wall extremity, meaning that if the wall size is shrinked then some area can be excluded from it. there is no direct implication following my understanding however the UI must follow some rule and it might collide with this issue.

## 3.8 Area Creation

### Severity: owner privileges

### Description

`_create` function member of `TheWallCore` can be called by admins, however when assigning the `tokenId` 
`_areasOnTheWall[x][y]` no verification is done to check if a `tokenId` was preassigned to the same area.

same issue is also applicable to `tokenId` itself since the non-fungible erc721 token is create by the same function, `_tokens[tokenId]` meaning that if a `tokenId` was previously created it can be overwritten by an admin by mistake or intentionally.

`create` function member of `TheWall` validate if the area is empty by calling ` _core._canBeCreated(x, y)`, meaning that public calls are checked for area overwriting but the restricted function is not, admin or public user can do the same mistakes and should be checked the same way.

## 3.9 ERC223 Compliance (Burn Logic)

### Severity: owner privileges

### Description

The implemented `_burn` function allows the admins to burn users tokens without permission refer to [ERC223](https://github.com/Dexaran/ERC223-token-standard/blob/development/token/ERC223/ERC223Burnable.sol) description to implement the burning logic correctly. 

`_useCoupons` function member of `TheWallUsers` uses `_burn` function to burn coupons once used however a different logic can be implemented to avoid such drastic solution like simply transforming the coupons to the contract and using ERC223 `tokenFallback` since it was intended to handle transfer event on the contract side.

### Code snippet

https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcoupons.sol#L87#L95

https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallusers.sol#L82#L100

## 3.10 Owner privileges

### Severity: owner privileges

### Description

1. Admin can change the coupons contract address at any moment [here](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallusers.sol#L59#L62).

Most function implemented on `TheWallCore` can be called by an admin that is different than `TheWall` contract address, since it is deployed independently from the main contract, multiply privileges issues can raise like the following:

2. Admin can set any token for sale instead of the owner calling [`_forSale`](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L140#L146) member of `TheWallCore`.
3. Admin can set any token for rent instead of the owner by calling [`_forRent`](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L148) member of `TheWallCore`.
4. Admin can remove cluster using [`_removeCluster`](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L168) without owner permission.
5. Admin can [buy](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L250), [rent](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L281), [rentTo](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L318), [cancel](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L330), [finish](https://github.com/RideSolo/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L352) rent, `_addToCluster`, `_removeFromCluster` without owner permission since all input variables on those functions can be directly manipulated by the admin in the opposit of the non restricted function implemented in `TheWall` contract.


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/6803297f5bd12bca834269c2dea3e88f

https://gist.github.com/gorbunovperm/e301d0a190a3b1588afebcff46b1b6b2

https://gist.github.com/MrCrambo/2d077c19128b807bea4003b6ca61cfaa

https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6

# 6. Notes.

## 6.1. Note about MrCrambo report.

### [Referrer setting](https://gist.github.com/MrCrambo/2d077c19128b807bea4003b6ca61cfaa#1-referrer-setting)
User would set any other his address as referrer. Therefore add checking that referrer address not equal me address will not protect from is user wants to get referrer payment and only increase gas consumption. This is not security issue.

## 6.2. Note about danbogd report.

### [Truncated buy Coupons value](https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6#31-truncated-buy-coupons-value)

The value don't Truncated because `weiAmount` exactly divide by `_costWei` (the rest sends to buyer). Is not security issue.

### [Underflow may occur](https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6#35-underflow-may-occur).
The function `_useCoupons(me, areasNum);` can't return value greater then `areasNum`, so underflow imposable.

### [Known vulnerabilities of ERC-20 token](https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6#36-known-vulnerabilities-of-erc-20-token)
This contract implement ERC223 token.

### [No check referrerCandidate](https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6#37-no-check-referrercandidate)
User would set any other his address as referrer. Therefore add checking that referrer address not equal me address will not protect from is user wants to get referrer payment and only increase gas consumption. This is not security issue.

### [Non-initialized return value](https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6#38-non-initialized-return-value)
It's init here [t = token.tt;](https://github.com/isvirin/TheWall/blob/163e39b888c2364c708139d0916db6e63c4acc59/thewallcore.sol#L415)

### [Wrong comparison](https://gist.github.com/danbogd/922f5e4d1ead39b4c1a414caac0bb2d6#39wrong-comparison)
`rewardWithdrawTime` - the time when reward was withdrawn, `_lastDivideRewardTime` - when reward is divided.  So `_beneficiaries[s].rewardWithdrawTime >= _lastDivideRewardTime` is correct comparison.