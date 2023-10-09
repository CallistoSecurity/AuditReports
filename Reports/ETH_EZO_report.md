# EZO Token Security Audit Report

# 1. Summary

[EZO Token](https://github.com/ezo-network/ezo-token/tree/master/ezotoken/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash e1284e5f8dd773ae9973b0fad3244efac9180513.

- [CurrrencyPrices.sol](https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/CurrrencyPrices.sol).
- [EZOToken.sol](https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol).
- [SmartSwap.sol](https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol).

# 3. Findings

In total, **25 issues** were reported including:

- 3 high severity issues.

- 6 medium severity issues.

- 5 low severity issues.

- 4 notes.

- 7 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).


## 3.1 Transfer

### Severity: high

### Description

When a user call [`transfer`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L264) function to take an order deposited through [`sendToken`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L222):

- The computed [`_valueCal`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L274) of EZO tokens to be sent to the order maker is wrong since the value should be equal to the amount of the currency sent multiplied by the currency price then divided by the price of EZO in usd. Multiple errors can be seen, 
    - [`safeDivv`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L44) is used instead of `safeDiv` which will divide the result of the multiplication by `1 ether` instead of the ezo price in USD.
    - Supposing if the division operation is correct the value used to divide is `100` but the price of EZO tokens can be changed by the owner using [`setEZOTokenPriceUSD`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L364), meaning that `100` should be replaced by `ezoTokenPriceUSD`.
