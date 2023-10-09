# 1. Summary

[ERA Token](https://github.com/etharemit/ETHA/blob/master/erc223audited.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> A fixed number of tokens will be created during the token sale, and no more will be generated after that. The functionalities of the token are beyond a speculative asset on an exchange.

# 2. In scope

Commit hash: `b0dc80c845002d65d6291331049628d96da5e87e`

1. [erc223audited.sol](https://github.com/etharemit/ETHA/blob/b0dc80c845002d65d6291331049628d96da5e87e/erc223audited.sol)

# 3. Findings

In total, **1 issues** were reported including:

- 1 medium severity issues.

No critical security issues were found.

## 3.1. ERC223 standard compliance.

### Severity: medium

### Description

The function `transfer(address _to, uint256 _value)` didn't call `tokenFallback` external function on the receiver contract at all.

### Code snippet

* [erc223audited.sol](https://github.com/etharemit/ETHA/blob/b0dc80c845002d65d6291331049628d96da5e87e/erc223audited.sol#L152)

### Recommendation

Add a `tokenFallback` function call to `transfer(address _to, uint256 _value)` function. (Look at sample from [Dexarans' ERC223 token description](https://github.com/Dexaran/ERC223-token-standard).)

# 4. Conclusion

Smart contract has medium severity issue, please fix it before deploying.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/326961a7e1e89eec21c37a7b7e4bf37b

https://gist.github.com/yuriy77k/c8643e99c0da2f61472e5e4463921f66

https://gist.github.com/yuriy77k/3f10b682e92783cfb3352a0afccbbf4a
