# WednesdayClub Security Audit Report

# 1. Summary

[WednesdayClub](https://wednesdayclub.app/w/club) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [SafeMath.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/SafeMath.sol) github commit hash 92f2aab40a6e36bc85853f2a109e737e0868a3b8.
- [WednesdayClub.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol) github commit hash ab55e6f6fba0d5ce1c0b7274efa2e719e13fb1f3.
- [WednesdayClubComment.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClubComment.sol) github commit hash ee647b448090707cb33187f4fa282a75345da087.
- [WednesdayClubPost.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClubPost.sol) github commit hash 825e162d32dfdc50b2a63345be1f150ddf52f4f4.
- [WednesdayClubUser.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClubUser.sol) github commit hash 825e162d32dfdc50b2a63345be1f150ddf52f4f4.
- [destructible.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/destructible.sol) github commit hash 0c98cbc694e12ef044bedc1aa56c6a4f221903b7.
- [ownable.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/ownable.sol) github commit hash 0c98cbc694e12ef044bedc1aa56c6a4f221903b7.
- [pausable.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/pausable.sol) github commit hash 14ca8fa8d6ea69d06f4801194addc680d214a13a.
- [repayable.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/repayable.sol) github commit hash 92f2aab40a6e36bc85853f2a109e737e0868a3b8.
- [tokenInterfaces.sol](https://github.com/aelchim/WednesdayClub/blob/master/src/tokenInterfaces.sol) github commit hash 0c98cbc694e12ef044bedc1aa56c6a4f221903b7.

# 3. Findings

In total, **8 issues** were reported including:

- 2 high severity issues.

- 4 low severity issues.

- 1 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Block Gas Limit

### Severity: High

### Description

All the following functions have a high risk to throw for `Out of Gas` or `Block Gas Limit` since the post or comment Id to be deleted is saved inside an array and a loop is used to find it.

- `deleteUserPost`
- `deleteIdFromPostIds`
- `deletePost`
- `deleteUserComment`
- `deleteComment`
- `deleteAllPosts`

The more a user uses the Dapp to share post or comment the longer the array will get, meaning that for the long term no work around can be done by the owner to avoid it.

Same issue is applicable for `unfollow` function where the array is a list of following or followers and `unblockUser` where the array is a list of blocked users.

Please note that once the id is found no break is used to exit the loop.

Following up with the same error:

- `nukeMe`
- `nukePosts`
- `nukeComments`

Have the same issue where there might be not enough of gas to delete the whole list if it is long enough, also it might reach `Block Gas Limit`.

### Code snippet

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L100

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L115

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L140

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L201

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L217

https://github.com/RideSolo/WednesdayClub/blob/master/src/WednesdayClub.sol#L268

https://github.com/RideSolo/WednesdayClub/blob/master/src/WednesdayClub.sol#L299

https://github.com/RideSolo/WednesdayClub/blob/master/src/WednesdayClub.sol#L324

https://github.com/RideSolo/WednesdayClub/blob/master/src/WednesdayClub.sol#L330

https://github.com/RideSolo/WednesdayClub/blob/master/src/WednesdayClub.sol#L339

### Recommendation

Mappings can be used to keep track of the array elements id without looking through the whole array for the a specific post or comment to delete.

## 3.2. Misuse Of `delete`

### Severity: High

### Description

Developers should be aware that when delete is applied on an array id the value assigned to that id is reset to zero but the array length stays the same, meaning that the dev should implement a logic where the last element on the array will replace the deleted id and the array length should be decremented by one.

A function such `deleteAllPosts` is useless and does not meet the goal set by the dev (following the comments `deleteAllPosts in groups i.e. delete 100, then 100 again, etc - for saving on gas and incase to many`) since the first 100 deleted post will be just set to zero but the array length of `postIds` will stay the same. If the total number of posts is high enough the transaction can reach block gas limit.

Please note that delete is also used in other functions, and the delete logic should be updated to a better one. 

### Code snippet

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L130

## 3.3. Post & Comment Creation

### Severity: low

### Description

- When creating a post using `writePost` the structure saved does not include `_content` and `_media` parameters (except the emitted event nothing can retrace the post to its included media or content), developers should always keep the link to the IPFS data inside the post structure and make it immutable. 

- Same issue is applicable for `writeComment`, nothing directly link the content and the media to the comment id (except the emitted event).

### Code snippet

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L50#L67

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L150#L168

## 3.4. Post & Comment ID

### Severity: note

### Description

- The inputted post id in `writePost` is not the same final id that is used to track the post itself.
- The same note is applicable for `writeComment`.

This can lead users to confusion except if handled by the Dapp.

### Code snippet

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L53

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L153

## 3.5. FallBack Function

### Severity: low

### Description

The fallback function is used to deposit ether to the contract, however it can be misused by users. Developers should create a dedicated function for deposit with a unique name.

### Code snippet

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L44

## 3.6. Owner Privileges

### Severity: owner privileges

### Description

- Pause/Unpause the posts, comments, likes and follow for users through `whenNotPaused`.
- Delete posts and comments.
- Owner can change amount for post, report, like, follow at any time. And also he can change interval times.

### Code snippet

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L100

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L115

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L140

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L201

https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L217

https://github.com/RideSolo/WednesdayClub/blob/master/src/WednesdayClub.sol#L268

https://github.com/aelchim/WednesdayClub/blob/ab55e6f6fba0d5ce1c0b7274efa2e719e13fb1f3/src/WednesdayClubPost.sol#L77

https://github.com/aelchim/WednesdayClub/blob/ab55e6f6fba0d5ce1c0b7274efa2e719e13fb1f3/src/WednesdayClubPost.sol#L89

https://github.com/aelchim/WednesdayClub/blob/ab55e6f6fba0d5ce1c0b7274efa2e719e13fb1f3/src/WednesdayClubPost.sol#L93

https://github.com/aelchim/WednesdayClub/blob/ab55e6f6fba0d5ce1c0b7274efa2e719e13fb1f3/src/WednesdayClubUser.sol#L55


## 3.7. Possibility of liking posts more than one time

### Severity: low

### Description

Anyone can like post few times using function [`likePost`](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L69)
And also anyone can [`report`](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L84) post many times from one account.
Similar in comments section.

## 3.8. Following many times

### Severity: low

### Description

Any address can follow any address many times using function [`follow`](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L255) because there is no checking that `msg.sender` follows `address`.

### Recommendation

Add checking that `msg.sender` is not follow current address.

## 3.9 Wrong comments

### Severity: minor observation

### Description

There is wrong comments in [line 291](https://github.com/aelchim/WednesdayClub/blob/master/src/WednesdayClub.sol#L291).

### Recommendation

Update comments for block function.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/92f44700ea801a666872c9d9a58ee61c

https://gist.github.com/yuriy77k/7a6aadc9cb29f20bad6aeff34c814140

https://gist.github.com/yuriy77k/da90f936c0c3aa4e5eb50f90ac42f8b8
