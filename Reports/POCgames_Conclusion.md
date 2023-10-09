# 1. Summary

[POCgames](http://etherhub.io/addr/0x489633589a2e9285197cabea82ba6a8af99acdd7#tab_addr_3) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://gist.github.com/yuriy77k/909d9541f30628d3a0e6c39af36a4cdf

the same as
http://etherhub.io/addr/0x489633589a2e9285197cabea82ba6a8af99acdd7#tab_addr_3

# 3. Findings

**3 issues** were reported including:

- 1 high severity issue.

- 1 medium severity issue.

- 1 low severity issue.

## 3.1. Result Pre-Calculation

### Severity: High

### Description

`wager` function parameters `ticketID` and `ticketLastBlock` form a message that is signed externally using `web3.eth.sign` the signature is made of 3 variables `v`, `r`, `s` that are used as input parameters also to recover the `secretSigner` address and confirm that the `ticketID` was provided by the `secretSigner` address private key owner.

However, `secretSigner` state variable [is assigned the value of](https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L136) `ecrecover(signatureHash, v, r, s)` output, then a requirement is set with:
```
 require (secretSigner == ecrecover(signatureHash, v, r, s), "web3 vrs signature is not valid.");
```
[The condition](https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L137) will always be true since `secretSigner` was assigned with the value of `ecrecover(signatureHash, v, r, s)`, any user can call wager and put a bet.

If an attacker calculate `ticketID` using `uint(keccak256(abi.encodePacked(ticketReveal)))` with a value of `ticketReveal` of his choice, the attacker can call `wager` place a bet using the calculated `ticketID`. (using the setup `ticketReveal` will allow him to [call play function](https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L160) and retrieve his ticketID)

`play` function result can be pre-calculated before calling it using the setup `ticketReveal` value, if the result allow the attacker to win, the attacker will call `play` otherwise he will wait 250 blocks and call `refund`.

The bot set up by the project team will not be able to call `play` to run the bet of the attacker since the `ticketReveal` will be unknown to it.

### Code snippet

https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L136

https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L137

https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L160

https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L156#L201

## 3.2. Donation Withdrawal

### Severity: medium

### Description

If a donator withdraw an amount higher than `address(this).balance - lockedInBets - jackpotSize - devFeeSize` using `withdrawDonation`, `checkContractHealth` modifier will not allow multiple functions to execute, the contract will freeze most actions until a new donation is done or ether is sent to contract through the fallback function.

donator should not be allowed to withdraw an amount higher than `address(this).balance - lockedInBets - jackpotSize - devFeeSize`.

The consequences can vary widely for both the project team and users.

### Code snippet

https://gist.github.com/RideSolo/d4b8cc709953428ad5b2bda8767f83b4#file-fiftyflip-sol-L211#L219

## 3.3. No checking for zero address.

### Severity: low

### Description

Functions member of  FiftyFlip contract do not require the to address to be non null before transfer. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://gist.github.com/yuriy77k/909d9541f30628d3a0e6c39af36a4cdf#file-fiftyflip-sol-L74

https://gist.github.com/yuriy77k/909d9541f30628d3a0e6c39af36a4cdf#file-fiftyflip-sol-L107

https://gist.github.com/yuriy77k/909d9541f30628d3a0e6c39af36a4cdf#file-fiftyflip-sol-L114



# 4. Conclusion

The contract is not safe, a direct exploit has been highlighted.


# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f2379724799ab771db12b3f0a68f9caa

https://gist.github.com/yuriy77k/9ddab2726b99c22db3716fbfab1903f6

https://gist.github.com/yuriy77k/0ba71a138f64df16fa7a0eaa3980c31f
