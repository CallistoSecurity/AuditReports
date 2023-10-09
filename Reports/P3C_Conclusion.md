# 1. Summary

[P3C.io Auto-Reinvestment Contracts](https://github.com/p3c-bot/p3c-bot.github.io/blob/upgrade/contracts/v1.0.0/) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Crop.sol](https://github.com/p3c-bot/p3c-bot.github.io/blob/upgrade/contracts/v1.0.0/Crop.sol) github commit hash 6d8e620544a3a65ee6f642718a8e5d8fcc538a69.
- [Farm.sol](https://github.com/p3c-bot/p3c-bot.github.io/blob/upgrade/contracts/v1.0.0/Farm.sol) github commit hash 6d8e620544a3a65ee6f642718a8e5d8fcc538a69.

# 3. Findings

In total, **1 issues** were reported including:

- 1 high severity issues.

## 3.1. Anyone can make a link to a crop contract.

### Severity: high

### Code snippet

* [Crop.sol](https://github.com/p3c-bot/p3c-bot.github.io/blob/6d8e620544a3a65ee6f642718a8e5d8fcc538a69/contracts/v1.0.0/Crop.sol#L68)

* [Farm.sol](https://github.com/p3c-bot/p3c-bot.github.io/blob/6d8e620544a3a65ee6f642718a8e5d8fcc538a69/contracts/v1.0.0/Farm.sol#L53)

### Description

`link` method in Farm contract and `linkCrop` in Crop contract is public and anyone can set any contract as crop-contract for any owner. There can be many vectors to attack.

1. Owner of "crop contract" can deploy any contract as "crop contract". Malicious "crop contract" may have `reinvest` method but included anything that an attacker will wish. For example some over regular operations with another contracts which the attacker will perform at the expense of the farmer. And the farmer won't suspect the problem because he will get his 3%.

2. It is possible to use the previous point issue (#1) to attack another contracts. For example for incrementing uint X values for overflowing (it is usually expensive but can be realized at the expense of the farmer).

3. Possible to set as `owner` the address that not really the owner of the crop contract. For example as `owner` can be set the user with good reputation or rating, but real owner of the contract will be another user which will make a profit.

And other attacks are possible using this vulnerability.

#### Recommendation

The right way is to deploy the crop contract from special method of the farm contract. In this case we will certainly know the owner of the crop contract and will be sure that the contract code is not changed.

# 4. Conclusion

The smart contract has high severity issue and can't be deployed. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/a517fe2a47cbec11d77c3491c8c96e83

https://gist.github.com/yuriy77k/046f28b9b6029c85f74dcecab8397ac5

https://gist.github.com/yuriy77k/6a54b3bd67040a524a3296a61af26df1
