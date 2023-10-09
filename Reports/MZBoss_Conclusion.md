# [MZBoss smart contract (casino share)](https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

This contract isn't safe to be used or invest in by any person, it contains:

- Logical errors.
- Multiple possible exploits.
- Non respect of a common unit when computing values, which lead to overflow or underflow or throw if SafeMath is used.
- Multiple other errors.

In conclusion, after analyzing the smart contract, there was no pre-testing before submitting the audit request. 

# 2. High severity issues:

## 2.1. Comparison operators Error

### Description

The contract developers used assignment operators instead of comparison operators in multiple `if` conditions and `require`, these errors can lead to:

- Set everyone as administrator..
- Set everyone as profit address.
- Always recognize address as game pot address, when a user sends ether using fallback function (normal ether transaction).
- Always throw `reinvest` function.
- Always throw `buy` function.

### Code snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L39

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L45

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L249

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L262

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L277

## 2.2. Logical Error

### Description

Both units of `ambassadorQuota_` and `tokenPriceInitial_` are ether therefore the multiplication by `1e18` doesn't make sense.
once corrected, another issue with this condition is that it will never be true since `tokenSupply_` will never be superior to `SafeMath.div(ambassadorQuota_, tokenPriceInitial_)` because the amount of ether sent by the investors or ambassadors is taxed 15% before computing the `_amountOfTokens` bought by the investors or ambassadors.

```
	if(tokenSupply_ > SafeMath.div(ambassadorQuota_, tokenPriceInitial_ * 1e18)) { ... }
```

### Code snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L603

### Recommendation

Replace the condition by:

```
	if(tokenSupply_ >= SafeMath.div(SafeMath.mul(ambassadorQuota_, 100-dividendFee_)/100, tokenPriceInitial_)) { ... }

```


# 3. Medium severity issues:

## 3.1. Initial Stage Condition

### Description

The ambassadors phase is set to automatically stop, with 20 ambassadors and 1 ether limit for each ambassador. the maximum amount that will be reached is 20 ethers, but the condition to disable ambassadors phase require the amount of ethers collected to be equal or less than `ambassadorQuota_` which is equal to 20 ethers. the condition to disable the initial stage will never be met.

```
	if( onlyAmbassadors && (SafeMath.sub(totalEthereumBalance(), _amountOfEthereum) <= ambassadorQuota_ )) { ... }
```
### Code snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L57

### Recommendation

Even if the ambassadors phase can be disabled by calling `disableInitialStage`, the contract should not contain any logical error.
change the condition operator to `<`.

## 3.2. 45% fee Charging

### Description

The project description specifies 15% fee when transferring tokens from one address to another, but in total 45% fee is charged.
In the comment it's clearly set that 15% is taxed on the sender and another 15% is charged on the receiver side, and for unknown reasons another 15% is taxed when adding the amount to the receiver.

### Code snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L389#L391

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L398

## 3.3. Initial Stage Disabling

### Description

Disabling the initial stage manually by the administrator can raise some apparent issues due to the logic flow, If it is disabled before that the condition requirement  is not reached the investors won't be able to get their shares.

### Code Snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L418#L423

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L603

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L607

### Recommendation

Either remove the function if the initial ambassadors stage is guaranteed to be successful  or change the condition in error [3.2]() by the following:

```
	if(!onlyAmbassadors) { ... }
```

This is assuming that the previous errors were taken into account. the contract developers should take into consideration that if they implement this recommendation another error can raise if the ambassadors doesn't invest, and the `tokensSupply_` remain null a division by zero is possible making the contract non-reusable.

## 3.4. Token to Ethereum Conversion

### Description

- Before converting tokens to ethereum when calling `tokensToEthereum_` an operation is subtracting `1e18` from `_tokens`, no reason is advanced for this, but we assume it is an error.

- The base units of different variables were not respected , either state or local variables. `tokenPriceInitial_` is multiplied by `1e18` when it is already in wei.

### Code Snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L665#L674

## 3.5. Casting Error

### Description

`dividendsOf` cast an operation value from `int256` to `uint256` this value is return by the same function and used multiple times in the smart contract without casting it back to `int256`.

Please note that `myDividends` return the value of `dividendsOf`.

### Code snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L519#L525

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L502

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L25

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L283

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L320

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L386


# 4. Low severity issues:

## 4.1. Investor Shares Computation

### Description

The dividends computation logic remains unclear, the Developers should provide more information since there is no clear pattern on how it is implemented.

### Code snippet

https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L519#L525


## 4.2. The administrator can accidentally remove himself.

### Code snippet

* [Line 430](https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L430)

```solidity
function setAdministrator(address _identifier, bool _status)
    onlyAdministrator()
    public
{
    administrators[_identifier] = _status;
}
```

### Description

The administrator can accidentally remove himself and leave the contract without management.

### Recommendation

Add the appropriate verification.


# 5. Minor observation.

## 5.1. `buy()` and `potDistribution()` functions always returns `false`.

### Code snippet

* [Line 250](https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L250)
* [Line 679](https://gist.github.com/Mizhentaotuo/58fc7c680c86f5d25bf4a409d990ab7f#file-mzboss-L679)

Example:
```solidity
function buy()
   public
   payable
   returns(uint256)
{
   require(profitAddress_[msg.sender] = false);
   purchaseTokens(msg.value); // <-- The return statement is missing.
}
```

### Description

The `return` statement is missing. And result of function execution is always `false`. This can lead to incorrect behavior of the dApp.

### Recommendation

Add the `return`.



# Revealing audit reports:

https://gist.github.com/yuriy77k/5708bedbff5c829c4cc02e99b867b496

https://gist.github.com/yuriy77k/4793172749237ea567ce37176d4d4754

https://gist.github.com/yuriy77k/9f8fa8fb03b594d000856e46b7ef0ca9

