# Heiswap Dapp Security Audit Report

# 1. Summary

[Heiswap Dapp](https://heiswap.exchange/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Heiswap (黑 swap) is an Ethereum transaction mixer that ultilizes parts of CryptoNote to enable zero-knowledge transactions.
> It ulitilizes Ring Signatures and pseudo-stealth addresses to achieve its zero-knowledge properties. The deployed smart contract handles the signature verification, while the client is responsible for generating the pseudo-stealth address.
> Ring signatures was only possible on the EVM (gas-wise) due to the recent addition of EIP198.

Description: https://kndrck.co/posts/introducing_heiswap/

Dapp: https://heiswap.exchange/

# 2. In scope

Commit hash: `afd1a2731bdcaea03698e29ee179b0b29b536807`

1. [Heiswap.sol](https://github.com/kendricktan/heiswap-dapp/blob/afd1a2731bdcaea03698e29ee179b0b29b536807/contracts/Heiswap.sol)
2. [AltBn128.sol](https://github.com/kendricktan/heiswap-dapp/blob/afd1a2731bdcaea03698e29ee179b0b29b536807/contracts/AltBn128.sol)
3. [LSAG.sol](https://github.com/kendricktan/heiswap-dapp/blob/afd1a2731bdcaea03698e29ee179b0b29b536807/contracts/LSAG.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 1 high severity issues.

- 2 medium severity issues.

- 1 low severity issues.

- 1 minor observation (not security issue)

## 3.1. Anyone can close a ring

### Severity: high

### Description

Confidentiality is based on the fact that there is no links between 5(maximum) deposits and 5 withdrawals. But the contract provides for the premature closure of the ring, even if the amount of participants is less than 5. The exposer can intentionally enter each ring together with the observed address and close the ring with two participants. Thus, he will be able to detect the withdrawing address.

To have only two participants in the ring, the exposer can track the transaction of the observed address and as soon as it appeared to make an Front-Running Attack closing the previous ring. Then the observed address will come into a new ring, which the exposer will be able to close with 2 participants.

### Code snippet

* [`forceCloseRing()`](https://github.com/kendricktan/heiswap-dapp/blob/afd1a2731bdcaea03698e29ee179b0b29b536807/contracts/Heiswap.sol#L187)

## 3.2. Losing the funds

### Severity: medium

### Description

When the deposit is not an exact value, rounding occurs. And a decimal fraction of deposit remains blocked in the contract. For example, if an amount of 2.7ETH is deposited , 0.7ETH will be lost.

### Code snippet

* [`deposit()`](https://github.com/kendricktan/heiswap-dapp/blob/afd1a2731bdcaea03698e29ee179b0b29b536807/contracts/Heiswap.sol#L63)

### Recommendation

Return the rest of the rounding.

## 3.3. Incorrect gas compensation

### Severity: medium

### Description

The contract provides for compensation of gas during the withdrawal of funds:
```solidity
    // Send ETH to receiver
    // Calculate fees (1.33%) + gasUsage fees
    uint256 gasUsed = startGas - gasleft();
    uint256 fees = (withdrawEther / 75) + gasUsed + startGas;
```

But `gasleft()` function returns just the amount of gas(not in `wei`). These calculations do not take into account the gas price. Therefore, the returned funds will be incomparably less than spent.

## 3.4. Zero address checking

### Severity: low

### Description

In function `withdraw` there is no zero address checking.

### Code snippet

[withdraw](https://github.com/kendricktan/heiswap-dapp/blob/master/contracts/Heiswap.sol#L109)

## 3.5. Unused variable

### Severity: minor observation 

### Description

`blocksPassed` variable is not used.

### Code snippet

* [blocksPassed](https://github.com/kendricktan/heiswap-dapp/blob/afd1a2731bdcaea03698e29ee179b0b29b536807/contracts/Heiswap.sol#L200)

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5ae25c2c0892dd524007429135c27c1a

https://gist.github.com/yuriy77k/573a83e8a78142ab86a0f9e6cbfea71d

https://gist.github.com/yuriy77k/3090239037c165b8fc2371e24ee509f5
