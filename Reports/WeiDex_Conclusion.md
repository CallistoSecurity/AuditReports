# 1. Summary

[WeiDex](https://github.com/weichain/weiDex) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [CrowdsaleLib.sol](https://github.com/weichain/weiDex/blob/master/contracts/utils/CrowdsaleLib.sol) github commit hash a4d7a230c71f2c1c03b57e33b73bda1440e62b2c.
- [Math.sol](https://github.com/weichain/weiDex/blob/master/contracts/utils/Math.sol) github commit hash d3f4f17c4bd74c89b34d429d04e95b001c4cb32a.
- [OrderLib.sol](https://github.com/weichain/weiDex/blob/master/contracts/utils/OrderLib.sol) github commit hash d3f4f17c4bd74c89b34d429d04e95b001c4cb32a.
- [Ownable.sol](https://github.com/weichain/weiDex/blob/master/contracts/utils/Ownable.sol) github commit hash d3f4f17c4bd74c89b34d429d04e95b001c4cb32a.
- [SafeToken.sol](https://github.com/weichain/weiDex/blob/master/contracts/utils/SafeToken.sol) github commit hash 30d87996b4f8f3f422c7a0374e5587ee8de362bb.
- [Token.sol](https://github.com/weichain/weiDex/blob/master/contracts/utils/Token.sol) github commit hash a257e2aba8920107ccc27e8984b4b19c330c76dd.
- [ERC20.sol](https://github.com/weichain/weiDex/blob/master/contracts/token/ERC20.sol) github commit hash d3f4f17c4bd74c89b34d429d04e95b001c4cb32a.
- [ERC20Detailed.sol](https://github.com/weichain/weiDex/blob/master/contracts/token/ERC20Detailed.sol) github commit hash a257e2aba8920107ccc27e8984b4b19c330c76dd.
- [IERC20.sol](https://github.com/weichain/weiDex/blob/master/contracts/token/IERC20.sol) github commit hash d3f4f17c4bd74c89b34d429d04e95b001c4cb32a.
- [IOldERC20.sol](https://github.com/weichain/weiDex/blob/master/contracts/token/IOldERC20.sol) github commit hash 30d87996b4f8f3f422c7a0374e5587ee8de362bb.
- [DailyVolumeUpdater.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/DailyVolumeUpdater.sol) github commit hash 0542a8757fad1b22605732c2620905c2a59f0442.
- [DiscountTokenExchange.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/DiscountTokenExchange.sol) github commit hash 414532ee95090ea9a9b6a22d8e7b605c443a5f3b.
- [Exchange.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/Exchange.sol) github commit hash 414532ee95090ea9a9b6a22d8e7b605c443a5f3b.
- [ExchangeOffering.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/ExchangeOffering.sol) github commit hash a4d7a230c71f2c1c03b57e33b73bda1440e62b2c.
- [IUpgradableExchange.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/IUpgradableExchange.sol) github commit hash 60cd6393d495d25a65361b3b09defa51376b2522.
- [OldERC20ExchangeSupport.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/OldERC20ExchangeSupport.sol) github commit hash f1767cd2d2cc60d1ddc7e2353928b124b9cd2e46.
- [ReferralExchange.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/ReferralExchange.sol) github commit hash 66116a904c384b16a4e1db61ff0984f36f316341.
- [UpgradableExchange.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/UpgradableExchange.sol) github commit hash 60cd6393d495d25a65361b3b09defa51376b2522.
- [WeiDex.sol](https://github.com/weichain/weiDex/blob/master/contracts/exchange/WeiDex.sol) github commit hash 414532ee95090ea9a9b6a22d8e7b605c443a5f3b.

## 2.1. Excluded

The following mock contracts (contracts that allow testing of production ones) are excluded from the audit:

- [ERC20Mock.sol](https://github.com/weichain/weiDex/blob/master/contracts/mocks/ERC20Mock.sol) github commit hash b41ca7abdbcfc0522e1270db300bfc7942b07eb1.
- [ExchangeMock.sol](https://github.com/weichain/weiDex/blob/master/contracts/mocks/ExchangeMock.sol) github commit hash 60cd6393d495d25a65361b3b09defa51376b2522.
- [OldERC20.sol](https://github.com/weichain/weiDex/blob/master/contracts/mocks/OldERC20.sol) github commit hash a257e2aba8920107ccc27e8984b4b19c330c76dd.
- [OldERC20Mock.sol](https://github.com/weichain/weiDex/blob/master/contracts/mocks/OldERC20Mock.sol) github commit hash b41ca7abdbcfc0522e1270db300bfc7942b07eb1.
- [UpgradableExchangeMock.sol](https://github.com/weichain/weiDex/blob/master/contracts/mocks/UpgradableExchangeMock.sol) github commit hash 60cd6393d495d25a65361b3b09defa51376b2522.
- [WeiDexMock.sol](https://github.com/weichain/weiDex/blob/master/contracts/mocks/WeiDexMock.sol) github commit hash d3f4f17c4bd74c89b34d429d04e95b001c4cb32a.

# 3. Findings

In total, **11 issues** were reported including:

- 3 medium severity issues.

- 8 low severity issues.

No critical security issues were found.

## 3.1. Crowdsale Registration

### Severity: medium

### Description

`registerCrowdsale` function member of `ExchangeOffering` contracts allow the owner to rewrite any information about an ongoing crowdsale, `ExchangeOffering` owner do not represent the team related with the project token to be sold, critical information like crowdsale wallet can be changed making at risk the fund raised by the crowdsale.

In order for the WeiDex to be trusted for crowdsales the team should remove any interactions after the first set up of the crowdsale, or give the administration access to the crowdsale project team.

### Code snippet

https://github.com/RideSolo/weiDex/blob/master/contracts/exchange/ExchangeOffering.sol#L22#L48

## 3.2. Decrease Approval Logic

### Severity: low

### Description

`decreaseAllowance` function member of `ERC20` contract do not check if the value of `subtractedValue` is higher than `_allowed[msg.sender][spender]`, making the transaction execution throw. 

This implementation of `decreaseAllowance` can create a racing condition, if the address owner that allows a value to be spent by another address wants to remove the allowance, and the spender send a transaction to transfer any amount of that value, the token owner transaction will throw.

Developers should add a condition to check the above case and set the value of `_allowed[msg.sender][spender]` to zero. 

### Code snippet

https://github.com/RideSolo/weiDex/blob/master/contracts/token/ERC20.sol#L145#L158

## 3.3. Left Tokens Withdrawal

### Severity: low

### Description

`withdrawWhenFinished` allow the fund (tokens) to be returned to the crowdsale wallet, this can be an issue if the project has promised to burn the tokens after the crowdsale.

Also the contract developers should check the token balance left just in case if the tokens amount that was registered was wrong.

### Code snippet

https://github.com/RideSolo/weiDex/blob/master/contracts/exchange/ExchangeOffering.sol#L90#L100

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/weiDex/blob/master/contracts/token/ERC20.sol#L1#L205

## 3.5. No zero address checking

### Severity: low

### Description

There is no check for zero-address as an input value. And this could potentially lead to the burning of fees (an exchange fee and a referral fee).
No zero address checking in functions [`trasnfer`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/mocks/OldERC20.sol#L30), [`trasnferFrom`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/mocks/OldERC20.sol#L58), [`setNewExchangeAddress`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/mocks/UpgradableExchangeMock.sol#L22), [`setDiscountToken`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/DiscountTokenExchange.sol#L40), [`setFeeAccount`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/Exchange.sol#L97) (and in [constructor](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/Exchange.sol#L30)).

## 3.6. Referrer will not receive his reward then `dailyVolumeCap` more than 1000 ETH.

### Severity: low

### Description

The [whitepaper](https://weidex.market/documents/whitepaper.pdf?v=1.0.1) says that one the exclusive features is referral program:

> Inviting a friend using the weiDex referral
system allows you to receive 20% of their
exchange trading fee sent to your wallet.
The more people you invite, the better the
bonuses will be!

Also it presents the calculation of the referrers profit:

|Referrals trade volume $/day|1000|2000|4000|8000|9000|9000|10000|20000|30000|50000|150000|
|--|--|--|--|--|--|--|--|--|--|--|--|
|Referral bonus $/day|0.2|0.4|0.8|1.6|1.8|1.8|2|4|6|10|30|
|Referral bonus $/period|18|36|72|144|162|162|180|360|540|3650|10950|

But referrer will not receive his reward when the daily exchange volume exceeded `dailyVolumeCap`. In current time it is 1000 ETH or about $100 000. The table shows the volume of referrals of the particular referrer, but the fees will not be paid if daily volume of the **whole exchange** is exceeded. Thus, the calculations of the daily profit of the referrer can be very different in real conditions.

Yes, `dailyVolumeCap` variable can be increased,  but then another exclusive feature of the project may not work:

> Free trades for all users during high trade volume times.

### Code snippet

* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/WeiDex.sol#L168
* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/WeiDex.sol#L212
* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/DailyVolumeUpdater.sol#L54-L60

## 3.7. Investors will not receive the presented profit.

### Severity: low

### Description

As in previous issue the whitepaper contains the table of expected weiDex profit.

> Trade fee 0.1% per trader, 0.2% for the whole trade

Timeline|Q4 2018|Q1 2019|Q2 2019|Q3 2019|Q4 2019|Q1 2020|Q2 2020|Q3 2020|Q4 2020|2021|2022
--|--|--|--|--|--|--|--|--|--|--|--
Expected volume $/day|500000|1000000|2000000|4000000|5000000|5000000|6000000|10000000|30000000|100000000|500000000
Expected weiDex profit $/day|1000|2000|4000|8000|10000|10000|12000|20000|60000|200000|1000000
Expected weiDex profit $/period|90000|180000|360000|720000|900000|900000|1080000|1800000|5400000|73000000|365000000

And here there is the same problem as in previous issue: the calculations of the profit can be very different in real conditions.

### Code snippet

* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/WeiDex.sol#L168
* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/WeiDex.sol#L212

## 3.8. The owner of the exchange can set any commission he want.

### Severity: medium

### Description

The exchange owner can set any fee, up to 100% of the transaction. This opportunity does not go well with the concept of **decentralized exchange**.

### Code snippet

* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/Exchange.sol#L86

### Recommendation

Need to check the max rate of fee.

## 3.9. When updating the exchange contract the user should specify the address of the new contract.

### Severity: medium

### Description

The owner can provide one version of the contract for auditing, but when the migrating and it comes to transferring funds, he [can change](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/UpgradableExchange.sol#L21) the `newExchangeAddress` again (to a malicious contract) and users won't know about it unless they specifically check it out. And the funds will be lost.

For greater security, it would be better if users specified the address of the new contract that was audited. And this address can be further compared with the `newExchangeAddress` in the `migrateFunds` function. So users can be sure that they are transferring money to a verified contract.

We consider this problem in a situation where the owner had selfish intentions. Or rights of the owner took possession of the hacker. This is important issue for a **decentralized exchange**.

### Code snippet

* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/UpgradableExchange.sol#L42

## 3.10. The `filledAmounts` of an order may exceed the order amount for buy.

### Severity: low

### Description

The `cancelOrder` function can be called several times, each time increasing `filledAmounts` of the order. This may lead to incorrect statistics being displayed in the dapp interface.

### Code snippet

* https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/Exchange.sol#L342-L368

## 3.11. Possible misunderstanding while createCrowdsale.

### Severity: low

### Description

There are no descriptions of parameters function `createCrowdsale`. It can be assumed, that [`tokenRatio`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/utils/CrowdsaleLib.sol#L51) is integer number of tokens per 1 ETH (without decimals), another way it causes wrong calculations in function [`buyTokens`](https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/ExchangeOffering.sol#L69).

### Code snippet

https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/utils/CrowdsaleLib.sol#L38-L59

https://github.com/weichain/weiDex/blob/18997e1837b964aae4a1374f632a0482c2bc69b2/contracts/exchange/ExchangeOffering.sol#L69

# 4. Conclusion

The contract contain some medium severity issues that need to be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/2b8cd99d013c8516b02f5d32771d18bb

https://gist.github.com/yuriy77k/4f79e4094d7d55cab66f238e84e1fd93

https://gist.github.com/yuriy77k/2bdc4ad6bfbaa31e77bcac0a0bbfd8d6
