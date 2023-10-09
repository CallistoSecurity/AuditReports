# 1. Summary

[MZBoss Version 3.0](https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [MZBoss_v3 ](https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992) github gist hash 89678eda702dd2df9c900dd7907ae992.

# 3. Findings

**5 issues** were reported including:

- 2 medium severity issues.

- 2 low severity issues.

- 1 minor observation.

## 3.1. Truncated Dividends Value

### Severity: medium

### Description

`dividendsOf` is returning a truncated value:
```
uint256 _TokensEther = SafeMath.div(tokenBalanceLedger_[_customerAddress], magnitude);
```   
tokenBalanceLedger_[_customerAddress] is divided by magnitude which will truncate the real value.

If we assume that a user has 1.5 tokens the result of `(tokenBalanceLedger_[_customerAddress] / magnitude)` is going to be `1` which is going to largely truncate the value.

### Code snippet

https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992#file-mzboss_v3-L443#L459

### Recommendation:
```
    function dividendsOf(address _customerAddress) public view returns(uint256) {   
        uint256 _TokensEther = tokenBalanceLedger_[_customerAddress];
        if ((int256(SafeMath.mul(profitPerShare_, _TokensEther)/magnitude) - payoutsTo_[_customerAddress]) > 0 )
           return uint256(int256(SafeMath.mul(profitPerShare_, _TokensEther)/magnitude) - payoutsTo_[_customerAddress]);  
        else 
           return 0;
    }
```

## 3.2. Assumed Total Dividends

### Severity: medium

### Description

```
uint256 _dividendsAssumed = SafeMath.mul(profitPerShare_, (_amountOfTokens / magnitude)) 
```
`_amountOfTokens` is always higher than `magnitude`, however, if we assume that a user bought 1.5 tokens the result of `(_amountOfTokens / magnitude)` is going to be `1` which is going to largely truncate the value.

### Code snippet

https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992#file-mzboss_v3-L532

### Recommendation

```
uint256 _dividendsAssumed = SafeMath.div(SafeMath.mul(profitPerShare__amountOfTokens),magnitude);
```

## 3.3. Token Price Description

### Severity: low

### Description

`tokenPriceInitial_` is equal to `5e15`, the description specify that 10 eth gives 200,000 MZBoss, but with hardcoded price 10 eth will provide 2000 mzboss tokens.

### Code snippet

https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992#file-mzboss_v3-L123

## 3.4. Wrong Return Value

### Severity: low

### Description

`purchaseTokens` function in an else statement returns `_amountOfTokens` but the local variable is not set inside the statement.
`_amountOfTokens` should be set with the return value of `purchaseTokensAfter`.

### Code snippet

https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992#file-mzboss_v3-L227

## 3.5. Similar modifiers. 

### Severity: minor observation

### Description

Modifiers [`tokenLeft`](https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992#file-mzboss_v3-L60) and [`enoughtToBuy`](https://gist.github.com/Mizhentaotuo/89678eda702dd2df9c900dd7907ae992#file-mzboss_v3-L67) has similar body and checking.

# 4. Conclusion

The contract contain medium issues that have to be fixed before deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c677368ae073264e20b0f4349246c8de

https://gist.github.com/alexo18/b019a0034f18fc2fb9c326db5caa6a66

https://gist.github.com/yuriy77k/cbf62d7aa36c0a1b5cd44889d06b01ca
