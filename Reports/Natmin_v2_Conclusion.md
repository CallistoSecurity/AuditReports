# 1. Summary

[Natmin v2](https://github.com/natminpureescrow/token) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [NatminToken.sol](https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol) github commit hash a912bd64f25fdbc6420e2627cb3c20373002b7e7.

# 3. Findings

**2 issues** were reported including:

- 2 low severity issues.

## 3.1. Transfer Event

### Severity: low

### Description

In the constructor a token transfer should be emitted when assigning the `_totalSupply` to the `constractOwner` to comply with ERC20 requirement.

### Code snippet

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L116

### Recommendation

## 3.2. Known Issue of ERC20 Standard 

### Severity: low

### Description

This is just a reminder for the contract developers (the described ERC20 issue is well-known and well documented).

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

### Code snippet

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L231

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L231

# 4. Conclusion

The contract is safe to deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/76c8ff297165a8fe20925fe22ff221cd

https://gist.github.com/yuriy77k/0a923e3b3dbe8a174087d876b74a968d

https://gist.github.com/yuriy77k/5b58e9b2560c96f625634cd8da780e5c
