# 1. Summary

[TGE ICO](https://github.com/Auditchain/tge) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ico.sol](https://github.com/Auditchain/tge/blob/master/ico.sol) github commit hash 1aa85cde7a357d06dc93e0858a62f8dd6ca4a919.

# 3. Findings

In total, **8 issues** were reported including:

- 3 medium severity issues.

- 4 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Refund Issues

### Severity: medium

### Description

If the ICO state is set to `Step.Refunding`, investors will still be able to buy tokens since `contribute` function does not require `currentStep` to be different than `Step.Refunding`, multiple issue can be raised:

- `minCap` can be reached, and the investors won't be able to use refund even if the state is set to `Step.Refunding`.
- The newly invested amount of ether after that the current state is changed to `Step.Refunding` will not be refunded except if the owner call `drain` extract the amount sent to the contract, add the newly invested ether plus any ether refunded to investors from the owner private assets and send it again using `prepareRefund`, which can be impossible if a high value of ether was already refunded.

### Code snippet

https://github.com/RideSolo/tge/blob/master/ico.sol#L232#236

https://github.com/RideSolo/tge/blob/master/ico.sol#L305#L329

## 3.2. Owner Interactions Issues

### Severity: medium

### Description

- The ICO start and end time is set using two functions `start` and `adjustDuration`, the developers can change the `startBlock` and `endblock` at will, the only provided explanation is since they use block numbers and the block time is not stable, they need this kind of administration over the contract, however block timestamp could have been used.

- The ICO contract do not automatically refund the investor in case of failure, but it needs the contract owner interaction using `prepareRefund` function since the total ether invested has to be sent back to the contract before refund of the investors.

- ICO phases do not follow a calender, to change from pre-sale to normal public sale `advanceStep` function has to be called by the owner, this  can impact the investors if they expect a pre-sale token bonus and the contract state is changed to public sale by the owner.

- When an investor call `refund` the crowdsale address is allowed to burn tokens using `burn` function member of `Token` as set in the `onlyAuthorized` modifier, however, even `Token` contract owner is allowed to burn tokens even with the crowdsale success.

- `Token` contract lock mechanism was intended for the crowdsale, however, `Token` contract owner is also allowed to lock or unlock the transfer of tokens after the ICO.

### Code snippet

https://github.com/RideSolo/tge/blob/master/ico.sol#L206#L210

https://github.com/RideSolo/tge/blob/master/ico.sol#L214#L219

https://github.com/RideSolo/tge/blob/master/ico.sol#L232#236

https://github.com/RideSolo/tge/blob/master/ico.sol#L224#L228

https://github.com/RideSolo/tge/blob/master/ico.sol#L428#L433

https://github.com/RideSolo/tge/blob/master/ico.sol#L393#L397

https://github.com/RideSolo/tge/blob/master/ico.sol#L415#L422

## 3.3. ICO State

### Severity: low

### Description

`prepareRefund` function member of `Crowdsale` contract should require that the total ether invest is lower than `minCap` before changing the state of the ICO.

### Code snippet

https://github.com/RideSolo/tge/blob/master/ico.sol#L232#236

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/tge/blob/master/ico.sol#L368#L514

## 3.5. Calculation mismatch.

### Severity: low

### Description

The 'maxCap' variable operates with decimals , so in this context the '- 1000' will not subtract 1000 tokens , rather the 1/1e15 of token.

### Code snippet  

https://github.com/Auditchain/tge/blob/master/ico.sol#L247

## 3.6. Token Transfer to Address 0x0.

### Severity: low

### Description

In functions [`transfer`](https://github.com/Auditchain/tge/blob/master/ico.sol#L439) and [`transferFrom`](https://github.com/Auditchain/tge/blob/master/ico.sol#L451) there are no zero address checking.

### Recommendation

Add zero address checking.
```solidity
require(to != address(0));
```

## 3.7. TODO comments left. 

### Severity: minor observation

### Description

In [`construction`](https://github.com/Auditchain/tge/blob/master/ico.sol#L182) there is `TODO` comments left. Please do not forget to change it.

## 3.8. Wrong token distribution. 

### Severity: medium

### Description

All the left tokens will be sent to team address, but it's a wrong way of distribution, team should get fixed amount and all the left tokens should be burned or there should be another sale.
Functions [`finalize`](https://github.com/Auditchain/tge/blob/master/ico.sol#L241) and [`tokenDrain`](https://github.com/Auditchain/tge/blob/master/ico.sol#L262).

# 4. Conclusion

There were found two medium severity issues and low severity issues, please fix it before deploying.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/dd740fef6e04c9c44f998c1945582a67

https://gist.github.com/yuriy77k/d4dbc1ce7dc82752d50a163ca7db65f8

https://gist.github.com/yuriy77k/c8c5a6036c06740d5f0b0c72e6a3d56c
