# 1. Summary

[Bitcademy v.2](https://github.com/bitcademyfb/Bitcademy_ICO/tree/master/contracts) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BitcademyToken.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol) github commit hash 9ae1acf20ed619e0aab034bafe7d6b37dcbb82e9.
- [BitcademyVesting.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol) github commit hash 5204d8645cb2689702dc75ab9a806980b0effb5d.
- [Crowdsale.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol) github commit hash 962ea93ceadea47d4d3974ff63cc68d6bec3ef78.
- [PreICOBitcademyGold.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol) github commit hash 962ea93ceadea47d4d3974ff63cc68d6bec3ef78.
- [RefundVault.sol](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/RefundVault.sol) github commit hash 695932da33c7ac090a0d6593bdf372977722ac87.

# 3. Findings

In total, **13 issues** were reported including:

- 5 medium severity issues.

- 7 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. White Listing Logic

### Severity: medium

### Description

`addToWhitelist` or `addManyToWhitelist` functions should not reset the value of `tokenToClaim[_beneficiary]` to zero, the previous version of the project doesn't include this issue.

- There is no relation between whitelisting and `tokenToClaim`.
- If a user is white listed then removed, if he is whitelistd again the value of the tokens bought by him will be reset to zero.
- If any of above functions is called twice by the dapp and a user has bought token between the two calls, the user will lose his tokens.

Others scenario can be described related to this issue like social engineering against a big investor, etc ...

If the `tokenToClaim` of a user are not null, the user should be able to withdraw his ether back if set to null by the dapp.

This issue is applicable for both `Crowdsale` and `PreICOBitcademyGold` contracts.

### Code snippet
 
https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L223#L226

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L232#L237

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L228#L231

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L237#L242

## 3.2. Token Withdrawal

### Severity: low

### Description

`withdrawAfterMainSale` function allow the investors to withdraw the tokens that they bought even if the crowdsale has failed knowing that they will also be able to withdraw their funds.

Contract developers should prevent them from withdrawing tokens by adding `require(goalReached())`.

This issue is applicable for both `Crowdsale` and `PreICOBitcademyGold` contracts.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L488#L496

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L492#L500

## 3.3. User Whitelist Removal

### Severity: medium

### Description

A user that is whitelisted and buy tokens, if he is removed from the whitelist later, the same user will not be able to withdraw his tokens if the crowdsale succeed. developers should implement a mechanism to allow him to refund his invested ether.

This issue is applicable for both `Crowdsale` and `PreICOBitcademyGold` contracts.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L248#L250

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L243#L245

## 3.4. Token Conversion and Bonus Computation

### Severity: medium

### Description

The purpose of ```rate``` is not clear. In one place its ["No of wei needed for each token"](https://github.com/bitcademyfb/Bitcademy_ICO/blob/cbf41bbe4828db1435f0635867a824965b9ace7c/contracts/Crowdsale.sol#L31-L32), but in others ["No of tokens per ether"](https://github.com/bitcademyfb/Bitcademy_ICO/blob/cbf41bbe4828db1435f0635867a824965b9ace7c/contracts/Crowdsale.sol#L148) what is the reverse value.

To get the token amount (using `_getTokenAmount`) from the ether sent to `Crowdsale` contracts, `_weiAmount` is divided by `rate` that is updated every 3 hours by the project team using `setRate`.

The token decimals is set to 18 same as ETH decimals, and the price in USD of 1 token is set to be 0.038 USD At the moment of writing 1 ETH should provide the investors with ~5947 tokens without taking into account the bonuses, but since `_weiAmount` is divided and not multiplied by a certain rate the expected tokens will never be provided. For example if `rate` is set to the minimum value possible (rate = 1), then an investor that send 1 ETH will receive 1 token.

`_getTokenAmount` function member of `PreICOBitcademyGold` contract use the exact same `_getTokenAmount` function as `Crowdsale` contract, knowing that the presale should provide 60% bonus, than the implementation of the highlighted function is wrong. 

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L349#L464

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L344#L460


## 3.5. `_getTokenAmount` Logical Error

### Severity: medium

### Description

- `remainingTokens`,`noOfTokens` and `weiAmount` variables are updated only if one of the condition `if(tokensInCondition > remainingTokens.sub(X*(10**18)))` is true, an `else` should be added to all the condition to handle the opposite case `false` and update their values to the following:
	- `noOfTokens = noOfTokens.add(tokensInCondition)`.
	- `weiAmount = 0`.
	- `remainingTokens = remainingTokens.sub(tokensInCondition)`

- `remainingTokens` in every condition `if(tokensInCondition > remainingTokens.sub(X*(10**18)))` if it is true, should be set as follow:
	- `remainingTokens = remainingTokens.sub(tokensInCondition)`

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L349#L464

## 3.6. Allowance Arguments Swapped

### Severity: low

### Description

`allowance` function as defined by the ERC20 standard has two arguments, the first one is the tokens owners `_owner` and the second one is the tokens spender `_spender`, however the constructor contain the following line for both `Crowdsale` and `PreICOBitcademyGold` contracts:
```
    remainingTokens = token.allowance(address(this) , tokenHolder);
```
The addresses has to be swapped to get the right value since the spender is the contract address not the opposite.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L169

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L166

## 3.7. Local Variable misuse

### Severity: low

### Description

### Recommendation

`release` function member or `BitcademyVesting` contract uses `unreleased` local variable for multi purposes, at each call of the function `unreleased` is set with `_token.balanceOf(this)` the same amount is accumulated to `released` state variable at every call, however `released` state variable isn't use in the contract and do not create any direct impact.
`unrealease` local variable is again set with another value `ReleaseCap.div(noOfMembers.mul(Releases))`.
Contract developers should use different local variables for every action, especially when the naming will lead to confusion.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol#L82

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol#L90

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol#L91

## 3.8. Vault Fund Deposit

### Severity: low

### Description

`_forwardFunds` function defined on `Crowdsale` and `PreICOBitcademyGold` contract uses `refundWeiAmt` that was previously declared on `buyTokens` function for both contract raising an Undeclared identifier compiler error.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L465#L468

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L469#L472

### Recommendation

`_forwardFunds` function should be updated as following: 

```
  function _forwardFunds(uint _value) internal {
    vault.deposit.value(_value)(msg.sender);
    investors.push(msg.sender);
  }
```

and called as followed:

```
_forwardFunds(weiAmount);
```

## 3.9. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/BitcademyToken.sol#L5#L18

## 3.10. Token Approval

### Severity: Minor observation

### Description

The constructor contain the following line for both `Crowdsale` and `PreICOBitcademyGold` contracts:

```
    token.approve(address(this), supply_cap.mul(10**18));
```
The contract address is approving to itself  `supply_cap` value of tokens to be spent, this line doesn't add anything to the contract.
The approval of tokens to be spent by the contract for the ICO should be done by `tokenHolder` address.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/Crowdsale.sol#L168

https://github.com/RideSolo/Bitcademy_ICO/blob/master/contracts/PreICOBitcademyGold.sol#L165

## 3.11. The investor will receive total bonuses for 2 rounds if the number of tokens exceeds the remaining amount in this round.

### Severity: medium

### Description

If the number of tokens exceeds the remaining amount in this round then in next condition block will use previous price with bonus. And the bonus of coming round will be added to this price.

```solidity
if (remainingTokens < 300000000*(10**18) && remainingTokens >= 250000000*(10**18) && weiAmount > 0){
  currentRate = currentRate.mul(100);
  currentRate = currentRate.div(125); // plus a 25 percent bonus.
  // ...
}

if (remainingTokens < 250000000*(10**18) && remainingTokens >= 200000000*(10**18) && weiAmount > 0 ){
  currentRate = currentRate.mul(100);
  currentRate = currentRate.div(120); // plus a 20 percent bonus. 55 percent bonus at all.
  // ...
}
```

In this case investor will get 55 percent bonus instead 20.

### Code snippet

https://github.com/bitcademyfb/Bitcademy_ICO/blob/cbf41bbe4828db1435f0635867a824965b9ace7c/contracts/Crowdsale.sol#L359-L439

## 3.12. Only `tokenHolder` should deploy Crowdsale contract.

### Severity: low

### Description

`token.approve` call in the constructor means that `msg.sender` approve `supply_cap`-value of tokens to contract address. But given the line 305 - `token.transferFrom(tokenHolder,_beneficiary, _tokenAmount)`, we assume that all tokens are owned by `tokenHolder`. It turns out that only the `tokenHolder` can call `token.approve`.

### Code snippet

https://github.com/RideSolo/Bitcademy_ICO/blob/2abde9c365e5819e69c4af987bed79f46828bcd5/contracts/Crowdsale.sol#L152-L170

https://github.com/RideSolo/Bitcademy_ICO/blob/2abde9c365e5819e69c4af987bed79f46828bcd5/contracts/PreICOBitcademyGold.sol#L149-L167

### Recommendation

If you want to use `approve` on the constructor then only the `tokenHolder` can deploy the contract. And in this case `_tokenHolder` parameter of this function is excessive. Use `tokenHolder = msg.sender`.

## 3.13. Zero address checking required. 

### Severity: low

### Description

In [`addMember`](https://github.com/bitcademyfb/Bitcademy_ICO/blob/master/contracts/BitcademyVesting.sol#L62) there are no checking for zero address.

### Recommendation

Add checking for zero address.
```solidity
require(_reserve != address(0));
```

# 4. Conclusion

These contracts have some issues that should be fixed before deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/e4572e21259429080e3a02d05dd92ac5

https://gist.github.com/yuriy77k/595e6e15aa2b580111cc23f8db9977a0

https://gist.github.com/yuriy77k/4f5e00f2ad3e47664230dfd27c9a9a72

