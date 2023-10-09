# EZO Token V2 Security Audit Report

# 1. Summary

[EZO Token V2](https://github.com/ezo-network/ezo-token/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash ee918152d177f8dcee02a76431bdace3b5647878.

- [CurrrencyPrices.sol](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/CurrrencyPrices.sol).
- [EZOToken.sol](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol).
- [SmartSwap.sol](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol).
- [secureETH.sol](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/secureETH.sol).

# 3. Findings

In total, **16 issues** were reported including:

- 5 high severity issues.

- 4 medium severity issues.

- 2 low severity issues.

- 5 owner privileges.

## 3.1 SmartSwap & SecureETH mint

### Severity: high

### Description

Following the description provided by the team about the usage of `SecureETH` contract:

- "To allow users to freeze the value of their Ether instantly. So in volatile times, they hit freeze / secure contract and the value stays"

The implementation of `SecureETH` does not protect users from volatility in any way:

- Users that will deposit ether through `_sendEther` will most probably lose their ether, since the deposited ether is added to `tokenBalancesForEZO[address(0)]` but when withdrawing it using `_withdrawEther` requires the balance of the contract to be higher than `tokenBalancesForEZO[address(0)]`, except if the owner deposit ether through the fallback function.
- Another issues is that the ether can be taken by someone swapping tokens against ether as described in "Smart Swap Deposit" issue.
- In case of price drop, the owner is required to deposit even more ether to cover the price volatility.

### Code snippet

https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L327#L334

https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L336#L353

https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L362


## 3.2 Transfer Computation

### Severity: high

### Description

Both [returnAmount](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L260) and [sentAmount](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L265) calculation assumes that the `EZO` price is fixed to 100 USD, however [`CurrencyPrices(currencyPricesContract).currencyPrices(address(this))`](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L257) was used when computing [`_valueCal`](), all calculation should reflect the same price.

## 3.3 Smart Swap Deposit

### Severity: high

### Description

When using smart swap contract deposit through [sendEther](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L189) or [sendToken](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L193) an order is automatically added ([addOrder](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L212)) and fulfilled ([generalFundAssign](), [generalFundAssignEZO](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L294)), if the wanted currency balance in the smart-swap contract is higher than what the user is requesting and the deposited currency is ezo tokens.

A user that deposited tokens or ether previously might not be able to withdraw his deposit since it can be swapped with other users deposit that deposited ezo tokens even if his wanted currency and sent currency are different than ezo tokens, check [here](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L224)

Users that deposit tokens to swap them against EZO will be automatically accredited newly minted tokens to their account, the deposited tokens will be kept inside the contract, check [here](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L213#L216)

This logic need a balanced deposit between all tokens otherwise some users orders might not be fulfilled and and their deposit might be spent, blocking them from using [cancelOrder](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L205#L210).

## 3.4 Rate Conversion

### Severity: high

### Description

The conversion operation in [getCalValue](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L275) is wrong:
- It supposes that all the tokens have decimals equal to 18 (1 ether).
- The value to be converted is multiplied by a truncated value, resulting in wrong return value. Check recommendation for more details.

### Recommendation
```
	function getCalValue(uint256 returnCurrencyAmount, uint256 remainingAmountNewOrder,address _currencySent,address _currencyWant) internal view returns(uint256)
	{
        return safeDiv(
            safeMul(
                safeDiv(
                    safeMul(
                        safeSub(returnCurrencyAmount,remainingAmountNewOrder), 
                        10**CurrencyPrices(currencyPricesContract).currencyDecimal(_currencySent), 
                    10**CurrencyPrices(currencyPricesContract).currencyDecimal(_currencyWant), 
                CurrencyPrices(currencyPricesContract).currencyPrices(_currencySent)
                , 
            CurrencyPrices(currencyPricesContract).currencyPrices(_currencyWant)
        );
	}
```

## 3.5 SecureETH Gas Limit

### Severity: high

### Description

`burn` function implemented in `SecureETH` requires the user address to be whitelisted in `allowedForBurningTokens`. `allowedForBurningTokens` is an array and to check if a user is whitelisted `isAllowed` modifier iterates over the array, when a simple mapping can be used to get the value directly.

This issue will cause high gas consumption, until the contract will become unusable (following the array length).

### Code snippet

https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/secureETH.sol#L163#L182

## 3.6 EZO Deposit

### Severity: medium

### Description

When [cancelling](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L217) an order [generalFundAssign](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L224) is used to refund the user tokens or ether, if the deposited tokens through [sendToken](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L205) are EZOs, instead of sending the tokens back to the user using [assignTokens](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L317) with the `sender` address equal to the EZO contract address, the developers used `mint` function which will create new tokens. The deposited EZO tokens will be frozen inside the contract making the token supply higher, an attacker can repeatedly deposit/cancel to make the total supply higher just to hurt the project. Please note that no max cap is setting when minting.

## 3.7 Transfer Return Value

### Severity: medium

### Description

When `_uniqueId` is equal to `systemAddress` the return value of the `transfer` function should be set to true, since it is a valid transaction, however the function will return false. Developers must simply add a return statement [here](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L289#L292).

## 3.8 ERC 20 Compliance  

### Severity: medium

### Description

Depending on the intention of the developers, the [transfer](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L247) function is not ERC20 compatible and users won't be able to transfer EZO tokens following the normal ERC20 rules.
- First argument of the `transfer()` function must contain the recipient's address. In this realization first argument is order id.
- From the ERC20 standard: “Note Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event”. 

## 3.9 ERC20 Compliance — method missing

### Severity: medium

### Description

In the ERC-20 standard should be approve, transferFrom functions and event Approval, but here they are missing.

## 3.10 SecureETH ERC20 Compliance

### Severity: low

### Description

`SecureETH` contract inherit from IERC20 interface but do not implement the ERC-20 functions following the standard, the developers should define the usage of such contract to allow us to conclude the risk related with it.

### Code snippet

https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/secureETH.sol

## 3.11 Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.12 Owner Privileges

### Severity: owner privileges

### Description

Please note that most function marked with "onlyOwner" can either remove trust that the blockchain technology enforce between users and developers or can be hacked in case if the private key is stolen.

1. [`setCurrencyPriceUSD`](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/CurrrencyPrices.sol#L87#L93) allow the contract owner to set any currency value, instead decentralized oracle can be used such as "chainlink".
2. Owner can whitelist any address allowing it to [burn](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L306) and [mint](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L296) tokens from any address using [addAllowedAddress](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L183)
3. Owner can [updateTxStatus](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L239) and block the user fund for a specific transaction.
4. The owner can prohibit the transfer of tokens by [`halt()`](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/EZOToken.sol#L107) function.
5. Owner can change currency smart contract address and EZO token smart contract address at any time and without any restrictions [here](https://github.com/ridesoloAudit/ezo-token/blob/e1284e5f8dd773ae9973b0fad3244efac9180513/ezotoken/contracts/EZOToken.sol#L207), [here](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L166) and [here](https://github.com/ezo-network/ezo-token/blob/ee918152d177f8dcee02a76431bdace3b5647878/ezotoken/contracts/SmartSwap.sol#L180).


# 4. Conclusion

The audited contracts are unsafe and should not be deployed due to multiple high severity. User funds are at risk.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/93fa7a343dc8b7e3c05ed969de963a5a

https://gist.github.com/gorbunovperm/832a41de5bccfbb8e471e64fd102dd41

https://gist.github.com/MrCrambo/e49da6f45c403c2cb072499b65b08ac2

https://gist.github.com/danbogd/13b8cdefbdbefbcb19a94090cf249f68
