# ETH-BSC Swap Security Audit Report

# 1. Summary

[ETH-BSC Swap](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing).


# 2. In scope

Commit [951fe2d39e873261a484f6fdbcdc11303a6c838d](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaBSCSwapAgentImpl.sol)

## 2.1. Excluded 

Folder [test](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/tree/master/contracts/test).

# 3. Findings

In total, **3 issues** were reported including:

- 0 high severity issues.

- 1 medium severity issues.

- 2 low severity issues.

- 0 notes.

- 1 owner privileges.

## 3.1. Possible indexes collision

### Severity: low

### Description

To create uniq index to get/set values in [contractWalletTokenIdTotalAmount](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaBSCSwapAgentImpl.sol#L264) is using sum of `token address + user address + token ID`. If token contract allow user to mint token with arbitrary token ID than will be easy to create collision.
I.e. User A has index `token address + user A address + token ID`, so user B can mint token with `token ID = user A address + token ID - user B address` and will receive the same index as user A.

This issue applicable for `OpenBiSeaBSCSwapAgentImpl` and `OpenBiSeaETHSwapAgentImpl` contracts in all places where `contractWalletTokenIdTotalAmount` is used.

### Recommendation

Better to use hash function to create index:
```Solidity
uint256 idx = uint256(keccak256(abi.encodePacked(tokenAddress, userAddress, tokenId)));
contractWalletTokenIdTotalAmount[idx] = contractWalletTokenIdTotalAmount[idx].sub(1);
```

## 3.2. Multisig wallets can't use swap functions

### Severity: low

### Description

The `notContract` modifier is used in [OpenBiSeaBSCSwapAgentImpl](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaBSCSwapAgentImpl.sol#L90-L94) and [OpenBiSeaETHSwapAgentImpl](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaETHSwapAgentImpl.sol#L52-L56) to protect swap functions from reentrancy attack. But it restrict users of multisig wallets to use swap functions. 

### Recommendation

Use [ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol) and replace `notContract` with `nonReentrant` modifier.

## 3.3. Users can't swap tokens from BSC to ETH if received it from other users

### Severity: medium

### Description

In [swapBSC2ETH](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaBSCSwapAgentImpl.sol#L207-L216), [swap721BSC2ETH](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaBSCSwapAgentImpl.sol#L255-L264), [swap1155BSC2ETH](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaBSCSwapAgentImpl.sol#L275-L284) of `OpenBiSeaBSCSwapAgentImpl` contract you require that user swap tokens from ETH to BSC before he will be able to swap it back (from BSC to ETH). But if user get this tokens from other user, he can't swap it back to ETH.

On the Ethereum side functions [fillBSC2ETHSwap](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaETHSwapAgentImpl.sol#L147), [fill721BSC2ETHSwap](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaETHSwapAgentImpl.sol#L160), [fill1155BSC2ETHSwap](https://github.com/oleksiivinogradov/eth-bsc-swap-contracts/blob/951fe2d39e873261a484f6fdbcdc11303a6c838d/contracts/OpenBiSeaETHSwapAgentImpl.sol#L174) will failed for user who did not swap it's token from ETH to BSC before.

### Recommendation

Instead of storing `contractWalletTokenIdTotalAmount[idx]` for separate users you may store there Total amount of swapped tokens: `contractWalletTokenIdTotalAmount[token_address]`.

## 3.4. The swapping process completely under the Owner's control.

### Severity: owner privileges

### Description

The owner can transfer tokens on the Ethereum side and mint them on the BSC side. 

However, Owner provides a transaction hash as proof of the user's swap operation. Therefore anyone can check each swap operation and ensure its correctness.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues (at least with medium severity) must be fixed prior to the usage of this contract. 

Since the swapping process completely on the owner's power, users should understand the risk of an "exit scam".
