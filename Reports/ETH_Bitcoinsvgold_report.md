# Security Audit Report

# 1. Summary

[BITCOIN SVGOLD (BSVG) Token](https://etherscan.io/address/0x8013d06a86f341afab95f82f6487e44c4dc0c655#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : BSVG
	Name        : BITCOIN SVGOLD
	Total supply: 21,000,000
	Decimals    : 18
	Standard    : ERC20

# 2. In scope

- [bitcoinsvgold.sol](https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4)

# 3. Findings

In total, **6 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

- 1 minor observation.

## 3.1 Non-compliance with ERC-20 Token Standard.

### Severity: medium

### Description

ERC-20 Token [Standard specifies](https://eips.ethereum.org/EIPS/eip-20) for functions `transfer` and `transferFrom`:

The function SHOULD throw if the `_from` account balance does not have enough tokens to spend.
But in this implementation it just returns false. This can lead to serious consequences. Because checking the return value of this function is rare.

### Code snippet

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L53

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L65

## 3.2. ERC20 Compliance — zero-value transfers rejecting

### Severity: low

### Description

EIP20 says that:  Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.

    But in this contract, function transfer and transferFrom has a condition:

`if (balances[msg.sender] >= _value && _value > 0) {`

### Code snippet

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L48

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L59

## 3.3. Transfer Event

### Severity: low

### Description

Following ERC-20 specification, a transfer event should be fired after assigning the total supply to the `msg.sender` inside `BITCOINSVGOLD` constructor.

### Code snippet

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L101

## 3.4. Transfer to `address(0)`

### Severity: low

### Description

Transfer to `address(0)` is allowed when using  `transfer` or `transferFrom` functions. 

### Code snippet

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L43

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L56

### Recommendation

Add the following code to the `transfer` and `transferFrom` functions :
```
require( _to != address(0) );
```

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.6. Sale Function

### Severity: minor observation

### Description

The only sale function implemented within the token contract is the fallback function, investors should be aware of the risks when using such contract:

- No deadline is implemented for the crowdsale.
- No hardcap or a softcap is implemented as crowdsale goals.
- No fixed token amount is specified for the crowdsale neither for the dev team, everything is sold except if the amount are gone be set after deployment. 
- No ether wallet it implemented to lock user investments and pay them back if the ICO fails.

### Code snippet

https://gist.github.com/yuriy77k/4af14798bc70074a7f87a739e18ecbc4#file-bitcoinsvgold-sol-L110

# 4. Conclusion

The audited smart contract has some issues with ERC20 Compliance that could cause losing the money in a particular situation. We recommend fixing these issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/bfeaa758af8c7e22256d581161ba878f

https://gist.github.com/yuriy77k/9395e3624d5ced70d61fc5dc714e037e

https://gist.github.com/yuriy77k/a3fd55c4039d8371463d70daa8065b10
