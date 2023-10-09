# 1. Summary

[EthId Tokens](https://etherscan.io/address/0xe5374b3840f0782608f86e270ae389b8ee2024c4#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Smart contract manages tokens for sharing revenue from identity verification for Ethereum addresses provided by Cryptonomica.net

[Full description](https://docs.google.com/document/d/15dr27enz30M0qjsWms5BMMiOZ-S84aQ_Zwm8NfqEGZM/edit)

# 2. In scope

- [EthIdTokens.sol](https://gist.github.com/yuriy77k/f6b0d4bf2ff2c77a639061756c5bd43a).

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. Precision issue

### Severity: low

### Description

Solidity operates only with integers. Thus, variables multiplication should be done before the division in order to reduce the rounding errors.

### Code snippet

https://gist.github.com/yuriy77k/f6b0d4bf2ff2c77a639061756c5bd43a#file-ethidtokens-sol-L188

### Recommendation

We recommend changing the order of division and multiplication.

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

## 3.3. Contract accept payment from anyone.

### Severity: low

### Description

An anybody, who send Ether to contract address may lose it because of no payment processing in contract code.

### Code snippet

https://gist.github.com/yuriy77k/f6b0d4bf2ff2c77a639061756c5bd43a#file-ethidtokens-sol-L330-L333

### Recommendation

Use a specially created function for payment to contract instead of fallback function or use sender restriction.

For example:

```solidity
function() public payable {
    address payer = 0x846942953c3b2A898F10DF1e32763A823bf6b27f;
    require(msg.sender == payer);
}
```

# 4. Conclusion

There are no serious issues found, but some low severity issues need to be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c34a1a70407f080d8e1a1e08dca6b798

https://gist.github.com/yuriy77k/64bb2e2e17b3a7da48738cbbc43fde16

https://gist.github.com/yuriy77k/b1cb233155ec3fafd4c7c8715d6b888a
