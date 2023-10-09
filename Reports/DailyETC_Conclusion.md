# 1. Summary

[DailyETC](https://etherhub.io/addr/0x2f9d693c1795970963e7ff2e3fa86386b9a8a342#tab_addr_3) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [DailyETC.sol](https://gist.github.com/yuriy77k/1034d7c95513cac791edf6419ae643a6)

# 3. Findings

In total, **2 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

No critical security issues were found.

## 3.1. Ponzi Schema

### Severity: medium

### Description

The audited game contract is a ponzi scheme, new players will send the ether that will pay the profits and affiliate commissions of other users, however due to the way that profit if calculated `SafeMath.div(SafeMath.mul(secondsPassed, investedETH[customer]), 2419200)` where a user will able to withdraw a total of his played amount every `2419200` seconds, with an unlimited time.

In order for a user to be paid his profit should be always less or equal to the contract balance, for example if two players invest 2 ethers each and wait less than Approximately 2 months without withdrawing, the profits will be higher than the contract balance, which will add more risk than just the ponzi scheme used by the contract.

A regular Ponzi scheme mostly hurts new investors, however the proposed game can even hurt the old investors following how long they waited to withdraw their profit.

### Code snippet

https://gist.github.com/yuriy77k/1034d7c95513cac791edf6419ae643a6#file-dailyetc-sol-L44#L46

## 3.2. Intended Statement Error

### Severity: low

### Description

`investETH` function takes `referral` address as parameter, one if statement check if `referral != 0x1` when it should be `referral != 0x0` to avoid any player to assign the referral commission to address 0x0. 

Analyzing the contract logic, it is more favorable for the developers to allow user input error and assign the commission to 0x0 address than avoid it, since it will add the ether to the contract balance without allowing any affiliate to withdraw it, and finally be used to pay players profits or other affiliate commissions.
 
### Code snippet

https://gist.github.com/yuriy77k/1034d7c95513cac791edf6419ae643a6#file-dailyetc-sol-L25


# 4. Conclusion

Investing into financial schemes can represent a significant high risk when a ponzi scheme is identified, and adding the implemented logic of the contract make it even more risky for investors.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/fdbc665e23f1e7725813fff3403b48a8

https://gist.github.com/yuriy77k/f3d951eff31fd5fa2598a697ced40033

https://gist.github.com/yuriy77k/12eadc2dae8a003e3753565f3ef2475e
