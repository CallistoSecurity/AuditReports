# Quarashi Network Security Audit Report

# 1. Summary

[Quarashi Network](https://etherscan.io/address/0x0aff88b4cf3015c9c17f1da1fccb88c632f3505e#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://quarashi.network
- https://t.me/quarashinetworkofficial
- https://twitter.com/QuarashiN/media

# 2. In scope

https://etherscan.io/address/0x0aff88b4cf3015c9c17f1da1fccb88c632f3505e#code

# 3. Findings

In total, **3 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 2 low severity issues.

- 0 notes.

- 1 owner privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. High gas consumption

### Severity: low

### Description

The functions `freezingCount()`, `getFreezing()`, `freezeTo()`, `releaseAll()` use lops of indefinite length and can use a lot of gas.

### Recommendation

Try to rebuild functions logic to avoid long loop in functions.

## 3.3. Owner privileges

### Severity: owner privileges

### Description

Contract owner has right:

1. Mint any amount of tokens to any address
2. Pause/unpause tokens transfer.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance the contract must halt deposits in case a large amount is being withdrawn in short period of time until the owner or the community of the contract approves further operationing.
- [ ] **Multisig owner account.**
- [ ] **Stnadard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As the result it is recommended to implement a function that will allow to extract any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report in order to ensure the operability of the contract and prevent any issues which are not directly related to the code of this smart-contract.

