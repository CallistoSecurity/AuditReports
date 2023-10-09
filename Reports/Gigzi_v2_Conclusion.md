# [Gigzi smart contract improved (ETH)](https://github.com/Gigzi/contracts/tree/master/contracts) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The contracts reviewed can be considered safe to be deployed. However a lot of optimization has to be done to minimize gas consumption.

## 2. Low severity issues:

## 2.1. Reserved Account of Fee Collector Address not updated

### Description 

The function `setTxFeeCollector` member of `FeeableToken` re-assign fee collector address, but do not reset the reserved account old address to avoid fee collection, however `addReservedAccount` can be used but at the price of extending `accountsReserved` dynamic array.

### Code snippet

https://github.com/GigziProject/GigziContracts/blob/ffe57bdaf1b0d1fb29d182cd03c1890b3be50ac8/contracts/FeeableToken.sol#L65#L68


## 2.2. Known Issues of ERC20 Standard.

#### Description

ERC20 Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

* Approve + transferFrom mechanism allows double Withdrawal attack (as commented on the contract).

* Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.


## 2.3. `processTransfer` always returns `false`

### [Code snippet](https://github.com/Gigzi/contracts/blob/0989f4a550ef8ae7110635784a96144d5578d7d1/contracts/GigBlack.sol#L204)

```solidity
function processTransfer(address _from, address _to, uint256 _value) internal returns (bool) {

    // update rewards before transfer
    updateAccountReward          (_from);
    updateAccountReward          (_to);

    FeeableToken.processTransfer (_from, _to, _value);
}
```

### Description

`processTransfer` function of GigBlack contract calls override and call parent function of FeedableToken contract. Parent function returns `true`, but GigBlack `processTransfer` doesn't have a `return` keyword and by default always return `false`. This can distort the logic of the application.

### Recommendation

Use `return FeeableToken.processTransfer (_from, _to, _value);` for right result.


# 3. Minor observations.

## 3.1. Reserved Account Optimization

### Description

Reseverd accounts are used to avoid paying fees for some addresses. Each time if the contract owner wants to add an address he calls `addReservedAccount` adding a new element to `accountsReserved` dynamic array, this practice adds too much gas consumption when checking if the address transaction fees are applicable (in `isFeeShouldBePaid` function).

### Code Snippet

https://github.com/Gigzi/contracts/blob/0989f4a550ef8ae7110635784a96144d5578d7d1/contracts/FeeableToken.sol#L60#L63

https://github.com/Gigzi/contracts/blob/0989f4a550ef8ae7110635784a96144d5578d7d1/contracts/FeeableToken.sol#L107#L125

### Recommendation

Use a simple mapping where an address will map to a boolean variable. this will considerably optimize transactions gas consumptions.
The functions `addReservedAccount` and `isFeeShouldBePaid` should be reimplemented as recommended.



# Revealing audit reports:

https://gist.github.com/yuriy77k/65cc60dd03e4d8353e3419744876c10d

https://gist.github.com/yuriy77k/22a7cc05f8d221e7109048efe1d37073

https://gist.github.com/yuriy77k/ed53bb4bc8cf32f42b6c2d9c42512f9f

