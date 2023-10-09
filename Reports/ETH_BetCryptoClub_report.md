# BetCrypto.Club Token (BCCT) Security Audit Report

# 1. Summary

[BetCrypto.Club Token (BCCT)](https://etherscan.io/address/0xd0864e40a0a5f8c988a8b247b4d8d48249306546#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://etherscan.io/address/0xd0864e40a0a5f8c988a8b247b4d8d48249306546#code

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

## 3.2. Unused code

### Severity: note

### Description

Internal _burnFrom function is not used in this contract.

### Code snippet

Line 248.

```js
         function _burnFrom(address account, uint256 amount) internal {
        _burn(account, amount);
        _approve(account, msg.sender, _allowances[account][msg.sender].sub(amount, "ERC20: burn amount exceeds allowance"));
        }    
``` 

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1aebc699f74dd43b154b4152a811bf91

https://gist.github.com/yuriy77k/ed9fd87832c4e1575aa7c95dd2bbe169

https://gist.github.com/yuriy77k/bbd91afaa8e80226690d89443388a9df
