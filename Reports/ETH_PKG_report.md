# PKG Security Audit Report

# 1. Summary

[PKG](https://etherscan.io/address/0x02f2d4a04e6e01ace88bd2cd632875543b2ef577#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> PKG is a decentralized game platform operating on Blockchain, using the VR/AR technologies and machine learning tools to enhance the experience of playing games and purchasing digital goods in games. PKG Token (PKG) can be used to buy digital goods in different games.

# 2. In scope

* [PKG](https://etherscan.io/address/0x02f2d4a04e6e01ace88bd2cd632875543b2ef577#code)

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

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

## 3.2. ERC20 Compliance: `transfer` not returns

### Severity: low

### Code snippet

* Line 30.

### Description

According to ERC20 standard the `transfer` function should return bool value:

> `function transfer(address _to, uint256 _value) public returns (bool success)`

But this contract is not implemented this.

## 3.3. ERC20 Compliance: Event missing

### Severity: low

### Code snippet

* Line 107.

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a `Transfer` event should be emitted.
An event isn't emitted when assigning the initial supply to the `msg.sender`.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/79f51f64425f7a49b9263237aafe8e84

https://gist.github.com/yuriy77k/d8048bd64de3c7f724c2ef92c66130b8

https://gist.github.com/yuriy77k/aee7740d83a64a2959aae4838d6159c9
