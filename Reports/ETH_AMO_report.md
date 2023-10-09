# AMO Project Security Audit Report

# 1. Summary

[AMO Project](https://github.com/AMO-Project/AMO-Contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [AMOCoin.sol](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol) github commit hash 51b441202ba09186d2aec530a88f9e898205027c.
- [AMOCoinSale.sol](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol) github commit hash 390f49f16827d272ce19ddd8a90757c45a974ff0.

# 3. Findings

In total, **7 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 2 notes.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Multiple Token Transfers
 
### Severity: medium

### Description

When `allocateTokens` is called the `tokenAmount` to be transferred to `to` address is not subtracted from `allocationList[to].allowedAmount`, meaning that if the function is called again by the owner a higher amount than `allocationList[to].allowedAmount` can be transferred to the `to`.

### Code snippet

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol#L463#L476

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol#L400#L407

## 3.2. Multiple Token Allocation
 
### Severity: notes

### Description

If Multiple allocations are made to `user` address using `addToAllocationList` function member of `AMOCoinSale`, the `amount` allocated is not cumulated in `allocationList[user].allowedAmount`.
Please note that `addToAllocationList` is meant to be used when `EarlyInvestment` is set, meaning that real payment are done offchain and this can lead to major errors from the team since this function is only used by the owner.

### Code snippet

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol#L400#L407

## 3.3. Owner Privileges
 
### Severity: Owner privilege

### Description

1. AMO token owner privileges:

- Enable/Disable transfers/burn/approval for all tokens holders (`onlyWhenTransferAllowed`,`enableTransfer`,`disableTransfer`) (check [here](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol#L49)).
- Selectively freeze/unfreeze tokens by addresses (`onlyAllowedAmount`,`lockAccount`,`unlockAccount`) (check [here](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol#L76)).

2. AMO sale owner privileges:

- Token destinated to be sold through the crowdsale contract are approved and not transferred and locked in the sale contract, meaning that the owner will still be able to handle them without backing them up by ether (check [here](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol#L376)).
- Every round (EarlyInvestment, PreSale,CrowdSale) has three stages (SetUp,Started,Ended), the sale contract logic do not allow to go to a previous stage of a round but a round can be selected once the the previous round has ended using `setUpSale`, meaning that the owner can reset the round even to a previous one. owner should not be able to reselect round in `setUpSale` but `round` has to be incremented directly inside the function, (check [here](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol#L318#L336)). 
- A sale round can be ended by the owner at any moment, (check [here](https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoinSale.sol#L357).
- `addToAllocationList` function member of `AMOCoinSale` add tokens amount to mapping for a dedicated users since it is allowed only when `EarlyInvestment` round then we can guess that the token sale was done offchain through fiat or any other way. However `removeFromAllocationList` can also cancel the allowed tokens before that `allocateTokens` is called and the tokens are transferred to the user address. The main issue here is when is the fiat or crypto payment is done and why an address can be removed knowing that a user has maybe didn't get his payment back.
- Owner can change minContribution, maxContribution, rate and hardCap for each sale round before starting sale.

## 3.4. Zero address

### Severity: notes

### Description

There is possibility of setting zero address as admin in function `AMOCoin` and as contract address in function `setTokenSaleAmount`.

### Code snippet

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol#L85

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol#L101

## 3.5. Modifier will block correct working

#### Severity: low

#### Description

The modifier `onlyValidDestination` from `AMOCoin` contract will fail the function in case of transferring funds to sale address. For example in function `setTokenSaleAmount` there are approving funds for `tokenSaleAddr`. And after it should be transferred from to this address, but `transferFrom` function checks with using `onlyValidDestination(to)` modifier.

### Code snippet

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol#L67

https://github.com/AMO-Project/AMO-Contracts/blob/master/contracts/AMOCoin.sol#L101

## 3.6. Known vulnerability of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/2fc552fd10cb2f77139068f29cfdedb5

https://gist.github.com/yuriy77k/618cb51beedeebb119dd37991a77238f

https://gist.github.com/yuriy77k/23b820cc0dbcced4d173dcbd105b9464
