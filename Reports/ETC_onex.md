# 1. Summary

[ONEX](https://github.com/HighlanderNetwork/ONEX-Network/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

ERC223 implementation, Proof-of-Stake Token on Ethereum Classic Network. This is an alternative method to the more widely known process used by bitcoin; Proof of Work (POW).

# 2. In scope

- [ONEX.sol](https://github.com/HighlanderNetwork/ONEX-Network/blob/master/contracts/ONEX.sol) github commit hash 63b02beee3da39cea86c84703047b5d757e2f756.

# 3. Findings

In total, **4 issues** were reported including:

- 3 medium severity issues.

- 1 low severity issues.

## 3.1. Block Gas Limit Error

### Severity: medium

### Description

When executing `mint` function, `getCoinAge` function get called to return the `coin*day`holding of a user, transaction execution can throw for block gas limit reached.
If a user receives many transaction, after a certain number the block gas limit can be reached when calling `getCoinAge`. therefore the users will not be able to receive his reward.
The for loop uses iterate over transferIns which is causing this issue.

### Code snippet

https://github.com/RideSolo/ONEX-Network/blob/master/contracts/ONEX.sol#L241#L252

## 3.2. Staker Reward

### Severity: medium

### Description

If a token transfer occurs and a staker didn't claim his reward by calling transfer function using `to` address equal to his own, the deposit history `transferIns` will be deleted.

Following onex description "ONEX provides two methods to trigger ONEX staking: 1.Sending a transaction to your own address with any amount of ONEX. 2.Using MyEtherWallet.com or Mist or any other software that can interact with contracts to execute mint() function.", contract developers should inform the stakers that if they transfer tokens after the minimum staking period without claiming their stake will result in the loss of their reward.

Implementing a simple mechanism that allows the addition of the reward to the stakers account if they meet the requirement when transfering tokens to another address can be implemented easily.

### Code snippet

https://github.com/RideSolo/ONEX-Network/blob/master/contracts/ONEX.sol#L150

https://github.com/RideSolo/ONEX-Network/blob/master/contracts/ONEX.sol#L169

### 3.3. Token Transfer to Address 0x0

### Severity: low

### Description

Onex Token do not require the `to` address to be non null before transfer. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://github.com/RideSolo/ONEX-Network/blob/master/contracts/ONEX.sol#L144

## 3.4. Wrong `interest` calculation

### Severity: medium

### Description

In function [`getProofOfStakeReward`](https://github.com/HighlanderNetwork/ONEX-Network/blob/5a6dcd53c295a61a3307d2195d57d25ca8376f17/contracts/ONEX.sol#L217) there is wrong interest calculation for first two years, because `maxMintProofOfStake` is default 10% annual interest as written in [line 80](https://github.com/HighlanderNetwork/ONEX-Network/blob/master/contracts/ONEX.sol#L80) and `interest` for first year should be 100% that means 10 times more than `maxMintProofOfStake`, but it calculates as `interest = (770 * maxMintProofOfStake).div(100);` that means it's less than 100%. Similar situation with calculations for second year.

# 4. Conclusion

The smart contract has medium severity issues, which should be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/39dd2472966f8960ab8701ca0a397844

https://gist.github.com/yuriy77k/76feec3f37615b7bdd815cbc44c31450

https://gist.github.com/yuriy77k/98ce881c46f38cf9a34bf6e9236eb14c
