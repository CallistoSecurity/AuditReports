# Security Audit Report

# 1. Summary

[MagicChain Token](https://github.com/magicchain/magicchain-blockchain/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : MAGI
	Name        : MagicChain
	Total supply: 42,000,000
	Decimals    : 6
	Standard    : ERC223

# 2. In scope

- [MagicChain223.sol](https://github.com/magicchain/magicchain-blockchain/blob/master/smartcontracts/MagicChain223.sol)

# 3. Findings

In total, **4 issues** were reported including:

- 2 medium severity issues.

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. ERC-223 Compliance

### Severity: medium

### Description

ERC-223 standard do not implement a `transferFrom` function, however the audited code contains a `transferFrom` function that share the same code (`_transfer`) as `transfer` function, knowing that many contract use `transferFrom` to handle approved token the audited implementation will cause backward compatibility issues with many deployed and probably future deployed contracts. 

For example if an address approve tokens to a contract address and the contract address try to call `transferFrom` to transfer the tokens to itself to be able to execute any logic and knowing that most contract do not implement `tokenFallback` function the transaction will throw.

ERC223 is intended to fix issues for direct transfer using `transfer` function only not `transferFrom`.

For more information check [here](https://github.com/ethereum/EIPs/issues/223).

### Code snippet

https://github.com/RideSolo/magicchain-blockchain/blob/master/smartcontracts/MagicChain223.sol#L178

https://github.com/RideSolo/magicchain-blockchain/blob/master/smartcontracts/MagicChain223.sol#L190#L213


## 3.2. Wrong Condition Value

### Severity: medium

### Description

`unfreezed` compute the unfreezed value of tokens since `_firstBlock`, however the condition in that function is used to limit the unfreezed token to a maximum of `_totalSupplyLimit` which is wrong since half of the total supply is assigned to the owner in the constructor. 
### code snippet

https://github.com/RideSolo/magicchain-blockchain/blob/master/smartcontracts/MagicChain223.sol#L217#L218

### Recommendation

The condition should limit the unfreezed token to half of `_totalSupplyLimit` otherwise the total supply will be equal to 63M not to 42M, when `totalSupply` function will return 42M.
Please note that `balanceOf` cold storage address will throw when `_coldStorageOut`will be higher than `_initialSupply`.

## 3.3. TransferFrom Reentrancy

### Severity: minor observation

### Description

`transferFrom` function calls `_transfer` where an external call is made using `tokenFallback`, if the `_to` address is a contract a reentrancy scheme is possible since `_approve` is resetting the value after the call to `_transfer` but with no impact since SafeMath will throw.

### code snippet

https://github.com/RideSolo/magicchain-blockchain/blob/master/smartcontracts/MagicChain223.sol#L178#L179

### Recommendation

Always check and set the variables before making any external call.

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

# 4. Conclusion

The audited contract contains compatibility issues with contracts that implement ERC20 approve and call mechanism, all the highlighted issues should be solved before deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/59b32c7b7b5ef9227b0aff0b6aac1bc3

https://gist.github.com/yuriy77k/ae8cfdfa852f586ebb4fc9160258cd32

https://gist.github.com/yuriy77k/915323b18ea8e6884b96f072e1285ed4
