# [MZBoss smart contract_v2](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The current contract version is not safe to be deployed, multiple high severity issues were highlighted.

# 2. High severity issues:

## 2.1. Tokens Purchase

### Severity: High

### Description

Anyone can exploit `purchaseTokens` making the input value of `_amountOfEthereum` not equal to the real `msg.value`. therefore, buying tokens with an `_amountOfEthereum` that he didn't send. There is no condition checking if `_amountOfEthereum == msg.value`. 
selling the tokens back to the contract will make the attacker able to withdraw ether based on the fake `_amountOfEthereum` that he used.

### Code snippet

https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L194#L240

## 2.2. Ethereum to Tokens Conversion

### Severity: High

### Description

the conversion does not take into account the decimals, which will lead the users to lose (_ethereum % tokenPriceInitial_) and will return token value without the decimals which will impact many other functions.

### Code snippet

https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L550#L564

### Recommendation

```
//	tokenPriceInitial_ ---> 10**decimals
//	_ethereum          ---> x

    function ethereumToTokens_(uint256 _ethereum)
        public
        pure
        returns(uint256)
    {
        require (_ethereum > 0);
	uint256 decimals_ = decimals;
        uint256 _tokenPriceInitial = tokenPriceInitial_;
        uint256 _tokensReceived = 
                    SafeMath.div
                    (
                        SafeMath.mul(_ethereum,10**decimals_), 
                        _tokenPriceInitial
                    );
        return _tokensReceived;
    }
```
## 2.3. Tokens to Ethereum Conversion

### Severity: High

### Description

Almost the same conversion mistake as the previous error, in this case the `sell` function will be impacted if the first error is updated.

### Code snippet

https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L570#L580

### Recommendation
```
//	10**decimals   ---> tokenPriceInitial_
//	_tokens ---> x

     function tokensToEthereum_(uint256 _tokens)
        internal
        pure
        returns(uint256)
    {   
        uint256 tokens_ = _tokens;
	    uint256 decimals_ = decimals;
        uint256 _etherReceived =
	        SafeMath.div(
		        SafeMath.mul (tokenPriceInitial_, tokens_),
		        10**decimals_
	        )
        return _etherReceived;
    }
```

# 3. Medium severity issues:

## 3.1. Dividends Computation

### Description

After multiple tests, the dividends computation has given erroneous output. in a special case a user was not able to sell & withdraw his ether since the [`dividendsOf`](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L421) was returning a value higher than the contract balance. the device should review the dividends computation mechanism (the test case is described below).

Test case: three invesors sent ether to buy tokens, all investors sold and withdraw their balance. The last investors was not able to get hist balance back.


# 4. Low severity issues:

## 4.1. Transaction fee

### Description

When an investor sells his shares a transaction fee is deducted from the ether that he gained, knowing that the transaction fees is paid by the investors when calling any function in the contract. 

The devs should explain the intention of this operation, especially since this fee is not shared between holders but will be lost in the contract.

### Code snippet

https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L332


# 5. Minor observation:

## 5.1. Profit Addresses

### Description

As per the code, only administrators can set profit addresses, ambassador cannot add profit addresses remove the related comment. 

### Code snippet

https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L363

## 5.2. Minimum Purchase Limit

### Description

A Minimum purchase limit is set to be 10^20 (100 MZB tokens) for normal investors (not ambassadors), the commented description of the contract does not mention any buy limit.

### Code snippet

https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L507


## 5.3. No need of casting to int256.

### Description

In lines [276](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L276), [340](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L340), [536](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L536) there is no sense of casting to int256, because return value will be uint256 because of using safeMath library.

### Recommendation

No sense of casting to int256.

## 5.4. Duplicate code

### Severity: minor

### Description

Ducplicated code on lines : [599](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L599) and [607](https://gist.github.com/Mizhentaotuo/49615f7ef06750ebb4e1f2a54593f128#file-mzboss_v2-L607)

### Recommendation

Put that line after if-else statement, and remove it inside.



# Revealing audit reports:

https://gist.github.com/yuriy77k/d9e86aa10bf621f00aaa67680850dee7

https://gist.github.com/yuriy77k/0d46d440c794a2e0a299b8506ff78a02

https://gist.github.com/yuriy77k/381591610fd03a84fbcd29b9aeb823f2

