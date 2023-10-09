# Security Audit Report

# 1. Summary

[Bitcademy ICO]() smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit 8b6b8687775a6b426aefbde50642877553dbd1e8

https://github.com/bitcademyfb/Bitcademy_ICO/tree/8b6b8687775a6b426aefbde50642877553dbd1e8/contracts


# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 3 notes.

## 3.1. Incorrect balance of vesting address.

### Severity: medium

### Description

In constructor of `BitcademyToken` variable `totalSupply_` must contain all available tokens, but `balances[_vestingAddress]` assigned with amount of 150% of total supply. 
This will cause assertion in [next line](https://github.com/bitcademyfb/Bitcademy_ICO/blob/9095e93ec69850333f20751cdc897d0b86b1943b/contracts/BitcademyToken.sol#L14). 

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9095e93ec69850333f20751cdc897d0b86b1943b/contracts/BitcademyToken.sol#L13

### Recommendation

If you would like to assign to vesting address 15% of Total Supply you should use following code:

`balances[_vestingAddress] = (totalSupply_.mul(15)).div(100);`

## 3.2. Not good visualization. 

### Severity: note

### Description

In assign `supply_cap` better to use amount of tokens mul to decimals, as used in others places of code.

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9095e93ec69850333f20751cdc897d0b86b1943b/contracts/Crowdsale.sol#L40

### Recommendation

Will looks better:

`uint256 public supply_cap = 410000000*(10**18);`

## 3.3. Using `goalReached` is not necessary 

### Severity: note

### Description

In the function `buyTokens` member of `Crowdsale` user could not buy tokens if goal reached. If during deploy will be set wrong amount of goal it will limit tokensale. The main limitation of Crowdsale are `supply_cap` so this additional is not needed.

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9095e93ec69850333f20751cdc897d0b86b1943b/contracts/Crowdsale.sol#L175

### Recommendation

As far as `goalReached` was not used in any other places it could be removed from here.
`// require(!goalReached());`

## 3.4. Using `goalReached` is not necessary 

### Severity: note

### Description

In the function `buyTokens` member of `PreICOBitcademyGold` user could not buy tokens if goal reached. If during deploy will be set wrong amount of goal it will limit tokensale. The main limitation of Crowdsale are `minimumTokens` so this additional is not needed.

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9095e93ec69850333f20751cdc897d0b86b1943b/contracts/PreICOBitcademyGold.sol#L178

### Recommendation

As far as `goalReached` was not used in any other places it could be removed from here.
`// require(!goalReached());`

## 3.5. Minimum Investment 

### Severity: low

### Description

The Minimum Investment is set to 1 ETH (10**18), but investor have to buy tokens for more then 1 ETH amount. If he send only 1 ETH transaction will revert.

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/9095e93ec69850333f20751cdc897d0b86b1943b/contracts/PreICOBitcademyGold.sol#L182

### Recommendation

Use following code:

`require(weiValue >= minimumInvestment);`

# 4. Conclusion

The audited smart contract has medium severity issue that should be fixed. 
Also, the smart contract don't provide ability to investors to withdraw investments if Softcap or Goal will not be reached. 
