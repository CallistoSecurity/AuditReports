# 1. Summary

[W12 Project](https://github.com/w12-platform/W12-Product-Blockchain-Protocol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Versionable.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/versioning/Versionable.sol) github commit hash 8077461a459caf5bc12840c4f65e8daf462e66c0.
- [VersionsLedger.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/versioning/VersionsLedger.sol) github commit hash a6239bfcb6452ff242bb529f81fdff67c73b29ea.
- [PercentMock.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/libs/PercentMock.sol) github commit hash 4d2aee23e2d1e474faa28f5968e21343155a0a4e.
- [Percent.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/libs/Percent.sol) github commit hash 4d2aee23e2d1e474faa28f5968e21343155a0a4e.
- [WToken.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WToken.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [WTokenStub.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WTokenStub.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [WTokenTestHelper.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WTokenTestHelper.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [ITokenExchange.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/ITokenExchange.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [ITokenExchanger.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/ITokenExchanger.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [ITokenLedger.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/ITokenLedger.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [TokenExchanger.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/TokenExchanger.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [Pricer.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/rates/roles/Pricer.sol) github commit hash 13f6999cbfa237efc8e8f55b56e6afcc71dc0571.
- [Symbols.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/rates/Symbols.sol) github commit hash 13f6999cbfa237efc8e8f55b56e6afcc71dc0571.
- [Rates.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/rates/Rates.sol) github commit hash 13f6999cbfa237efc8e8f55b56e6afcc71dc0571.
- [IW12Crowdsale.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/interfaces/IW12Crowdsale.sol) github commit hash 46686dea921a80a99ff03fcfc1c42bb94d4e4404.
- [IW12CrowdsaleFactory.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/interfaces/IW12CrowdsaleFactory.sol) github commit hash a142e21cef302c259fbd0fff5e4fdcba27e356d4.
- [IW12Fund.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/interfaces/IW12Fund.sol) github commit hash 024f97d3aeb51fb7321237e0a3994c991f8c2a60.
- [IW12FundFactory.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/interfaces/IW12FundFactory.sol) github commit hash 876cb4dd36ef36c7c4fd72cee0ed348201d63719.
- [W12Fund.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12Fund.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [W12FundCrowdsaleStub.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12FundCrowdsaleStub.sol) github commit hash 46686dea921a80a99ff03fcfc1c42bb94d4e4404.
- [W12FundFactory.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12FundFactory.sol) github commit hash 8077461a459caf5bc12840c4f65e8daf462e66c0.
- [W12FundStub.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12FundStub.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [W12ListerStub.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12ListerStub.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [W12CrowdsaleStub.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12CrowdsaleStub.sol) github commit hash 8077461a459caf5bc12840c4f65e8daf462e66c0.
- [W12CrowdsaleFactory.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12CrowdsaleFactory.sol) github commit hash 8077461a459caf5bc12840c4f65e8daf462e66c0.
- [W12Lister.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12Lister.sol) github commit hash 43c49e0c223259a4227db2c0500ba4b91b195b8c.
- [W12Crowdsale.sol](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12Crowdsale.sol) github commit hash 46686dea921a80a99ff03fcfc1c42bb94d4e4404.

# 3. Findings

**12 issues** were reported including:

- 1 high severity issues.

- 3 medium severity issues.

- 5 low severity issues.

- 3 minor observation.

## 3.1. Refund Logic

### Severity: High

### Description

Without correction of the Refund logic any user will be able to be refunded a higher amount of ether than what he bought, making the last investors to call refund, lose their ether. 

Each call to `getRefundAmount` will recalculate the average price of the tokens bought by the investor (following the ether left in the contract since `tranche` can withdraw some ether after every milestone reached) and multiply it by the amount of tokens to be refunded.

`getRefundAmount` function description do not match the implemented code (if this is not corrected the investor will be refunded more than what is expected), as shown below `c` is first multiplied by `a` than divided by `b` but the implemented code show the opposite:

```
    /**
        a = address(this).balance
        b = totalFunded
        c = buyers[buyer].totalFunded
        d = buyers[buyer].totalBought
        e = wtokensToRefund

        ( ( c * (a / b) ) / d ) * e = (refund amount)
    */
```

```
	uint allowedFund = buyers[buyer].totalFunded.mul(totalFunded).div(address(this).balance);
```

If the above issue is taken into consideration and changed, investors should be aware that the refund function is dependent on the remaining ether balance of the contract. In other terms the transactions order of investors calling `refund` or if `tranche` is called by the owner will impact the refund value of every investor.

The formula present a ratio of the total ethereum invested by a user divided by the total ethereum collected at the moment of the call of the function multiplied by the contract balance, since the contract balance will change after every call, the refund value of a user that will call `refund` after will be affected. Please note that the same issue can be applied to `tranche` function.

Another remark is with this logic the contract if the ICO fail and all users call refund, the contract will always have ether stuck in it.

The dependence can be easily avoided if the developers set a fixed amount to be redistributed to the investors and not use address(this).balance.

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12Fund.sol#L92#L115

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12Fund.sol#L192#L217

## 3.2. Tranche Requirement

### Severity: low

### Description

The white paper describe tranche function to allow the project to receive funds for each stage of the Roadmap, before the start of the sale of tokens:

"The project receives installments at the end of each stage of the Roadmap. For each stage of the Roadmap, before the start of the sale of tokens, the project specifies the% of the funds that it can receive, and the date from which it is possible to request these funds."

However `tranche` function is allowed to execute following the milestones not the stages.

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12Fund.sol#L230#L241

## 3.3. Token Exchanger

### Severity: medium

### Description

`addTokenToListing` member of `TokenExchanger` contract pairs tokens to be exchanged in a 1:1 ratio only following `TokenExchanger` contract logic.

If an ERC20 or WToken address is paired with different addresses, the value of `listingTokenToWToken[token]`(if the same `token` address is paired more than once) and `listingWTokenToToken[wToken]`(if the same `wToken` address is paired more than once) will be overwritten since `hasPair(token, wToken)` will return false even if one of the addresses was used before, consequences:

- `getWTokenByToken` will return the last paired value.
- `getTokenByWToken` will return the last paired value.
-  A user calling `exchange` function might end up receiving a different token or the function might just revert.

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/TokenExchanger.sol#L24#L33

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/TokenExchanger.sol#L35#L37

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/TokenExchanger.sol#L45#L49

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/TokenExchanger.sol#L45#L49

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/exchanger/TokenExchanger.sol#L55#L67

## 3.4. Pricer Removing

### Severity: medium

### Description

`removePricer` function member of `PricerRole` can be called by anyone and remove all addresses allowed with `onlyPricer` modifier, no access restriction is set.
Depending in the further use of `PricerRole` contract, the impact can vary widely.

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/rates/roles/Pricer.sol#L30#L32

## 3.5. Symbols Contract

### Severity: medium

### Description

- `removeSymbol` function member of `Symbols` does not finish updating the last symbol that has been moved to the deleted index, which may returns an erroneous index later. Depending in the further use of `Symbols` contract, the impact can vary widely.

- Another issue is that no access restriction is set in the contract, following the contract logic setup by the developers this might present a risk or not (code reusability can be affected).

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/rates/Symbols.sol#L16#L22

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/rates/Symbols.sol#L32#L40

### Recommendation

```
    function _removeSymbolByIndex(uint index) internal {
        require(index < symbolsList.length);

        if (index != symbolsList.length - 1) {
            symbolsList[index] = symbolsList[symbolsList.length - 1];
        // -- Add the following to update the index of the last symbol -- //
	    symbolIndex[symbolsList[index]] = index;
	    delete symbolsList[symbolsList.length - 1];
        }
	else delete symbolsList[index];
	// --------------------- END RECOMMENDATION --------------------- //
	
        symbolsList.length--;
    }
```

## 3.6. Zero Address

### Severity: low

### Description

`setVersion` function member of `VersionsLedger` contract do not require `_address` input parameters to be different than zero address.

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/versioning/VersionsLedger.sol#L15#L25

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WToken.sol

## 3.8. Possibility of increasing Tokens amount. 

### Severity: low

### Description

In [WhitePaper](https://tokensale.w12.io/W12-en.pdf) at page 48 there written, that tokens amount are limited and can not be increased, but smart contract has `mint()` function and trusted people can mint, also owner can add trusted people.

### Code snippet

https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/c81ef801315eb4a2535e4fc072a37b42d5b18e9d/contracts/token/WToken.sol#L164-L177

### Recommendation

There should be no possibility of minting extra tokens, because in whitepaper there written that tokens amount are limited and fixed.

## 3.9. It is possible for trusted address to call `vestingTransfer` function with zero `_value`.

### Severity: low

### Code snippet

* [token/WToken.sol, line 56](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WToken.sol#L56)

* [token/WToken.sol, line 241](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WToken.sol#L241)

### Description

The `_value` parameter is not checked for a zero value. In case if `_value` is zero the condition in line 182 will be true and a vesting time will be added to `vestingTimes` array each call of `vestingTransfer` function with zero of `_value` parameter. Thus, duplicate values will be accumulated in the `vestingTimes` array.

And if after zero value `vestingTransfer` will be transfer with positive value, then `accountBalance` function will not work correctly. For each duplicated `vestingTime` the balance will be subtracted.

It can happen by accident or maliciously.

### Recommendation

Use check for zero value of `_value` parameter. Or check for duplicates in `vestingTimes` array.

## 3.10. TODO comments left. 

### Severity: minor observation

### Description

[`TODO`](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/interfaces/IW12Crowdsale.sol#L9) comment left in code.

## 3.11. Modifier has no sense. 

### Severity: minor observation

### Description

Modifier [`onlyFrom`](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/W12FundStub.sol#L37-L39) allows everyone and can be deleted and not used.

## 3.12. No need of checking. 

### Severity: minor observation

### Description

[Checking](https://github.com/w12-platform/W12-Product-Blockchain-Protocol/blob/master/contracts/token/WToken.sol#L165) has no sense, because SafeMath library will do it automatically.


# 4. Conclusion

The audited contracts contain different severity level issues that need to be fixed before deployment including a high severity.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/9bebeab10b57be632c6aa5592c5060af

https://gist.github.com/yuriy77k/0ac2e633c833e87abcb466807df21aec

https://gist.github.com/yuriy77k/430045c0823ecadacf952720cb62dda9

