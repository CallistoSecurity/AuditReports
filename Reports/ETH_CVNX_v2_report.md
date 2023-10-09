# CVNX Token and Governance V2 Security Audit Report

# 1. Summary

[CVNX Token and Governance V2](https://crypviser.network) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://crypviser.network
- https://t.me/crypviser_group
- https://www.twitter.com/crypviser

# 2. In scope

CVNX Token
https://etherscan.io/address/0xb2e7abc38b44a86be7965297b5a97e3b16453667#code

CVNXGovernance
https://etherscan.io/address/0x4faba8c745aff69110328cff817472ca03d0f65d#code

The same as provided in file `CVNXContract 2.0.zip`

Smart contracts: 
- CVNX.sol
- CVNXGovernance.sol
- ICVNX.sol
- ICVNXGovernance.sol

# 3. Findings

In total, **0 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **1 note** were reported including:

- 0 notes.

- 1 owner privileges.

No critical security issues were found.



## 3.1. Owner privileges

### Severity: owner privileges

### Description

The function `Swap()` allow users to swap CVN to CVNX tokens. But CVNX tokens initially belongs to the `owner` wallet, so if owner transfer tokens to another wallet swap will not work.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As the result it is recommended to implement a function that will allow to extract any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report in order to ensure the operability of the contract and prevent any issues which are not directly related to the code of this smart-contract.

