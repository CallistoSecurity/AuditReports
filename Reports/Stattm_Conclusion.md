# [Stattm](https://github.com/Stattm/Stattm.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The smart contract has critical issues and can not be deployed. Also pay attention on issue 5.1.

# 2. High severity issues:

## 2.1. It is possible for not whitelisted address to buy tokens in the Private Sale stage.

### Code snippet

* [StattmPrivSale.sol, lines 34-36](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/StattmPrivSale.sol#L34-L36)

### Description

In the fallback function of `StattmPrivSale` contract is prohibition of purchase of tokens for not whitelisted addresses, but `buy` function is public and it is possible to call it directly without restrictions.

### Recommendation

Use right modifier for buy function or make another solution.

## 2.2. It is possible for investors and for owner of crowdsale to lose money.

### Code snippet:
* [AbstractCrowdsale.sol, line 78](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L78)

### Description

This situation is possible when there are purchases from not whitelisted addresses and without this purchases the softCap in not reached. Steps of possible case:
1. Crowdsale is finished and softCap is reached.
2. Some whitelisted addresses [received their tokens](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L84-L98).
3. Some addresses(not whitelisted) [returns their ethers](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L66-L83) and after it softCap turned to not reached.
4. And now the owner [can't to receive](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L125) funds received during the crowdsale and address from step 2 can't to return their ethers [because](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L74) `ethPayed[msg.sender]` is zero.


# 3. Medium severity issues:

## 3.1. Anyone can change a name-address pair.

### Code snippet

* [NameRegistry.sol, line 7](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/NameRegistry.sol#L7)

### Description

`setAddress` is a public function and anyone can call it and change address for any name.

### Recommendation

Use `owner` variable to make a right access rule.

## 3.2. Wrong `forceReturn` function.

### Code snippet

* [AbstractCrowdsale.sol, lines 37-49](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L37-L49)

### Description

Apparently, the purpose of this function is manual sending tokens to the not whitelisted `_adr` that already bought tokens but haven't received yet. But by mistake in this function is used `msg.sender` instead `_adr` variable to get the value of purchased tokens and to send them. Besides, purchased tokens are not counted in soft capitalization although ethers were received:
```solidity
totalTokensToTransfer = totalTokensToTransfer - amountToSend;
softCapReached = totalTokensToTransfer >= softCapInTokens();
```

## 3.3. In some situation it is possible for whitelisted address to get tokens for free in the Private Sale stage.

### Code snippet

* [AbstractCrowdsale.sol, lines 112-119](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L112-L119)

### Description

In case softCap is 0 the tickets are transferred immediately:
```solidity
if(tokensToTransfer[msg.sender] > 0 &&  token.isWhiteListed(msg.sender) && softCapInTokens() == 0){
  // ...
  require(token.transfer(msg.sender,amountOfTokens));
  // ...
}
```
But here `ethPayed[msg.sender]` doesn't turn to 0. And it may happen that this address is excluded from the white list (it could be social engineering). In this case is possible to [withdraw ethers](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L80):
```solidity
if (getNow()  > saleEndTime()
  && (softCapReached == false
    || token.isWhiteListed(msg.sender) == false)) {

    // ...
    require(msg.value == 0);
    // ...
    uint256 amountToReturn = ethPayed[msg.sender];
    // ...
    msg.sender.transfer(amountToReturn);
    // ...
}
```


### Recommendation

Remake this function based on your goals.


# 4. Low severity issues:

## 4.1. Values in Smart Contracts and in Projects' website are different.

### Description

On the website description the private sale end on `GMT: Tuesday, October 9, 2018`, however on the [`flatPrivSale.sol`](https://github.com/Stattm/Stattm.sol/blob/master/flatPrivSale.sol#L579#L581) the end date is set to `GMT: Friday, October 5, 2018 6:58:02 PM` and on [`StattmPrivSale.sol`](
https://github.com/Stattm/Stattm.sol/blob/master/contracts/StattmPrivSale.sol#L19#L21) the date is set to `GMT: Tuesday, October 9, 2018 12:00:00 AM`.

Another minor remark is that in both contract the date is commented to be for the 5th of october.

[Hard cap](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/StattmICO.sol#L11) at ICO should be 30,000,000 STTM, but in Smart Contract there is 35,000,000
[Hard cap](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/StattmITO.sol#L20) at ITO should be 20,000,000 STTM, but in Smart Contract there is 25,000,000
[Soft cap](https://github.com/Stattm/Stattm.sol/blob/master/contracts/StattmPrivSale.sol#L7) at private sale should be 1,000,000 STTM, but in Smart Contract there is 0

All info has taken from [project's website](https://stattm.com).

### Recommendation

There are something wrong neither in you Smart contracts nor website. You need to correct that values.

## 4.2. Known vulnerabilities of ERC-20 token

### Code snippet

* [ERC20.sol, line 79](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/9b3710465583284b8c4c5d2245749246bb2e0094/contracts/token/ERC20/ERC20.sol#L79)

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4.3. Logic of buying incorrect. 

### Description

In sale period if user will reach the hardcap his buying transaction will be reverted, that means he will lose the gas. [Line 105](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L105)

### Recommendation

User should get maximum tokens he can buy - `hardCapInTokens() - totalTokensToTransfer`, where `totalTokensToTransfer` without his buying token amount and extra Ethers should be refunded him.

## 4.4. Zero address checking require. 

### Description

* in [`constructor`](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L30) there are possibility to set `beneficiary` as zero address.

* in [`constructor`](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/StattmPrivSale.sol#L25) at private sale there are possibility to set `dev` address as zero address.

### Recommendation

Add zero address checking
```solidity
require(_beneficiary != address(0));
```
## 4.5. Last version of `MintableToken.sol` has significant changes.

### Code snippet

* [StattmToken.sol, line 16](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/StattmToken.sol#L16)

* [StattmToken.sol, line 38](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/StattmToken.sol#L38)

### Description

Mintable token of OpenZeppelin solution in up to date version doesn't have `mintingFinished` variable. The reason is described [here](https://github.com/OpenZeppelin/openzeppelin-solidity/issues/1348). And used `_totalSupply` variable instead `totalSupply_`.

### Recommendation

Use the latest version of [this token](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC20/ERC20Mintable.sol).

# 5. Minor observation:

## 5.1. There left test function. 

### Description

[`setNow`](https://github.com/Stattm/Stattm.sol/blob/91608d8ec63a96c652ae02ae9781d165d1d9c003/contracts/AbstractCrowdsale.sol#L58) function is public and everyone can change the `_now` variable and can hurt the smart contracts. There are comment that this function is test function, so it should be deleted from main contracts.

### Recommendation

This function should be deleted before deploying to mainnet.



# Revealing audit reports:

https://gist.github.com/yuriy77k/b1e03f082aef69f022eeedec3837f0d2

https://gist.github.com/yuriy77k/9608b6d16224da26a07bc073b032dec0

https://gist.github.com/yuriy77k/dd9f23d99b7e5a948ca203876ff1cb7f
