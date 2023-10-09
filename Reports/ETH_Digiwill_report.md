# Digiwill Token Security Audit Report

# 1. Summary

[Digiwill Token](https://github.com/digiwills/Token/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> is an ERC20 Token with some additions, it has functions to lock and unlock token transfers, to block wallet
to send and receive tokens.

> Its main purpose is to pay for digiwills service, a service that aims to save sensitive data of users and send that data to successors once meet some conditions.

# 2. In scope

Commit hash: `e64c0a8f0244fc726f50f20e6323137efececf41`

1. [DigiwillToken.sol](https://github.com/digiwills/Token/blob/e64c0a8f0244fc726f50f20e6323137efececf41/DigiwillToken.sol)

# 3. Findings

In total, **3 issues** were reported including:

- 1 low severity issues.

- 1 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. No event call

### Severity: low

### Description

In function `constructor` there is no `Transfer` event call, that funds transferred to owner.

### Code snippet

[constructor](https://github.com/digiwills/Token/blob/master/DigiwillToken.sol#L107)

## 3.2. No event call

### Severity: note

### Description

In the function `burn` there is no event call.

### Code snippet

[burn](https://github.com/digiwills/Token/blob/master/DigiwillToken.sol#L203)

## 3.3. Blocking transferring

### Severity: owner privileges

### Description

It described in auditing request, but also should be presented in report.

The owner has the privilege to block the transfer of tokens. (`setTokenTransferLock()`, `setAddressTransferAllowance()` and `setAddressBlockState()` functions).

### Code snippet

[doTransfer()](https://github.com/digiwills/Token/blob/e64c0a8f0244fc726f50f20e6323137efececf41/DigiwillToken.sol#L152-L155)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/da57b6bf8cfbd89f131dde4abd874dd2

https://gist.github.com/yuriy77k/6a80d33a0bef5706d48eedf1530ace24

https://gist.github.com/yuriy77k/764bde274dca8f3ae8b709cfcb9bdd56
