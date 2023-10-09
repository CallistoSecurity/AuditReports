# EthvaultENSRegistrar Security Audit Report

# 1. Summary

[EthvaultENSRegistrar](https://github.com/ethvault/ens-registrar-contract/blob/master/contracts/EthvaultENSRegistrar.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> EthvaultENSRegistrar is the contract that will be assigned ownership of the ethvault.xyz ENS domain.

> It allows a set of claimants (i.e. addresses) controlled by ethvault.xyz to distribute ethvault.xyz subdomains to user public keys not controlled by ethvault.xyz. As long as the registrar controls ethvault.xyz, it should not be possible for the user to lose control of the ENS subdomain via action by other users. The maximum risk allowed for losing a private key of a claimant is that additional domains may be registered.

https://ethvault.xyz

# 2. In scope

Commit hash: `d7e71dc8c2cf5f95214fc52329d8a8c922e8a25f`

* [EthvaultENSRegistrar.sol](https://github.com/ethvault/ens-registrar-contract/blob/d7e71dc8c2cf5f95214fc52329d8a8c922e8a25f/contracts/EthvaultENSRegistrar.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

## 3.1. Checking input addresses

### Severity: low

### Code snippet

* [register()](https://github.com/ethvault/ens-registrar-contract/blob/d7e71dc8c2cf5f95214fc52329d8a8c922e8a25f/contracts/EthvaultENSRegistrar.sol#L194)

### Description

Incoming addresses should be checked for an empty value(`0x0` address) to avoid loss of funds or blocking some functionality.

## 3.2. Any claimant can delete all others

### Severity: medium

### Description

Using function `removeClaimants` some claimant can remove all other claimants and the contract owner from claimants array.

### Code snippet

https://github.com/ethvault/ens-registrar-contract/blob/master/contracts/EthvaultENSRegistrar.sol#L47

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c9cc27397f0a3585c294601a48ef8794

https://gist.github.com/yuriy77k/de5aa1f64fa9f41254316f94ae8da36a

https://gist.github.com/yuriy77k/a567e5c080a3509edfb9cfc69b028b49
