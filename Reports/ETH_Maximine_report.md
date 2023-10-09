# Maximine Coin (MXM) Security Audit Report

# 1. Summary

[Maximine Coin (MXM)](https://maximine.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Audit Top 200 CoinMarketCap tokens.

> Maximine Coin (MXM)

# 2. In scope

1. [MXM.sol](https://etherscan.io/address/0x8e766f57f7d16ca50b4a0b90b88f6468a09b0439#code)

# 3. Findings

In total, **5 issues** were reported including:

- 4 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

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
## 3.2. ERC20 Compliance — event missing

### Severity: low

### Description

According to ERC20 standard when coins are minted(or burned) a `Transfer` event should be emitted.

### Code snippet

* Line 41, 163, 181.

## 3.3. ERC20 Compliance — transfer returns

### Severity: low

### Description

According [EIP20 Standard](https://eips.ethereum.org/EIPS/eip-20) the `transfer` function should returns boolean value `function transfer(address _to, uint256 _value) public returns (bool success)`. But here it is not implemented.

### Code snippet

* Line 76.

## 3.4. ERC20 Compliance: zero-value transfers rejecting

### Severity: low

### Description

EIP20 says that:
> Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.
But in this contract, function `transfer` has a condition:
```solidity
require(balanceOf[_to] + _value > balanceOf[_to]);
```

### Code snippet

* Line 56.

## 3.5. Owner Privileges

### Severity: owner privileges

### Description

The contract owner allowed to ban transfer functions for certain user.

### Code snippet

* Line 86, 77, 111, 128.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/47daa3e68f380c48e40ff1d101b83d64

https://gist.github.com/yuriy77k/d59b2a1e8b4801fff49b18b6456d7435

https://gist.github.com/yuriy77k/f5baefc0963cc674897d5f81413fa605
