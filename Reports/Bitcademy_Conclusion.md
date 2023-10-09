# 1. Summary

[Bitcademy]() security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BitcademyToken.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol) github commit hash 695932da33c7ac090a0d6593bdf372977722ac87.
- [BitcademyVesting.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol) github commit hash 73a3959c584d11027db95596eacf09949683b54a.
- [Crowdsale.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol) github commit hash 41f902ba690bbc4dd9f0b195d3abf0ff00f23763.
- [PreICOBitcademyGold.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol) github commit hash b5ceb4b7f8eb4bc3055c5df38630b6b02ae784e3.
- [RefundVault.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/RefundVault.sol) github commit hash 695932da33c7ac090a0d6593bdf372977722ac87.

# 3. Findings

**16 issues** were reported including:

- 5 high severity issues.

- 3 medium severity issues.

- 7 low severity issues.

- 1 minor remark.

## 3.1. Compilation Issues

### Severity: low

### Description

`PreICOBitcademyGold` and `Crowdsale` contracts do no compile for multiple reasons:

- Both contracts inherit from Openzepplin `Ownable.sol` contract, but `Ownable.sol` is not imported in both contracts.
- Documentation parsing error, a tag with `@param _wallet` was used for both contracts, the original contract contained `_wallet` as input but it was removed after modification for both contracts. `_wallet` has been hardcoded to be `multisig_wallet`.
- Developers are assigning `multisig_wallet` to a non defined state variable `wallet` (the line should be removed in both contracts).
- When creating an instance of `RefundVault` contract in both contracts the input parameters should be`multisig_wallet` and not `wallet`.
- `updateReleaseDate` member of `PreICOBitcademyGold` is implemented twice, the [first version](https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L86#L89) should be removed since it doesn't use `onlyOwner` modifier.
 
### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L18

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L18

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L141

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L92

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L156

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L105

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L103

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L153

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L86#L89

## 3.2. Active Vault State

### Severity: High

### Description

One of the [`RefundVault`](https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L103) used by the team is instantiated during the deployment of `PreICOBitcademyGold`, all the collected fund will be sent to it. However, the vault state is never set to `State.Closed` or to `State.Refunding` by calling one of the following functions `vault.close()` or `vault.enableRefunds()`.

The project team will never be able to withdraw the vault balance, and the investors will not be able to withdraw their invested ether.
 
## 3.3. Token Distribution

### Severity: High

### Description

When an investor buy tokens on the pre-ICO or the ICO phase, the tokens are not directly sent to him, contract owner will have to call `releaseAfterMainSale` function member of `PreICOBitcademyGold` and `Crowdsale` to distribute the tokens to the investors, multiple issues will raise through this logic:

- At the moment of writing the max gas limit per block for ethereum blockchain is 8m gas. `releaseAfterMainSale` will send the bought tokens to every investor, supposing a minimum gas consumption for every investor transfer to be ~50k gas, 8m gas will be consumed for less than 160 investors. Contract developers should implement a withdrawal pattern where the users withdraw their tokens by themselves otherwise the implemented function will have a high probability to throw if the number of investors is high enough, blocking all investors tokens.
- Following the function name release after main sale and the logic in `updateReleaseDate` function, `require(release_date > now)` is wrong, the requirement should be `require(release_date < now)`.
- Every address that buy tokens is pushed in `investors`, even if a single investor buy tokens twice his address will be pushed twice. `tokenToClaim[investor]` is not set to zero after a successful transfer if the same address is contained X times in `investors` array the address will receive X times as much tokens.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L436#L444

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L321#L329

## 3.4. Multiple Investment

### Severity: high

### Description

`tokenToClaim` mapping is used to save the amount of tokens bought by the investors. If an investor with the same address by X times, the total amount is not accumulated, and when transferring the tokens bought after the release date the investor will only receive the last purchase.

This issue is applicable for both `Crowdsale` or `PreICOBitcademyGold` contract.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L309

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L294

## 3.5. Token Conversion and Bonus Computation

### Severity: medium

### Description

To get the token amount from the ether sent to `Crowdsale` or `PreICOBitcademyGold` contracts, `_weiAmount` is divided by `rate` that is updated every 3 hours by the project team using `setRate`.

The token decimals is set to 18 same as ETH decimals, and the price in USD of 1 token is set to be 0.038. At the moment of writing 1 ETH should provide the investors with ~5947 tokens without taking into account the bonuses, but since `_weiAmount` is divided and not multiplied by a certain rate the expected tokens will never be provided.

for example if `rate` is set to the minimum value possible (rate = 1), then an investor that send 1 ETH will receive 1 token.

Since the computation of the token amount developers should also recheck the bonus computation formula for `Crowdsale` contract, another remark is that `PreICOBitcademyGold` contract doesn't contain any formula to compute the promised 60% bonus (maybe the developers intention was to set the 60% bonus externally before updating `rate`).

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L294#298

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L343

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L356

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L367

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L378

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L390

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L402

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L411

## 3.6. `_getTokenAmount` Logical Error

### Severity: high

### Description

`_getTokenAmount` member of `Crowdsale` contract contain multiple error plus the above mentioned issues on [Token Conversion and Bonus Computation](#35-token-conversion-and-bonus-computation):

- `remainingTokens`,`noOfTokens` and `weiAmount` state variables are updated only if one of the condition `if(tokensInCondition > remainingTokens.sub(X*(10**18)))` is true, an `else` should be added to all the condition to handle the opposite case `false` and update their values to the following:
	- `noOfTokens` should be set to `tokensInCondition`.
	- `weiAmount` should be set to zero.
- Multiple condition used to check the different bonuses phases are badly formulated this will result in a contract that will not sell in tokens, the condition should be changed to the following:

```
	if (remainingTokens <= 300000000*(10**18) && remainingTokens > 250000000*(10**18) && weiAmount > 0) { ... }
	if (remainingTokens <= 250000000*(10**18) && remainingTokens > 200000000*(10**18) && weiAmount > 0) { ... }
	if (remainingTokens <= 200000000*(10**18) && remainingTokens > 150000000*(10**18) && weiAmount > 0) { ... }
	if (remainingTokens <= 150000000*(10**18) && remainingTokens > 100000000*(10**18) && weiAmount > 0) { ... }
	if (remainingTokens <= 100000000*(10**18) && remainingTokens > 50000000*(10**18) ) { ... }
	if (remainingTokens <= 50000000*(10**18)) { ... }
```

- `_getTokenAmount` function has `view` modifier, but this function will change `remainingTokens` value.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L331#L418

## 3.7. Total Tokens Allocated for Crowdsale

### Severity: medium

### Description

`remainingTokens` state variable is set in the constructor for both `PreICOBitcademyGold` and `Crowdsale` to be `token.totalSupply()` which is wrong, its value has to be equal to the total tokens allocated for pre-ICO phase for `PreICOBitcademyGold` and the total allocated to the ICO phase for `Crowdsale` contract 

This issue will throw any token transfer if the approved value to be transferred by `PreICOBitcademyGold` or `Crowdsale` is lower than remainingTokens that will be sold. Of course, only if all approved tokens to be transferred by the contract are sold. 

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L161

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L111

### Recommendation

`token.allowance(tokenHolder,address(this))` can be used to set `remainingTokens` if tokens are approved to be spent by the contract before deployment.

## 3.8. Vesting Logic

### Severity: medium

### Description

`releasableAmount` and `vestedAmount` functions member of `BitcademyVesting` are not necessary, the amount to be withdrawn by the members is fixed to be `unreleased = ReleaseCap.div(noOfMembers.mul(Releases))`. The functions can be removed just by changing few lines in `release` function and adding a requirement where the member won't be able to withdraw only if `block.timestamp > cliff`.

The vesting logic allows each added `members[_member]` to withdraw 18 times max. Adding or removing members should not be allowed after that the members are able to withdraw, this will result in not enough of tokens to cover all the members withdrawals or the opposite where tokens will be stuck in the contract.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol#L77#L103

## 3.9. Min Investment on ICO Phase

### Severity: low

### Description

The minimum investment on ICO phase is set in the White Paper to be 100 USD, however in `Crowdsale` contract no minimum buy threshold is set. 

## 3.10. Known Issues of ERC20 Standard

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol#L5#L17

## 3.11. Token Supply

### Severity: Minor

### Description

The token contract total supply is fixed by the White Paper to be 1,000,000,000 , contract developers should consider hard coding its value, and not assigning it through the constructor input.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol#L10


## 3.12. Zero address checking required. 

### Severity: low

### Description

In [`constructor`](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/BitcademyToken.sol#L10) there are no checking for zero address.

### Recommendation

Add checking for zero address.
```solidity
require(_reserve != address(0));
```

## 3.13. Undefined variable as argument of `RefundVault` initialization.

### Severity: high

### Code snippet

* [Crowdsale.sol, line 153](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/Crowdsale.sol#L153)

* [PreICOBitcademyGold.sol, line 103](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/PreICOBitcademyGold.sol#L103)

### Description

`wallet` variable is not defined, but used here. The contract will not compiled.

### Recommendation

Use `multisig_wallet` variable instead `wallet`. Remove the mentions of the `wallet` variable.

### 3.14. It is possible for owner to adjust date of ICO close after the ICO finalization.

### Severity: low

### Code snippet

* [Crowdsale.sol, line 454](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/Crowdsale.sol#L454)

### Description

It is possible for owner to call `adjustCloseDate` function even if ICO will finalized. In this case, the ICO can be continued.

### Recommendation

Use `require(!isFinalized)` condition in `adjustCloseDate` function.

## 3.15. The `_forwardFunds` function does not take into `refundWeiAmt` value.

### Severity: low

### Code snippet

* [Crowdsale.sol, line 425](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/Crowdsale.sol#L425)

* [PreICOBitcademyGold.sol, line 313](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/PreICOBitcademyGold.sol#L313)

### Description

The `_forwardFunds` function used `msg.value` to deposit into Refund Vault. But before that is possible that some paid funds [will be refunded](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/Crowdsale.sol#L194). Thus, there will not be enough funds for the deposit to Refund Vault.

## 3.16. The `releaseAfterMainSale` function should can called only after finalization.

### Severity: low

### Code snippet

* [Crowdsale.sol, line 440](https://github.com/bitcademyfb/Bitcademy_ICO/blob/5fa73751972132af76786c1ea6bc9266b58e05b9/contracts/Crowdsale.sol#L440)

### Description

The `releaseAfterMainSale` function should can called only after finalization. But `release_date` may be changed.

### Recommendation

Add `require(isFinalized)` condition to `releaseAfterMainSale` function.


# 4. Conclusion

This contract contains critical issues, and is not safe to be deployed. The contract developers probably didn't do deep testing of the contracts functionality.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/199db5163f5512faa504831edb0d29be

https://gist.github.com/yuriy77k/e411250bd71ff33b0964722c55679c2c

https://gist.github.com/yuriy77k/4c2ecc833d9d7aa7b25141c8b3a5c306
