# 1. Summary

[Jointer](https://ropsten.etherscan.io/address/0xc0141f4df3f0378e59c34ecb7c9fe2769d654567#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Mint tokens when new properties are tokenized and burn tokens when send back to the reserve.

http://www.jointer.io/

# 2. In scope

1. [JNTR.sol](https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol)

# 3. Findings

In total, **7 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

- 2 minor observation.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

### 3.2. Get wallet info of not allowed address.

### Severity: low

### Description

If the input value will be specified as unallowed address, then the result will be info about the owner's wallet (with index 0).

### Code snippet

* [Getting zero index of unallowed address](https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol-L83)
* [Setting index=0 for owner](https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol-L74)

### Recommendation

Use `(address => bool)` mapping for `allowedAddress`, for example.

## 3.3. The owner can reduce tokens on any address.

### Severity: medium

### Description

The owner can transfer tokens from any address to himself.

### Code snippet

* [Move tokens to owners address](https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol-L178)

## 3.4. Required check for an `0x0` address.

### Severity: low

### Description

Address in argument of these function are not checked for zero value. It is possible to lose tokens by sending tokens to `0x0` address.

### Code snippet

* [transfer](https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol-L110)
* [transferFrom](https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol-L125)

### Recommendation

Use condition like `require(_to != address(0))`.

## 3.5. ERC20 Compliance.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.
An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

https://gist.github.com/yuriy77k/f9d1c80f003cfbfa505e2ea8e8b8cb39#file-jntr-sol-L73

## 3.6. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.18. It is suggested to use the latest version and fix all compiler warnings that arise.

## 3.7. Discrepancy with the ERC20 standard

### Severity: minor observation

### Description

In Jointer.sol, according to the ERC20 standard, the variable decimals should be declared as uint8.

# 4. Conclusion

There is some vulnerabilities were found in this contract. You have to fix them before deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5db60495b910792ac9694f273360915a

https://gist.github.com/yuriy77k/079d9117954a6fc994623dd382d88797

https://gist.github.com/yuriy77k/72f600b6b1bcd73953f63589942ec817
