# 1. Summary

[Viribus Token](https://etherscan.io/address/0x0e08b02d89ca66cf157c6658c02933ef0bc38cb6#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

ERC20 token + crowdsale: 
1. Standard ERC20 functionality 
2. Oraclize update rate ETH-USD every 12 hours
3. Selling tokens

Following Viribus token website 1 Token = 1 Diamand the price is set for one token to be 6600 USD, on this particular case the token emission is a very important process and should be completely backed by the equivalent amount of ether that represent the value of the diamond stored in USD.

https://viribustoken.com/

# 2. In scope

- [VRBS.sol ](https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24)

# 3. Findings

In total, **6 issues** were reported including:

- 3 low severity issues.

- 3 minor observation.

No critical security issues were found.

## 3.1. Required check for an `0x0` address.

### Severity: low

### Description

The `setHolder` allow to set new holder address, but there is no check of empty input value. Is possible to lose the funds by sending ETH to 0x0 address.

### Code snippet

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1736

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1764

### Recommendation

Use condition like `require(newHolder != address(0))`.

## 3.2. If the server is not responding

### Severity: minor observation

### Description

1. Note that from the oraclize documentation:
> If and when the server is not responding or is unreachable, we will send you an empty result.

In this case it will be 0:
```solidity
cursETHtoUSD = parseInt(result);
```
It is not dangerous here. Just recursive update of the price will break:
```solidity
function updateCostETH() internal {
    costETH = (costUSD * 1 ether).div(cursETHtoUSD); // division by zero
    emit NewCostETH(costETH);
}
```

And will need to manually run `updatePrice` again.

2. If there are any changes with "https://api.gdax.com" API you will not be able to change it to another URL.

### Code snippet

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1704

## 3.3. Start Sales Rate

### Severity: minor observation

### Description

`cursETHtoUSD` rate is first initialized to 150 USD, when in reality the rate could be different. Please note that since the deployment of the contract the sales are open which means that users might invest using a wrong ETH/USD rate.

### Code snippet

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1714

### Recommendation

Use Oraclize API to update the price and do not authorize the sale function to run until the price update by Oraclize callback.

## 3.4. Oraclize Balance Refill

### Severity: minor observation

### Description

`addBalanceForOraclize` is member of `VRBS`, knowing that users might possibly confuse it with the sale function, `addBalanceForOraclize` should be accessible by the owner only. Please note that `updatePrice` price is payable also which mean that users do not need an extra function to add any balance to the contract to enable the call.

### Code snippet

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1728

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1714

### Recommendation

The function should be marked with `onlyOwner` modifier or removed.

## 3.5. Approval Decrease

### Severity: low

### Description

`decreaseAllowance` function revert if the value to be decreased from the total allowed is higher than the allowance (most implementations set the value to zero in this case), a racing condition can occur in this case between the spender and approver and the approver can lose tokens under some specific cases.

### Code snippet

https://gist.github.com/yuriy77k/ee656af7383f005bb13be7687f86be24#file-vrbs-sol-L1481#L1487

### Recommendation

Set the value of the allowance to zero if the amount to be decreased is higher than the remaining allowance.

## 3.6. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

# 4. Conclusion

The smart contract is safe, but some low severity issues were found.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/2ef16716ad109af0b06fde0e5e3150ad

https://gist.github.com/yuriy77k/7e3585ba8c31b28215bbd0438c5d0af3

https://gist.github.com/yuriy77k/9d30caef13356581b5538bbe8ac07d1e
