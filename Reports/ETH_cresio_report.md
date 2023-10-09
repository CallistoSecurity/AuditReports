# 1. Summary

[Cresio](https://etherscan.io/address/0xdb2b3db44820526e7472dea06506b2e40506b3b9#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Cresio.sol](https://gist.github.com/yuriy77k/58be706ba8cbb2765d90dcbcc53505a2).

# 3. Findings

In total, **5 issues** were reported including:

- 3 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. The Crowdsale is possible only in manual mode.

### Severity: low

### Description

`isMinting` is false by default and there is no way to turn it to true. In this case tokens purchase in contract is not allowed.

### Code snippet

https://gist.github.com/yuriy77k/58be706ba8cbb2765d90dcbcc53505a2#file-cresio-sol-L37

https://gist.github.com/yuriy77k/58be706ba8cbb2765d90dcbcc53505a2#file-cresio-sol-L80

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```
require( _to != address(this) );
```

## 3.3. No checking for zero address.

### Severity: low

### Description

Transfer & transferFrom functions do not prevent from sending tokens to address 0x0.

### Recommendation
Add zero address checking

```
require(_to != address(0));

```

## 3.4. Check input value of `changeCrowdsaleRate`.

### Severity: minor observation (low)

### Description

There is necessary to check the input value of the `changeCrowdsaleRate` function for zero-value. In case of the argument is not specified, the user can pay money but not get tokens.

Since minting is disabled, this problem is not relevant, but needs to be resolved when re-using the contract.

### Code snippet

https://gist.github.com/yuriy77k/58be706ba8cbb2765d90dcbcc53505a2#file-cresio-sol-L98

## 3.5. Wrong `tokens` calculation

### Severity: minor observation (medium)

### Description

As this contract has `decimals` equal to 0, for right calculating of tokens from the ether, there should be multiplying to `RATE` before dividing to 10^18. Also, have to be check for the minimum payment amount.

Since minting is disabled, this problem is not relevant, but needs to be resolved when re-using the contract.

### Code snippet

https://gist.github.com/yuriy77k/58be706ba8cbb2765d90dcbcc53505a2#file-cresio-sol-L82

### Recommendation

1. Use `mul` operation first and then `div`.

2. Make condition with a minimum payment limit.

3. Use in **div** operation `1e18` instead of `100000000000000`.

# 4. Conclusion

The smart contract has no critical issues and can be used, but persons who would like to reuse it must pay attention to pointed issues.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7f7140f3cd1c7444b276001b79dcebb6

https://gist.github.com/yuriy77k/09e9a511d7fb4b664d9fae656231617b

https://gist.github.com/yuriy77k/3e250baf5e5fc69b568fd3478c4fe7b4
