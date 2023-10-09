# [Memority pre ICO smart contract](https://etherscan.io/address/0x7183a469dd9dcb4a336cdc2e306174d0ffea3602#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited contract contains issues with different severity level that have to be fixed before the deployment. The contract owner has too much administration power over the contract, the main goal of decentralization is not satisfied since the owner can badly impact the investors benefit.

# 2. High severity issues:

## 2.1. Ethereum to Token Conversion

### Severity: High

### Description

Taking into account the issue 3.1, the devs should correct to ethereum to token conversion since it is not well formulated.

### Code snippet

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L128

### Recommendation

```
        // tokenPrice --> 10**decimals
        // msg.value  --> amount

        uint decimals_ = decimals;
        uint amount  = (msg.value * 10**decimals) / tokenPrice;
```

## 2.2. Wrong comparison and possibility to buy more. 

### Description

`lastTotal` variable will count tokens amount, while `maxBuyAmount` will on ETH.
Also `lastTotal` variable will count only last bought tokens but without current buying amount, that means, user can send `maxBuyAmount - 1` for example at first buying and then `maxBuyAmount - 1` for current buying and in total he will buy more, than max buy amount.

### Recommendation

Compare variables with similar type, cast `lastTotal` to ETH.
Add current user sent amount to `lastTotal` variable and then check if it less then max buy amount.
```solidity
require(lastTotal + msg.value <= maxBuyAmount);
```


# 3. Medium severity issues:

## 3.1. Token Price

### Description

`tokenPrice` assignment is not the same in the constructor and `setPrices` function, `tokenPrice` should be equal to the ether value of one token.

### Code snippet

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L35

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L86

### Recommendation

Replace the assignment in the constructor by: `tokenPrice = _tokenPrice;`

## 3.2. Truncated Bonus Amount

### Description

To compute the `bonusAmount` the amount should be first multiplied by `bonusPercent` then divided by `100`, otherwise the value will be truncated.

### Code snippet

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L131

## 3.3. Transfer ownership to zero address. 

### Description

Possibility of setting zero address as `newOwner` at [`transferOwnership`](https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L7) function.

### Recommendation

Need to check if `newOwner` address is not zero address.
```solidity
require(newOwner != address(0));
```

# 4. Low severity issues:

## 4.1. Setter Functions

### Description

The input of every setter function should be checked before being assigned to its state variable, any error can cause a dysfunction of the contract.

### Code snippet

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L85#L87

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L89#L92

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L94#L96

# 5. Minor observation:

## 5.1. Total Invested Ether Computation

### Description

To compute the total invested amount of ether by an address, a mapping of addresses point to an array of elements that is filled through each investment. The code is very gas consuming and should be updated with a simple mapping of addresses pointing to an uint value that is incremented on every investment from an address.

### Code snippet

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L111#L126

## 5.2. Tokens Burning

### Description

The owner is given the power to burn any token value from any address, without confirmation from an investor. This situation cannot be accepted by any investors.

### Code snippet

https://gist.github.com/yuriy77k/3be96697cb5feef09fda2c2015484553#file-pre_ico_memority-sol-L74#L80


# Revealing audit reports:

https://gist.github.com/yuriy77k/5f530797a2c9097633531471c99588f4

https://gist.github.com/yuriy77k/53eb94174d06ede813e3e15bb6d44f6f

https://gist.github.com/pro100skm/182542dffc8f9a63e0916d42bcdcdd1f

