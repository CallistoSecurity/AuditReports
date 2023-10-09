# WeiDex v2 Security Audit Report

# 1. Summary

[WeiDex v2](https://github.com/weichain/weidex-eth-v2) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Exchange.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/Exchange.sol) github commit hash db7124b565567e248dd78e2f14ed309128276a22.
- [ExchangeBatchTrade.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/ExchangeBatchTrade.sol) github commit hash 13fdd9261cdd78da524884bc104acafbb5551ef2.
- [ExchangeMovements.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/ExchangeMovements.sol) github commit hash 849237c8c44d1ffa97f3c40e808b731791649343.
- [ExchangeOffering.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/ExchangeOffering.sol) github commit hash ed7a514bf9c62aa9bb16e59ec10b3477d0ca2b7d.
- [ExchangeStorage.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/ExchangeStorage.sol) github commit hash 13fdd9261cdd78da524884bc104acafbb5551ef2.
- [ExchangeUpgradability.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/ExchangeUpgradability.sol) github commit hash 13fdd9261cdd78da524884bc104acafbb5551ef2.
- [WeiDex.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/WeiDex.sol) github commit hash 4c835122474f34344405dcf6dd12a897f7697b27.
- [OldERC20.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/mocks/OldERC20.sol) github commit hash 13fdd9261cdd78da524884bc104acafbb5551ef2.
- [SimpleOldToken.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/mocks/SimpleOldToken.sol) github commit hash 13fdd9261cdd78da524884bc104acafbb5551ef2.
- [SimpleToken.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/mocks/SimpleToken.sol) github commit hash e0a5e7794ca31d8f021a392f6bd4ba345d186506.
- [LibCrowdsale.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/utils/LibCrowdsale.sol) github commit hash ed7a514bf9c62aa9bb16e59ec10b3477d0ca2b7d.
- [LibMath.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/utils/LibMath.sol) github commit hash 13fdd9261cdd78da524884bc104acafbb5551ef2.
- [LibOrder.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/utils/LibOrder.sol) github commit hash e0a5e7794ca31d8f021a392f6bd4ba345d186506.
- [LibSignatureValidator.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/utils/LibSignatureValidator.sol) github commit hash 19f4791d4423a8a8ce10d546278a7dc6048e660b.
- [IExchangeUpgradability.sol](https://github.com/weichain/weidex-eth-v2/blob/master/contracts/exchange/interfaces/IExchangeUpgradability.sol) github commit hash 19f4791d4423a8a8ce10d546278a7dc6048e660b.

# 3. Findings

In total, **7 issues** were reported including:

- 4 low severity issues.

- 2 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Referral address require checking for zero

### Severity: low

### Description

Referrals addresses are set in `deposit` function member of `ExchangeMovements` contract, if the users do not input a referral address and leave it empty, the referral reward will be assigned to `address(0)` in `executeTrade` function member of `Exchange` contract.

The impact will be locking an amount of different tokens to address 0x0 without possibility of withdrawal, the amount can vary following the traded volume and the number of users without referral addresses.

### Code snippet

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeMovements.sol#L69#L72

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/Exchange.sol#L242

### Recommendation

Check `referrer` address in `executeTrade` where `referrer` should be different than `address(0)` and allocate the referral reward following the result.

## 3.2. Exchange Upgrade

### Severity: low

### Description

`importEthers/importTokens` function member of `ExchangeUpgradability` do not set the referral address for a user when importing the user fund from an old exchange address. this issue will cause the same problem described in " Referral Reward" issue.

### Code snippet

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeUpgradability.sol#L116

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeUpgradability.sol#L146

## 3.3. Exchange Balance Transfer

### Severity: low

### Description

In `transfer` function member of `ExchangeMovements` contract some requirement should be set to avoid sending balances to wrong addresses.

### Code snippet

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeMovements.sol#L119

### Recommendation

Add the following lines to the function:

```
	require(to!=address(0));
	require(to!=address(this));
```
## 3.4. Exchange Balance Transfer

### Severity: low

### Description

`migrateFunds` function member of `ExchangeUpgradability` does not check if the new exchange address is set to non null address. 

### Code snippet

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeUpgradability.sol#L58

## 3.5. ERC-20 Issues

### Severity: notes

### Description

The following issues are part of mock files that are probably used for test only:

- `transfer/transferFrom` do not require the destination address to avoid zero address transfer or any other erroneous address .
- It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
- Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/mocks/OldERC20.sol

## 3.6. Experimental Features

### Severity: notes

### Description

As raised by the compiler "Experimental features are turned on. Do not use experimental features on live deployments" the audited code uses `ABIEncoderV2` that is in experimental phase and should not be deployed in a live network.

### Code snippet

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/Exchange.sol#L2

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeBatchTrade.sol#L2

https://github.com/RideSolo/weidex-eth-v2/blob/master/contracts/exchange/ExchangeOffering.sol#L2

## 3.7. Owner privileges

### Severity: owner privileges

### Description

Owner can migrate exchange to new contract address. It may has issues if was not audited.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/95510c49110e25766c1d75bd99e8d307

https://gist.github.com/yuriy77k/0523a14212bf02bdd9a6c7a047eaa002

https://gist.github.com/yuriy77k/e29ec5b85f2dd1380c39bbdcec1cee9f
