# Nexo Security Audit Report

# 1. Summary

[Nexo](https://github.com/nexofinance/NEXO-Token/blob/master/contracts/NexoToken.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 3571169b3365adfc92c5bd743cc75b5184a2172a.

- [NexoToken.sol](https://github.com/nexofinance/NEXO-Token/blob/master/contracts/NexoToken.sol).

# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 1 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

## 3.2. Owner Privileges

### Severity: owner previliges

### Description

Owner allows himself to call `transferFrom` function from investors, community and advisers address, so there is risk to investors, that owner will transfer this tokens to another address.

### Code snippet

https://github.com/nexofinance/NEXO-Token/blob/master/contracts/NexoToken.sol#L103

## 3.3. Address is not correct.

### Severity: note

### Description

Don't forget to change addresses before deploy contract.

### Code snippet

https://github.com/nexofinance/NEXO-Token/blob/3571169b3365adfc92c5bd743cc75b5184a2172a/contracts/NexoToken.sol#L31

https://github.com/nexofinance/NEXO-Token/blob/3571169b3365adfc92c5bd743cc75b5184a2172a/contracts/NexoToken.sol#L42

https://github.com/nexofinance/NEXO-Token/blob/3571169b3365adfc92c5bd743cc75b5184a2172a/contracts/NexoToken.sol#L57

https://github.com/nexofinance/NEXO-Token/blob/3571169b3365adfc92c5bd743cc75b5184a2172a/contracts/NexoToken.sol#L75

https://github.com/nexofinance/NEXO-Token/blob/3571169b3365adfc92c5bd743cc75b5184a2172a/contracts/NexoToken.sol#L92

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/2bf5ef25e14b3c8fe974092f082e73ef

https://gist.github.com/yuriy77k/35cb280c011e56ae697b72d5dd0c379e

https://gist.github.com/yuriy77k/c8775b71c10309e21c343bd1400f965c