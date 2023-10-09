# SmartSwap ETHBNB Security Audit Report

# 1. Summary

[SmartSwap ETHBNB](https://github.com/ezo-network/SmartSwap/tree/main/BNBETH) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The Smart Swap contract allows swapping ETH <> BNB and ERC20 <> BEP20 tokens by face value. It uses Oracle to get the price of tokens at the moment of swap.

# 2. In scope

Commit [f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead](https://github.com/ezo-network/SmartSwap/tree/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH)


- [SwapFactory.sol](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/SwapFactory.sol)
- [SwapPair.sol](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/SwapPair.sol)
- [Validator.sol](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/Validator.sol)
- [Ownable.sol](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/Ownable.sol)
- [SafeMath.sol](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/SafeMath.sol)
- [TransferHelper.sol](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/TransferHelper.sol)
 

# 3. Findings

In total, **4 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

- 4 owner privileges.

No critical security issues were found.

## 3.1. Owner privileges

### Severity: owner privileges

### Description

1. Owner can [change](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/SwapFactory.sol#L98) company fee.
2. Owner can [change](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/SwapFactory.sol#L131) factory contract to the new contract.
3. Owner can [change](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/SwapFactory.sol#L110) validator contract.
4. Owner can [change](https://github.com/ezo-network/SmartSwap/blob/f2b3d82c8ed0d61cfd99621fea8cca6a798c9ead/BNBETH/Validator.sol#L78) Oracle contract.

Since tokens swapping correctness completely relies on the `Validator` response and the `Validator` contract relies on the `Oracle` response the owner may change those contracts to make swapping unfair (or steal money).
The developer said that the voting contract will be used as an owner to reduce fraud risks and remove key ownership manipulation.

# 4. Conclusion

The audited smart contract can be deployed. No direct security issues were found during the audit. But the users have to take note of the owner's rights and have to trust the SmartSwap owner and `Oracle` that used in the SmartSwap contract. The developer said that the voting contract will be used as an owner to reduce fraud risks and remove key ownership manipulation.

# 5. Revealing audit reports

https://gist.github.com/MrCrambo/0e1e527980b6bc790ad3301fd56687da

