# UCOIN Security Audit Report

# 1. Summary

[UCOIN](https://www.ucoincorp.com) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol       : UCOIN
	Name         : Universal Coin
	Capped supply: 5,000,000,000
	Decimals     : 8 
	Standard     : ERC20

# 2. In scope

- [UCOIN_Smart_Contract.sol](https://github.com/ucoincurrency/UCOIN/blob/65f85924bbd88e4edca573ca6dcf4688cc1f0394/UCOIN_Smart_Contract.sol#L1) github commit hash 65f85924bbd88e4edca573ca6dcf4688cc1f0394.

# 3. Findings

In total, **9 issues** were reported including:

- 1 high severity issues.

- 2 medium severity issues.

- 3 low severity issues.

- 3 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Buy/Sell

### Severity: High

### Description

The buy and sell price is set by two variables that do not contain nominator and denominator information, meaning that for example the sell price set to a minimum will be 1 wei making the price of 1 token that is sold to the contract equal to 1 ether since the decimals are equal to 18.

Developers should be aware that this will not give them any flexibility to set the token sell and buy prices, meaning that the buy price for 1 token should be higher than 1 ether (please note that following this logic more than 5 billions ether are needed to buy all the tokens buy the investors).

### Code snippet 

https://github.com/ucoincurrency/UCOIN/blob/65f85924bbd88e4edca573ca6dcf4688cc1f0394/UCOIN_Smart_Contract.sol#L228-L245

## 3.3. ERC20 Compliance

### Severity: medium

### Description

`transfer` does not return a boolean as described in [EIP20](https://eips.ethereum.org/EIPS/eip-20) (please note that `transferFrom` is correctly implemented).

### Code snippet

https://github.com/ucoincurrency/UCOIN/blob/65f85924bbd88e4edca573ca6dcf4688cc1f0394/UCOIN_Smart_Contract.sol#L90#L93

## 3.4. Minting Overflow

### Severity: medium

### Description

Mint function does not use safeMath library or any other logic to prevent overflow.

### Code snippet

https://github.com/ucoincurrency/UCOIN/blob/master/UCOIN_Smart_Contract.sol#L211#L212

## 3.5. Transfer Event

### Severity: low

### Description

The transfer event is not emitted when allocating to total supply inside the constructor.
   
### Code snippet

https://github.com/ucoincurrency/UCOIN/blob/master/UCOIN_Smart_Contract.sol#L50#L59

## 3.6. Owner privileges

### Severity: owner privileges

### Description

1. Owner can mint unlimited amount of tokens, even if the total supply is already set in the constructor.

2. Owner can freeze/unfreeze users wallets without restrictions.

3. Owner can change the token buys/sell price at any moment.

### Code snippet

https://github.com/ucoincurrency/UCOIN/blob/master/UCOIN_Smart_Contract.sol#L210#L215

https://github.com/ucoincurrency/UCOIN/blob/master/UCOIN_Smart_Contract.sol#L220#L223

https://github.com/ucoincurrency/UCOIN/blob/master/UCOIN_Smart_Contract.sol#L228#L231

## 3.7. Zero address checking

### Severity: low

### Description

There is no zero address checking in function `transferOwnership`.

### Code snippet

https://github.com/ucoincurrency/UCOIN/blob/master/UCOIN_Smart_Contract.sol#L20

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/17e5525da7b4e33e0e475ab13469ce1a

https://gist.github.com/yuriy77k/207a417f8ec02ea21577eb7fef16d06d

https://gist.github.com/yuriy77k/ea60b757433f37354aa112c2cbf1b416

## 5.1. Notes about RideSolo report

[3.1. Frozen ETH](https://gist.github.com/yuriy77k/17e5525da7b4e33e0e475ab13469ce1a#31-frozen-eth) is not a high severity security issue. No need to withdraw contract balance it used for trading. To withdraw ETH, the owner can set token price in contract balance and sell it, as a last resort. it's owner privileges.
