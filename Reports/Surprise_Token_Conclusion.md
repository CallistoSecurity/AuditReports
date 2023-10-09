# Surprise Token Audit Report.

# 1. Summary

This document is a security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing), where [Surprise Token](https://github.com/Looder/airdrop/) has been reviewed. This contract makes airdrop tokens to first transaction to/from any address.

# 2. In scope

- [sps.sol](https://github.com/Looder/airdrop/blob/4108a0613091029ba6c159da5ddfbc27d0bcfcf0/sps#L1) github commit hash 4108a0613091029ba6c159da5ddfbc27d0bcfcf0.

# 3. Findings

**3 issues** were reported including:

- 1 high severity issues.

- 1 medium severity issues.

- 1 low severity issues.

## 3.1. SPS Airdrop

### Severity: medium

### Description

SPS contract airdrop `276` tokens to every single new address that makes a `transfer` or `transferFrom` transaction. 
The operation is done once for each address  without any limit on the total airdropped tokens except the totalsupply of SPS itself. This logic will push users to abuse the contract by creating multiple addresses if the tokens value is higher than the transactions gas consumption.

### Code snippet

https://github.com/RideSolo/airdrop/blob/master/sps#L77#L81

https://github.com/RideSolo/airdrop/blob/master/sps#L98#L102

## 3.2. The function getBalance may return wrong result.

### Severity: high

### Description

The [function getBalance](https://github.com/RideSolo/airdrop/blob/master/sps#L145) return fake balance amount if there were no transactions from the specified address. This conflict with ERC20 standard.

## 3.3. Known Issues of ERC20 Standard

### Severity: low

### Description

SPS token is ERC20 compatible, ERC20 Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

- Approve + transferFrom mechanism allows double Withdrawal attack.
- Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.

## 4. Conclusion

The audited contract contains issues with different severity level that have to be fixed before the deployment. This contract can not be used as ERC20 token due to non-compliance with the standard ERC20.

