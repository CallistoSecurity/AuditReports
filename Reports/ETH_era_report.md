# 1. Summary

[ERA Token](https://github.com/etharemit/ETHA) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [erc223Ownable.sol](https://github.com/etharemit/ETHA/blob/master/erc223Ownable.sol) github commit hash 6f81fa015f1a9fc6065f9ad1788835d4a3a55852.

# 3. Findings

In total, **1 issues** were reported including:

- 1 medium severity issues.

## 3.1. ERC223 Standard Compliance.

### Severity: medium

### Description

The reviewed token contract is not ERC223 fully compliant.

1. The function `transfer(address _to, uint _value, bytes _data)` call tokenFallback external function on the receiver contract before adding the value to balances[_to]. The original implementation adds the token value to the balance before making the external call (check the link below).
https://github.com/Dexaran/ERC223-token-standard/blob/master/token/ERC223/ERC223_token.sol#L63#L68

2. The function `transfer(address _to, uint256 _value)` didn't call tokenFallback external function on the receiver contract at all.

### Code snippet

https://github.com/etharemit/ETHA/blob/6f81fa015f1a9fc6065f9ad1788835d4a3a55852/erc223Ownable.sol#L198

https://github.com/etharemit/ETHA/blob/6f81fa015f1a9fc6065f9ad1788835d4a3a55852/erc223Ownable.sol#L142

### Recommendation

Use sample from [Dexarans' ERC223 token description](https://github.com/Dexaran/ERC223-token-standard).

# 4. Conclusion

Smart contract has medium severity issues, please fix it before deploying.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/894003aad43bc45d7a868769189f4f89

https://gist.github.com/yuriy77k/687fc19c9d8c7b251fff5b281559bcd3

https://gist.github.com/yuriy77k/1f183fc700224dbd2041a4c161634bef
