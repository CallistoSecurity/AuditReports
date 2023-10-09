# FriendsFingers Security Audit Report

# 1. Summary

[FriendsFingers](https://www.friendsfingers.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [FriendsFingersBuilder.sol](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol) github commit hash a5905720f22ccb643e295ce9c1ee868407c0cc6e.
- [FriendsFingersCrowdsale.sol](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol) github commit hash d2fd56163eb87493b6fc367b8c89a21295322361.
- [FriendsFingersToken.sol](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/token/FriendsFingersToken.sol) github commit hash 8149bc64ec430f8066f3d3f48688b10f58f1b3cf.
- [ContractReceiverImpl.sol](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/utility/ContractReceiverImpl.sol) github commit hash ce4d887429f563728b81648714fcc416ee53c656.
- [ContractReceiverInterface.sol](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/utility/ContractReceiverInterface.sol) github commit hash ce4d887429f563728b81648714fcc416ee53c656.
- [SafeContract.sol](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/utility/SafeContract.sol) github commit hash ce4d887429f563728b81648714fcc416ee53c656.

# 3. Findings

In total, **6 issues** were reported including:

- 1 critical severity issues.

- 1 low severity issues.

- 4 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Crowdsale Finalization

### Severity: Critical

### Description

`closeCrowdsale` function member of `FriendsFingersBuilder` designed to finalize the crowdsale is only accessible by owner or the crowdsale creator, however `finalize` function member of `FriendsFingersCrowdsale` is public.

If any attacker calls `finalize` directly and all the condition to finalize the crowdsale are met, FriendsFingersBuilder's function `closeCrowdsale` won't execute since it calls `finalize` function that will throw since it was previously called. This will leave the token contract in a minting state since `finishMinting` can't be called anymore.

The Token contract is designed to block all transfers until mintingFinished is set to true, meaning that an attacker can block the newly deployed token transfers for all users and makes the ICO fail.

### Code snippet

https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol#L152

https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L133

https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/token/FriendsFingersToken.sol#L17

https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/token/FriendsFingersToken.sol#L33

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.3. Owner Privileges

### Severity: owner privileges

### Description

FriendsFingers dapp is a tool for entrepreneur to deploy ICO without the need to deploy a dapp or develope it by themselves, however people using FriendsFingers should be aware that they have no control over the ICO, everything has to handled through FriendsFingers owner.

1. Pause/Unpause [`claimRefund`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L123), [`buyTokens`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L97) and [`finalize`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L133) without any restriction using [`pauseCrowdsale`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol#L198)/[`unpauseCrowdsale`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol#L203)(please not that for example pausing `finalize` will allow FriendsFingers owner to possibly withdraw the fund collected by the ICO that does not belongs to them in anyway using [`safeWithdrawal`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L172) or [`setExpiredAndWithdraw`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L177) after a year from the end of the ICO) .
2. Block a crowdsale without permission from the ICO creator using [`blockCrowdsale`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol#L208) and allow the users to withdraw their ether supposing that no action were taken by FriendsFingers to withdraw the investors fund.
3. FriendsFinger's owner can change the rate applied to the ICO at any moment using [`setFriendsFingersRateForCrowdsale`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol#L186) or [`setDefaultFriendsFingersRate`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/FriendsFingersBuilder.sol#L175).

## 3.4. Block refund

### Severity: owner privileges

### Description

During crowdsale creation, the owner may set the wrong `endTime` and withdraw Ethereum before investors get a refund.
[`claimRefunds`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L123) function could be failed, if owner will set end time as one year ago date and send himself ethers using function [`safeWithdrawal`](https://github.com/FriendsFingers/friendsfingers-smartcontracts/blob/master/contracts/crowdsale/FriendsFingersCrowdsale.sol#L172).

### Recommendation

Add code below:
```solidity
require(_endTime > _startTime);
```

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/dee4dd5975dad8c0ca82deddfd5c5567

https://gist.github.com/MrCrambo/516f38cdbc71b70c4e8dcfdf33505b3b

https://gist.github.com/yuriy77k/d149fca062b286f839a4d39f6ca693d8
