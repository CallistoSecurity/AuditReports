# Cryptomillions.io V2 Security Audit Report

# 1. Summary

[Cryptomillions.io V2](https://cryptomillions.io) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol       : CPM-1
	Name         : Cryptomillions Series 1
	Capped supply: 600,000,000
	Decimals     : 8 
	Standard     : ERC20

# 2. In scope

- [ERC20_CPM1_Token_v2.sol](https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1_V2/) github commit hash f13d70bae67dc5570a7a95816609a36ada80877b.

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. Allowance Decrease

### Severity: low

### Description

The value to be substructed is decreased using safemath `sub` function meaning that if the value is higher than the remaining allowance the transaction will throw when it could be set to zero instead. multiple scenario can be imagined for the end user.

### Code snippet

https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1_V2/blob/master/ERC20_CPM1_Token_v2.sol#L120#L123

### Recommendation

Check if the allowance is lower than the value to be substracted and set the allowance to zero if the condition is met.

## 3.2. Extra requirement

### Severity: low

### Description

The following extra requirement is added to `_transfer`, `require(_to != address(msg.sender))`, the requirement might cause a compatibility issue especialy if there is any logic implemented in a contract like a batch payment where the msg.sender address will receive some tokens (please not that no reason comme to mind to add such requirement).

### Code snippet

https://github.com/cryptomillionsofficial/CREATE_ERC20_CPM1_V2/blob/master/ERC20_CPM1_Token_v2.sol#L103

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/025c8634e2e6a1ee7c6e0c7d667e3724

https://gist.github.com/yuriy77k/1fef9bf4d38fbbc5a618ac35a1177cd9

https://gist.github.com/yuriy77k/f32936670f6b6406540b9cdec5e10263
