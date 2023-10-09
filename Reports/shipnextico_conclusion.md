# 1. Summary

[ShipNext ICO]() security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [IBonus.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/IBonus.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [ICurrency.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ICurrency.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [IStorage.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/IStorage.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [ShipCoin.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ShipCoin.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [ShipCoinBonusSystem.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ShipCoinBonusSystem.sol) github commit hash a11e140b5f9a056641273c01dd4abb1d302b1076.
- [ShipCoinCrowdsale.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ShipCoinCrowdsale.sol) github commit hash 5d481e14078c74182b78c07df0ca787ef1a29198.
- [ShipCoinCurrency.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ShipCoinCurrency.sol) github commit hash a11e140b5f9a056641273c01dd4abb1d302b1076.
- [ShipCoinStorage.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ShipCoinStorage.sol) github commit hash e39eaf9ebd6077d6843a5bb743b795839a8e135d.
- [SafeMath.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/lib/SafeMath.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [MultiOwnable.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/base/MultiOwnable.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [Ownable.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/base/Ownable.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [String.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/base/String.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [BasicToken.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ERC20/BasicToken.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [BurnableToken.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ERC20/BurnableToken.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [ERC20.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ERC20/ERC20.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [ERC20Basic.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ERC20/ERC20Basic.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [MintableToken.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ERC20/MintableToken.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.
- [StandardToken.sol](https://github.com/emil-dudnyk/shipnextico/blob/master/contracts/ERC20/StandardToken.sol) github commit hash ffd90c0b40eae76687c5e88fd587910b39561d5c.

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

No critical security issues were found.

## 3.1. State Variables Alteration by Contract Owners

### Severity: medium

### Description

A high majority of the state variables used on the audited contracts can be reset by the team at anytime, those alterations can completely modify the contract logic and introduce errors,
The usage of ethereum blockchain for fund raising purposes through ICOs should be purely automated to profit of such technologies.

* The owner can withdraw ETH funds even if the softcap is not reached. This is possible because the owner can manually add any amount of any currency as "raised" funds. He can do this with functions `addPay` or `editPay`.

* The owner can get back all tokens from Crowdsale contract even if contributors have not received their tokens. There is `getSHPCBack` function in the Crowdsale Contract and the owner can get all tokens after the sale ends but even if the contributors have not yet received their tokens.

* Refund status can be set only manually. In case if soft cap not reached the contributors can receive their funds, but only if state is `SaleState.REFUND`. And it is changed manually by the owner. In this case contributors couldn't be sure that they get their funds back. Status should be changed automatically.

* Owners can leave bonus period faster, than need by starting main sale in function `startSale`. 

### Code snippet

https://github.com/emil-dudnyk/shipnextico/blob/e39eaf9ebd6077d6843a5bb743b795839a8e135d/contracts/ShipCoinCrowdsale.sol#L458

https://github.com/emil-dudnyk/shipnextico/blob/e39eaf9ebd6077d6843a5bb743b795839a8e135d/contracts/ShipCoinCrowdsale.sol#L642

https://github.com/emil-dudnyk/shipnextico/blob/e39eaf9ebd6077d6843a5bb743b795839a8e135d/contracts/ShipCoinCrowdsale.sol#L331

https://github.com/emil-dudnyk/shipnextico/blob/e39eaf9ebd6077d6843a5bb743b795839a8e135d/contracts/ShipCoinCrowdsale.sol#L303


## 3.2. Truncated Value

### Severity: low

### Description

`subPercent` function member of `ShipCoinCurrency` contract deduct the `b` % from `a`, how ever when computing the value of `b` % of `a` the value is truncated since it is first divided by `100`.

Following the usage of the function the consequences can vary. 

### Code snippet

https://github.com/emil-dudnyk/shipnextico/blob/a11e140b5f9a056641273c01dd4abb1d302b1076/contracts/ShipCoinCurrency.sol#L447

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.4. Complicated system of rights.

### Severity: low

### Description

Consider a separate case. In the `buyTokens` function of ShipCoinCrowdsale Contract is called several functions and each of them requires the user to belong to a special group of rights.

|Function|Group required
|--|--
|`storageContract.addPayment`|`onlyMultiOwnersType(9)`
|`currencyContract.addPay`|`onlyMultiOwnersType(2)`

Thus, to call a `buyTokens` function from the ShipCoinCrowdsale contract the contract must be added to the permissions group "2" of ShipCoinCurrency contract and to group "9" of ShipCoinStorage contract. Adding this contract to the desired group is not described in this code. It is looks complicated and may lead to mistakes and it doesn't matter if permissions added manually or automatically.

Also in this contract other functions requiring other rights are called.

### Code snippet

https://github.com/emil-dudnyk/shipnextico/blob/e39eaf9ebd6077d6843a5bb743b795839a8e135d/contracts/ShipCoinCrowdsale.sol#L358-L359

## 3.5. The maximum amount of tokens sold on the presale can be exceeded.

### Severity: low

### Description

The [description file](https://docs.google.com/document/d/1g59aw3Th6zStbOWwqlmnVEdZlFMJKG1Tpue7FmheKRE/edit) says that maximum amount of sales at the presale stage is 200 millions of tokens. But there is no such check in the purchase function. There is only a check for the maximum amount of tokens (at the sale stage).

### Code snippet

https://github.com/emil-dudnyk/shipnextico/blob/e39eaf9ebd6077d6843a5bb743b795839a8e135d/contracts/ShipCoinCrowdsale.sol#L354

# 4. Conclusion

The audited contract do not follow the basic idea of decentralization, users are exposed to extra risks same as with centralized systems.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/d546f79935d2c576dc1425526fd30cb4

https://gist.github.com/yuriy77k/a93c2b090f0cdb2b90d6993c8b8818e8

https://gist.github.com/yuriy77k/1ac3b0c29aa89f5ba6c1f17ee28cf6b4
