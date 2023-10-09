# [LOTTEREUM](https://github.com/LottereumDev/Lottereum/blob/master/smart%20contract) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

This contract is not safe and cannot be deployed, it contains high severity security issues and high severity operability issues.

# 2. High severity issues:

## 2.1. Result Prediction and Manipulation

### Description

Two possible scenarios can guarantee or increase the chance of an attacker to win the lottery:

- Malicious miner manipulation of the transactions orderings can allow the attacker to increase his chances of winning.
- using a simple attack contract an attacker can check the value of `amountOfTicketsForEthereumLotteryPurchased` if using the ethereum lottery or `amountOfTicketsForTokenLotteryPurchased` when using the token lottery. Setting simple condition to guarantee that he wins by checking if the value of the state variables satisfy the condition present in `playLotteryByToken` or `playLotteryByEthereum` functions.

### Code snippet

[buyTicketForEthereumLottery](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L238) and [playLotteryByEthereum](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L300-L320)

[buyTicketForTokenLottery](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L226) and [playLotteryByToken](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L269-L280)

### Recommendation

Look at [here](https://medium.com/@promentol/lottery-smart-contract-can-we-generate-random-numbers-in-solidity-4f586a152b27).

## 2.2. Anyone can add themselves as a participant without paying a commission.

### Code snippet
* [Line 156-169](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L156-L169)

```solidity
function addParticipiant(address partAddress, LotteryType typeLot) {
  if(typeLot == LotteryType.Token) {
    if(numPartToken == participientsTokenLottery.length) {
      participientsTokenLottery.length += 1;
    }
    participientsTokenLottery[numPartToken++] = partAddress;
  }

  if(typeLot == LotteryType.Ethereum) {
    if(numPartEthereum == participientsEthereumLottery.length) {
      participientsEthereumLottery.length += 1;
    }
    participientsEthereumLottery[numPartEthereum++] = partAddress;
  }
}
```

### Description

`addParticipiant` function is called from `participateInTokenLottery` with preliminary checks and commission paying. But also this function may be called by anyone.

### Recommendation

Mark `addParticipiant` function as `internal`.

## 2.3. Missing function visibility specifier.

### Description

The `clearParticipiants()` function has no visibility specifier, that may be maliciously exploited to destroy the whole lottery process.

### Code snippet  

https://github.com/LottereumDev/Lottereum/blob/master/smart%20contract#L171#L156

### Recommendation

Mark this functions as `internal`.

## 2.4. Missing restriction modifier.

### Description

The `importAddresses()` function is callable externally without a restriction.

### Code snippet  
https://github.com/LottereumDev/Lottereum/blob/master/smart%20contract#L179

# 3. Medium severity issues:

## 3.1. Internal Call to Restricted Function

### Description

Internal call to `playLotteryByEthereum` and `playLotteryByToken` is made inside `buyTicketForEthereumLottery` and `buyTicketForTokenLottery`, however this two functions are restricted by the `onlyAdministrator` modifier. 
when `amountOfTicketsForTokenLotteryPurchased` or `amountOfTicketsForEthereumLotteryPurchased` will reach `1000` the transactions wil fail.

### Code snippet

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L227-L228

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L239-L240

### Recommendation

Mark this functions as `internal`. And make another functions like `startLotteryByTokenManually` with `onlyAdministrator` rights.

## 3.2.Logical contradiction between 'buyTicketForTokenLottery()'  and 'playLotteryByToken()' functions.

### Description

The 'Token' and 'Ethereum' lotteries are triggered separately one from another by the 'buyTicket..' functions , but the 'playLottery...' functions implement the 'onlyAdministrator' modifier.

In case if there is intention to call the 'buyTicketForTokenLottery()' function as a contract administrator and held both lotteries at the same time, the 'allowance' mechanism needs to be used to withdraw tokens from the user account.

In case if the lotteries do may held asynchronously, the tokens of 'tokensOfLosers ' variable may be lost ,because the variable is non cumulative and reinitiated  every time when the lottery is held,
so there is a situation when multiple 'Token'  and no 'Ethereum' lotteries will be held.

### Code snippet  

https://github.com/LottereumDev/Lottereum/blob/master/smart%20contract#L228#L263#L265

## 3.3. Lost winning tickets.

### Description

Some winning tickets may be lost, being replaced by another, purchased later, ticket . 

### Code snippet  

https://github.com/LottereumDev/Lottereum/blob/master/smart%20contract#L226


# 4. Low severity issues:

## 4.1. Buy Ticket For Ethereum

### Description

A user can send more ether than the ticket price when calling `buyTicketForEthereumLottery` the extra ether will be lost in the contract.

### Code snippet

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L234

## 4.2. Lottery Winners distribution

### Description

The following description assumes that the devs didn't take into account the high severity issue mentioned in 2.1.

80% of `totalFundInTokenLottery` is distributed to the lottery winners, however after starting distribution only 96% of the original 80% is distributed (check the computation bellow).
```
(1000÷100)×(2÷100+4÷1000+2÷1000)+(1000÷20)×(4÷1000+2÷1000)+(1000÷10)×(2÷1000)+2÷10 = 0.96
```
Same issue with the 70% of `totalFundInEthereumLottery`, 96% of the original 70% will be distributed and the other 4% will be lost in the contract.

### Code snippet

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L264

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L269-L280

## 4.3. Identical Functions

### Description

`participateInEthereumLottery` and `participateInTokenLottery`contain the exact same code.
one function should handle ether to buy tickets and the other one should handle tokens.

### Code snippet

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L197

https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L209

## 4.4. Known vulnerabilities of ERC-20 token

### Code snippet
* [Line 52](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L52)

### Description

It is possible to re-approve attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

### Recommendation

The approval of a new amount must be made only when allowance is 0.

## 4.5. It is possible to lose owner rights occasionally.

### Code snippet
* [Line 17](https://github.com/LottereumDev/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L17)

### Description

`transferOwnership` function doesn't have zero address check. And if the parameters of this function will not be added then owners rights will be transfered to `0x0` address.

### Recommendation

Use `require(newOwner != address(0))`.


# 5. Minor observation:

## 5.1. Play & Withdraw Pattern

### Description

This description  assumes that the high severity security issues are solved.
A play and withdrawal pattern should be used instead of `playLotteryByEthereum` and `playLotteryByToken` that will consume a highly excessive amount of gas.

### Recommendation

The dev should set an opening and closing time where in between players can buy tickets and after closing time players will withdraw their reward by themselves.

## 5.2. Useless State Variables

### Description

`ticketsForTokenLottery` and `ticketsForEthereumLottery` map players addresses to their ticket number and own address, adding useless extra data.

### Code snippet

https://github.com/RideSolo/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L107

https://github.com/RideSolo/Lottereum/blob/8485c34a0364f8aad53ff6e719604d7d7aa32e12/smart%20contract#L108

## 5.3. Opening and Closing Time

### Description

An opening time is set and checked, but no closing time is set which means that users can play the lottery even after that it closes.

## 5.4. The `TotalSupply` variable not updating.

### Description

The 'TotalSupply'  not updating in the 'playLotteryByEthereum()'  function when distributing tokens to the investors.

### Code snippet  

https://github.com/LottereumDev/Lottereum/blob/master/smart%20contract#L339



# Revealing audit reports:

https://gist.github.com/yuriy77k/8dc77c7a2ed7433f234b5cf1b95d59b8

https://gist.github.com/yuriy77k/8918a74d8cd0f8bf0309867d25a2a632

https://gist.github.com/yuriy77k/31f0349de39989b2c88eb5be33359fbe

