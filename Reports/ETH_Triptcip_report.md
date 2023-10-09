# Triptcip Security Audit Report

# 1. Summary

[Triptcip](https://github.com/mikevercoelen/triptcip-contracts/blob/master/contracts/Triptcip.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit cf9ae100c3245f165c2b28fdb84ac7829d5cd49d
- [Triptcip.sol](https://github.com/mikevercoelen/triptcip-contracts/blob/cf9ae100c3245f165c2b28fdb84ac7829d5cd49d/contracts/Triptcip.sol)

## 2.1. Excluded

OpenZeppelin files was excluded from audit.

- @openzeppelin/contracts/access/Ownable.sol
- @openzeppelin/contracts/security/Pausable.sol
- @openzeppelin/contracts/security/ReentrancyGuard.sol
- @openzeppelin/contracts/token/ERC20/IERC20.sol
- @openzeppelin/contracts/token/ERC721/extensions/ERC721Burnable.sol
- @openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol
- @openzeppelin/contracts/utils/Counters.sol
- @openzeppelin/contracts/utils/cryptography/ECDSA.sol


# 3. Findings

In total, **1 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. Use local variable to reduce gas usage

### Severity: note

### Description

Ridding from storage use much more gas than reading from local variable. If you need to use storage value more then one time assign it to local variable and use it instead of loading storage. 
You have used [uint256 deadline = auction.deadline;](https://github.com/mikevercoelen/triptcip-contracts/blob/cf9ae100c3245f165c2b28fdb84ac7829d5cd49d/contracts/Triptcip.sol#L286) but still use `auction.deadline` in [this part](https://github.com/mikevercoelen/triptcip-contracts/blob/cf9ae100c3245f165c2b28fdb84ac7829d5cd49d/contracts/Triptcip.sol#L290-L297) of code.

### Recommendation

Use variable `deadline` instead of `auction.deadline` (where possible) like following:

```Solidity
    if (deadline == 0) {
      // Start the deadline, it's set to NOW + 24 hours in seconds (86400)
      // Deadline should prob be a contract level constant, or configurable here
      auction.deadline = block.timestamp + deadlineInSeconds;
    } else if (deadline - block.timestamp <= 900) {
      // If within 15 minutes of expiry, extend with another 15 minutes
      auction.deadline = deadline + 900; // TODO: move this to a contract level constant
    }
```


# 4. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

