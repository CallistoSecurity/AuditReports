# ETH-BSC Swap Security Audit Report

# 1. Summary

[ETH-BSC Swap](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing).


# 2. In scope

Commit [20ec6c502ba97cb530a54070d3b040ccdab76258](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/20ec6c502ba97cb530a54070d3b040ccdab76258/contracts/)

## 2.1. Excluded 

Folder [test](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/tree/master/contracts/test).

# 3. Findings

In total, **1 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

- 0 notes.

- 1 owner privileges.

## 3.1. The swapping process completely under the Owner's control.

### Severity: owner privileges

### Description

The owner can transfer tokens on the Ethereum side and mint them on the BSC side. 

However, Owner provides a transaction hash as proof of the user's swap operation. Therefore anyone can check each swap operation and ensure its correctness.

# 4. Conclusion

The audited smart contract can be deployed. Pointed issues were fixed.

Since the swapping process completely on the owner's power, users should understand the risk of an "exit scam".

# 5. Previous report revealing

https://gist.github.com/yuriy77k/326fde5902e91a2ea950a537b89258bd