- The [`condition`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L276) used to check if the value requested by the function caller is incorrect, the condition should be as follow ` if(_valueCal > _value) { /* do processing */}` to avoid extra computation if both values are equal.
- The [`returnAmount`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L279#L280) computation is incorrect it should be the difference in USD multiplied by the 10 power the currency decimals then divided by the currency price in USD. 
- [`sentAmount`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L285) should be the `_valueCal` in EZO multiplied by EZO token price in USD divided by the currency price in usd, while taking the decimals into account, node of the described steps where taken into account.


## 3.2 Smart Swap Deposit

### Severity: high

### Description

When using smart swap contract deposit through [`sendEther`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L193) or [`sendToken`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L197) an order is automatically added ([`addOrder`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L216)) and fulfilled ([`generalFundAssign`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L269), [`generalFundAssignEZO`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L284))if the wanted currency balance in the smartswap contract is higher than what the user is requesting and the deposited currency is ezo tokens, check [here]](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L220#L225).

A user that deposited tokens or ether previously might not be able to withdraw his deposit since it can be swapped with other users deposit that deposited ezo tokens even if his wanted currency and sent currency are different than ezo tokens, check [here](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L226#L227)

Users that deposit tokens to swap them against EZO will be automatically accredited newly minted tokens to their account, the deposited tokens will be kept inside the contract, check [here](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L217#L219)

This logic need a balanced deposit between all tokens otherwise some users orders might not be fulfilled and and their deposit might be spent, blocking them from using [`cancelOrder`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L209).

## 3.3. Unhandled condition
### Severity: high
### Description

In the function `checkStability` there is chance that the `returnCurrencyAmount` will be equal the  `remainingAmountNewOrder` but this condition is unhandled, so in this case the function will be revert. The function `generalFundAssign` that transfer funds will not work for this user.

### Code snippet

https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L244

## 3.4 EZO Deposit

### Severity: medium

### Description

When [cancelling](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L234) an order [`generalFundAssign`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L241) is used to refund the user tokens or ether, if the deposited tokens through [`sendToken`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L222) are EZOs, instead of sending the tokens back to the user using [`assignTokens`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L337) with the `sender` address equal to the EZO contract address, the developers used `mint` function which will create new tokens. The deposited EZO tokens will be frozen inside the contract making the token supply higher, an attacker can repeatedly deposit/cancel to make the total supply higher just to hurt the project. Please note that no max cap is setting when minting.


## 3.5 Rate Conversion & Multiplication Overflow

### Severity: medium

### Description

The following conversion operation might be wrong, since the token decimals are not taken into account directly inside the code, the token decimals value can be integrated with the price in usd however we cannot confirm (this issue should be confirmed with the developers)

- https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L219
- https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L221
- https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L242
- https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L266

## 3.6 Unset Tokens  

### Severity: medium

### Description

The owner is responsible of setting the tokens prices using [`setCurrencyPriceUSD`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/CurrrencyPrices.sol#L74) however it is not possible to set all the tokens that exist on the blockchain, meaning that the tokens addresses not set by the owner should not be allowed for deposit using [`sendToken`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L222). This will just constrain the users to cancel their order or give a bad user experience to other users if they want to take that order using [`transfer`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L264) since the transaction will just throw because of [dividing by zero]().

## 3.7 ERC 20 Compliance - transfer

### Severity: medium

### Description

Depending on the intention of the developers, the [transfer](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L264) function is not ERC20 compatible and users won't be able to transfer EZO tokens following the normal ERC20 rules.

### Recommendation

To create the uniqueId needed in the contract logic, the developers can use a hashing function with a pack of some unique variables that cannot be recreated twice and save the `value` and `sender` variables in a mapping.

## 3.8. ERC20 Compliance — method missing
### Severity: medium
### Description

In the ERC-20 standard should be approve, transferFrom functions and event Approval, but here they are missing.

### Code snippet
https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L8


## 3.9. Transfer prevents transfers of zero value
### Severity: medium
### Description

The transfer function in EZO is not ERC20 compliant as it stands. From the ERC20 standard: “Note Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event”. This function has a require statement that causes execution to revert if value is greater than zero. 

### Code snippet
https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L266

## 3.10. Missing event call.
### Severity: low
### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

### Code snippet
https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L197


## 3.11. Can not burn all tokens
### Severity: low
### Description

The burn function in EZOToken.sol will not let users burn all of their tokens because of the fourth require statement, which prevents users from decreasing their balance to zero by using the burn function.

### Code snippet
https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L328 

## 3.12 Price Setting

### Severity: low

### Description

[`setCurrencyPriceUSD`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/CurrrencyPrices.sol#L74) function role is to set the tokes reference value, `_currency` and `_price` arrays should be of equal length, a require must be added to check that condition.

## 3.13. Zero address checking

### Severity: low

### Description

In function [`addAllowedAddress`](https://github.com/ezo-network/ezo-token/blob/master/ezotoken/contracts/EZOToken.sol#L201) there is no zero address checking.

## 3.14. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

## 3.15 Naming Errors

### Severity: note

### Description

Naming on `SafeMath` library is leading to confusion when using `safeMull`, `safeMul`, `safeDiv` and `safeDivv` making the code readability more complex and prone to errors.

### Code snippet

https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L19#L49

https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L8#L38

## 3.16 Gas Consumption

### Severity: note

### Description

When depositing [ether](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L212) or [tokens](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L222) a new [`PurchaseData`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L225) contract is deployed making the transaction cost more expensive and not optimized.

## 3.17. Not correct comment
### Severity: note
### Description

Under burn function we see wrong comment "Function will create new tokens and assign to investor's address".

### Code snippet
https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L325

## 3.18. Unused mappings
### Severity: note
### Description

Mappings allowed and allowedToBurn are created but never used.

### Code snippet
https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L167-L170

## 3.19 Owner Privileges

### Severity: owner privileges

### Description

Please note that most function marked with "onlyOwner" can either remove trust that the blockchain technology enforce between users and developers or can be hacked in case if the private key is stolen.

1. [`setCurrencyPriceUSD`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/CurrrencyPrices.sol#L74) allow the contract owner to set any currency value, instead decentralized oracle can be used such as "chainlink".
2. Owner can whitelist any address allowing it to [`burn`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L326) tokens from any address.
3. Owner can whitelist any address allowing it to [`mint`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L316) tokens from any address using [`addAllowedAddress`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L201)
4. Owner can change EZO token price using [setEZOTokenPriceUSD](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L364) 
5. Owner can [`updateTxStatus`](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L256) and block the user fund for a specific transaction.
6. The owner can prohibit the transfer of tokens by [`halt()`](https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L123) function.
7. Owner can [change currency smart contract address](https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L207) and [EZO token smart contract address](https://github.com/ezo-network/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/SmartSwap.sol#L177) at any time and without any restrictions. 

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/6ecbd3224e5d808e40338746a252f2c9

https://gist.github.com/gorbunovperm/5305cb96a2d85e42b77db4c79b120de6

https://gist.github.com/MrCrambo/f9499804a579df9fb10611079e5f4776

https://gist.github.com/danbogd/bc4d72f3d20c7b87d83f56c42af91e35
