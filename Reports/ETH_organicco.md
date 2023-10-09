# 1. Summary

[Organicco](https://etherscan.io/address/0x910845fbbd3210d74a69bc1b39a3e6dcc9630f36#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Organicco.sol](https://gist.github.com/yuriy77k/8a52431c34bb8731ca6b5d2cacacd7f7)

# 3. Findings

In total, **6 issues** were reported including:

- 1 high severity issues.

- 1 medium severity issues.

- 3 low severity issues.

- 1 minor observation.

## 3.1. ERC20/ERC223 Compatibility

### Severity: High

### Description

ERC223 was made to avoid token transfer to contract that are not implemented to handle tokens, the original ERC223 provide a completely seamless implementation that is 100 percent backward compatible with ERC20. the Implemented token isn't ERC20 compliant neither ERC223.

Contract compatible with ERC20 will not be able to transfer tokens to other contract that support even ERC223 compatible, since there is two overloaded transfer function, any contract that should transfer tokens to another contract will have to call the overloaded transfer function with `bytes _data`, hence removing the backward compatibility of the original ERC223.

The ERC20/ERC223 implementation is wrong since there is two separate transfer function for address transfer and contract transfer. 

### Code snippet

https://gist.github.com/yuriy77k/8a52431c34bb8731ca6b5d2cacacd7f7#file-organicco-sol-L106

https://gist.github.com/yuriy77k/8a52431c34bb8731ca6b5d2cacacd7f7#file-organicco-sol-L118

## 3.2. Approve

### Severity: Medium

### Description

`approve` function do not allow the user to reset the previously approved value but add the new value to the old one.

Consequences can vary but token holders used to fully compliant ERC20 may allow more tokens instead of resetting the allowed value.

The function isn't ERC20 compliant.

### Code snippet

https://gist.github.com/yuriy77k/8a52431c34bb8731ca6b5d2cacacd7f7#file-organicco-sol-L154

## 3.3. Token Transfer to 0x0 address

### Severity: low

### Description

The implemented token allow transfer to 0x0 accidental tokens loss issue can be applied.

### Code snippet

https://gist.github.com/yuriy77k/8a52431c34bb8731ca6b5d2cacacd7f7#file-organicco-sol-L106

https://gist.github.com/yuriy77k/8a52431c34bb8731ca6b5d2cacacd7f7#file-organicco-sol-L140

## 3.4. ERC20 Compliance.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

## 3.6. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.0. It is suggested to use the latest version and fix all compiler warnings that arise.

# 4. Conclusion

The audited contract is not ERC20/ERC223 compatible and cannot be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/290cb130c42cff8d185e946bc33ca3fa

https://gist.github.com/yuriy77k/c41b84b87c9cb5afc56b7b2d1c8e3aed

https://gist.github.com/yuriy77k/84bb4d6f693a8f20fa7f93e10be281a9
