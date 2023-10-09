# aXpire Token Security Audit Report

# 1. Summary

[aXpire Token](https://etherscan.io/address/0xC39E626A04C5971D770e319760D7926502975e47#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol      : AXPR
	Name        : aXpire
	Total supply: 350,000,000
	Decimals    : 18 
	Standard    : ERC20

# 2. In scope

- [aXpire Token](https://etherscan.io/address/0xC39E626A04C5971D770e319760D7926502975e47#code)

# 3. Findings

In total, **10 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

- 5 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. ERC-20 Compliance

### Severity: medium

### Description

Following [EIP-20](https://eips.ethereum.org/EIPS/eip-20) specifications:

- Transfers of 0 values "MUST" be treated as normal transfers and fire the Transfer event, this issues is applicable for both `transfer` and `transferFrom` since if `value` is equal to 0 the functions do not fire a Transfer event and return false.
- `transfer` "SHOULD" throw when the `msg.sender` doesn't have enough fund. 
- Same as previously following the specifications `transferFrom` should throw and not return false if the `_from` address doesn't have enough of fund or if the allowed value isn't enough to cover the transaction `_value`.

### Code snippet

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L155

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L166

## 3.2. Owner Privileges

### Severity: owner privileges

### Description

Contract owner allow himself to:
1.  Burn from any address, making all users at a critical severity risk, such behavior cannot be accepted by the investors. Once tokens are allocated to and address it belongs only to that address to burn the tokens, check [here](https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L223).
2. pause/unpause `approval/transfer/transferFrom`, check [here](https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L107).
3. halt/unhalt token sale, check [here](https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L325).
4. Ico can be ended by owner only, check [here](https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L348).
5. Reset the sale exchange rate at any moment, check [here](https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L321).

## 3.3. Allowance Approval

### Severity: low

### Description

Following ERC20 standard, `approve` function "Allows _spender to withdraw from your account multiple times, up to the _value amount. If this function is called again it overwrites the current allowance with _value.", However the implemented function throw in case if  `allowed[msg.sender][_spender]` is different than zero and `_value` different than zero. this partially solve double withdrawal attack but create incompatibility for some Dapps, and do not allow the user to directly reduce the allowance creating a race between user and spender.

### Code snippet

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L183

## 3.4. Transfer Event

### Severity: low

### Description

Following [EIP-20](https://eips.ethereum.org/EIPS/eip-20) when "A token contract which creates new tokens SHOULD trigger a Transfer event with the `_from` address set to 0x0 when tokens are created".

This issue issue is related with both constructor and `createTokens` function since tokens are created and transfer event is not triggered.

### Code snippet

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L277

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L317

## 3.5. Transfer to address(0)

### Severity: low

### Description

In both `transfer` and `transferFrom` transfers to `address(0)` are allowed.

### Code snippet

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L155

https://gist.github.com/RideSolo/65ca7446379c3d261ca9d2d2e1c7508a#file-axpire-sol-L166

## 3.6. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

# 4. Conclusion

The audited smart contract has issues with ERC20 Compliance and cannot be used as ERC20 token. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/d19fc5b0b1b91bd75b4a0d725ecf7c48

https://gist.github.com/yuriy77k/7fd07d4f6f1972916a6734f2c37b200c

https://gist.github.com/yuriy77k/d16d404a58342856e22fdd5e5976f776
