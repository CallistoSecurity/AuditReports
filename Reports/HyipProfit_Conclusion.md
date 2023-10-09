# 1. Summary

[HyipProfit](https://etherscan.io/address/0xdf1f0c982e6574c4b89ec49e2636abd9a25a3126#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [HyipProfit.sol](https://gist.github.com/yuriy77k/96fb54f0d8da5aa7ffa90e2b84c0b416)

# 3. Findings

In total, **5 issues** were reported including:

- 1 high severity issues.

- 3 medium severity issues.

- 1 low severity issues.

## 3.1.Dividends Topup Logic

### Severity: medium

### Description

When the ico is finalized the same fallback function used to buy tokens is used by the team to topzup the dividends, if an investors send ether after the ico end if the amount is higher than 1 eth than it will be distributed to tokens holders.
Contract developers should use a different function or implement

### Code snippet

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L195

## 3.2. Users Withdrawal

### Severity: medium

### Description

In stage 2 people who participated to the presale are allowed to withdraw up to 40 to their initial investment, since the team will vest on each stage a part of it.
in stage to as implemented on `getAvailableFundsForTheTeam` the team will get a total of 30% from stage one then half of the rest of the presale fund 35%, in total 65%, however as explained before the users are allow to withdraw up to 40% (check function `getPreSaleWeiToReturn`).

The same issue is also applicable for stage 5 with the `icoFund`.

### Code snippet

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L337

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L308

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L315

## 3.3. Truncated Buy Token Value

### Severity: high

### Description

Any investor that send an amount of ether higher than `N x currentPrice` will have the token amount that he bought truncated, since `msg.value` is directly divided by the `currentPrice`.

### Code snippet

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L173

### Recommendation

- Write the conversion as a fraction that equals 1.
- Multiply the value to be converted using the previous fraction.
- Solidity always truncate the value of X when divided by Y, developers should first multiply than divided to get the non-truncated value.

## 3.4. Contract Logic

### Severity: medium

### Description

As per the audit request description the ICO has 4 stages, after the 4th stage (0,1,2,3) the token price is set to zero (check `getCurrentSellPrice`):

- The fallback function used to buy tokens will throws since a division per zero will occur (user a require to avoid such practice).
- After the 4th stage (`currentStage == 4`) the `checkIfMissionCompleted` function check an external address balance to submit next ico stages that will allow the withdrawal of the ether by the team or by the investors, developers should explain the logic used for better understanding and possible avoidance of any possible issues.

### Code snippet

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L315

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L173

https://gist.github.com/RideSolo/9fc994080586be2d67e6ae34b8790b06#file-hyipprofit-sol-L358#L360

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4. Conclusion

The highlighted issues should be fixed before deployment.


# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ad17cafb5e9536bf55294d2c1ffacf96

https://gist.github.com/yuriy77k/c38289fc9a4a056b3c19700039650263

https://gist.github.com/yuriy77k/0a0f38160e1f4dda90ff5e20924ea78c
