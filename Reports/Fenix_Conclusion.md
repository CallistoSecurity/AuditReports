# [Fenix Project](https://github.com/fenixcash/fenix-smart-contract) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited contracts do not contain any direct exploit, however the users should be informed about issue 2.1.


# 2. Medium severity issues:

## 2.1. Owner Control over Token Holders

### Description

The Token contract owner has the right to transfer tokens from holders accounts without permission from them using `spendFrom` and `payFrom` functions members of `FenixCashToken` contract.
Users should be aware of the risks that this logic represent to their holdings. Private key hacking is an issue when it is for only one user, but when a hack can reach the total number of the token holders it cannot be accepted.

### Code snippet

https://github.com/RideSolo/fenix-smart-contract/blob/master/contracts/FenixCashToken.sol#L67#L69

https://github.com/RideSolo/fenix-smart-contract/blob/master/contracts/FenixCashToken.sol#L106#L109


# 3. Low severity issues:

## 3.1. Known Issues of ERC20 (QRC20) Standard

### Description

FENIX token is ERC20 compatible (QRC20), ERC20 (QRC20) Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

- Approve + transferFrom mechanism allows double Withdrawal attack.
- Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.

# 4. Minor observation:

## 4.1. Fallback Function

### Description

A contract without a fallback function will not accept ether.

### Code snippet

https://github.com/RideSolo/fenix-smart-contract/blob/master/contracts/FenixCashToken.sol#L28#L33


## 4.2. No checking for zero address. 

### Description

At [`constructor`](https://github.com/fenixcash/fenix-smart-contract/blob/41934fa453eefa4e380f9a847b0b9d9ba952299e/contracts/FenixCashTokenPreSale.sol#L49
) and [`setWallet`](https://github.com/fenixcash/fenix-smart-contract/blob/41934fa453eefa4e380f9a847b0b9d9ba952299e/contracts/FenixCashTokenPreSale.sol#L58) function there are no checking if wallet is not zero address.

And same issues at [Token Sale contract](https://github.com/fenixcash/fenix-smart-contract/blob/master/contracts/FenixCashTokenSale.sol): 
[`constructor`](https://github.com/fenixcash/fenix-smart-contract/blob/41934fa453eefa4e380f9a847b0b9d9ba952299e/contracts/FenixCashTokenSale.sol#L55)
 and [`setWallet`](https://github.com/fenixcash/fenix-smart-contract/blob/41934fa453eefa4e380f9a847b0b9d9ba952299e/contracts/FenixCashTokenSale.sol#L64) function.

### Recommendation

Add checking for zero address.

```solidity
require(_wallet != address(0));
```

## 4.3.The commission funds remains at the previous admin disposal after the 'setAdmin(address newAdmin)' function call.

### Code snippet  

https://github.com/fenixcash/fenix-smart-contract/blob/master/contracts/FenixCashToken.sol#L124

https://github.com/fenixcash/fenix-smart-contract/blob/master/contracts/lib/Admin.sol#L38


# Revealing audit reports:

https://gist.github.com/yuriy77k/8593f171df7e2e2f77510c6658bfe20f

https://gist.github.com/yuriy77k/b6e76adda87c91d48999a6ef32a1c8a8

https://gist.github.com/yuriy77k/daa70721f4215519e5562a86ce231322

