# CRAD CASH Security Audit Report

# 1. Summary

[CRAD CASH](https://etherscan.io/address/0x608f006b6813f97097372d0d31fb0f11d1ca3e4e#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : CRAD
	Name        : CRAD CASH
	Total supply: 100,000,000
	Decimals    : 18 
	Standard    : ERC20/ERC223

# 2. In scope

- [CRAD CASH](https://etherscan.io/address/0x608f006b6813f97097372d0d31fb0f11d1ca3e4e#code).

# 3. Findings

In total, **6 issues** were reported including:

- 2 medium severity issues.

- 3 low severity issues.

- 1 notes.

## 3.1. ERC223 & ERC20 Implementation

### Severity: medium

### Description

Implementing both ERC20 & ERC223 in the same contract like it is done in Crad Cash token does not make sense since the implementation still allow token transfer to contracts that may handle token transfers using `transfer(address _to, uint256 _value)`, when in a normal ERC223 implementation `transfer(address _to, uint256 _value)` will still call `transfer(address _to, uint _value, bytes memory _data)` by just adding and empty `_data` array.

This implementation does not prevent contracts and dapps or users to transfer tokens to contract since the most used function is `transfer(address _to, uint256 _value)` and not `transfer(address _to, uint256 _value, bytes memory _data)`. all related issues with ERC20 that ERC223 solves are still applicable.

### Code snippet

https://gist.github.com/RideSolo/ab90f1c3a8808fd1ab7f286d4152fc76#file-crad-sol-L94

https://gist.github.com/RideSolo/ab90f1c3a8808fd1ab7f286d4152fc76#file-crad-sol-L146

## 3.2. ERC223 Transfer

### Severity: medium

### Description

When calling `transfer(address _to, uint _value, bytes memory _data)` function, if  `_to` address is a contract `tokenFallback` is called before assigning the tokens to the contract balance, which will cause compatibiity issues since the ERC223 standard call `tokenFallback` after assigning the tokens to the contract address, check [here](https://github.com/Dexaran/ERC223-token-standard/blob/master/token/ERC223/ERC223_token.sol#L54#L70) for more details.

### Code snippet

https://gist.github.com/RideSolo/ab90f1c3a8808fd1ab7f286d4152fc76#file-crad-sol-L146

## 3.3. Transfer Event

### Severity: low

### Description

A transfer event should be triggered when initializing owner balance.

### Code snippet

https://gist.github.com/RideSolo/ab90f1c3a8808fd1ab7f286d4152fc76#file-crad-sol-L175#L179

## 3.4. Fallback Function

### Severity: note

### Description

Any ether that is sent through the fallback function to the contract is forwarded to the contract owner, developers should explain such logic.

### Code snippet

https://gist.github.com/RideSolo/ab90f1c3a8808fd1ab7f286d4152fc76#file-crad-sol-L180

## 3.5. Transfer to 0x0 Address

### Severity: low

### Description

`transfer(address _to, uint _value, bytes memory _data)` does not prevent from sending tokens to 0x0 address.

### Code snippet

https://gist.github.com/RideSolo/ab90f1c3a8808fd1ab7f286d4152fc76#file-crad-sol-L146

### Recommendation

Add a requirements to check if `_to` address is different then `address(0)`. 

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

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7cf31491fed613a2957689eac67abb6a

https://gist.github.com/yuriy77k/c95a37906e305d52097cde1b27542e35

https://gist.github.com/yuriy77k/6db564588bbe401a519289efac98ff24
