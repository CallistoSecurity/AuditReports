# Mermaid Finance v2 Security Audit Report

# 1. Summary

[Mermaid Finance](https://github.com/MermaidFinance/MermaidToken/blob/main/contracts.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://mermaid.finance/
- https://twitter.com/mermaid_finance

# 2. In scope

Commit `149ebb03ed65b358249f24956df5cb8fc34a65f0`

[contracts.sol]https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol)

# 3. Findings

In total, **3 issues** were reported, including:

- 0 high severity issues.

- 3 medium severity issues.

- 0 low severity issues.

In total, **8 notes** were reported, including:

- 2 notes.

- 5 owner privileges.


## 3.1. Possible incorrect message in `require` function.

### Severity: note

### Description

The [require(now > _lockTime , "Contract is locked until 7 days");](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L507) the message indicates a specific locking period, but function [function lock()](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L497) allow to lock to any period.

### Recommendation

Replace with message without specific locking period.


## 3.2. Use `constant` keyword for unchangeable variables

### Severity: note

### Description

To reduce gas usage the the unchangeable variables [_name, _symbol, _decimals](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L746-L748) should be declared with a `constant` keyword as it was in previous version.

## 3.3. Function includeInReward() waste a Gas and has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function [includeInReward()](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L901-L910) use the loop `for()` to find and remove address from the `_excludedFromReward` list. It requires a lot of Gas if many addresses are excluded. In the case of the long excluded list, the function will be aborted with `OUT_OF_GAS` exception.

### Recommendation

Use [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) instead of address array (line 204). So you could remove record using function [remove()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/243adff49ce1700e0ecb99fe522fb16cff1d1ddc/contracts/utils/structs/EnumerableSet.sol#L157).

## 3.4. Function _getCurrentSupply() has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function [_getCurrentSupply()](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L981) use the loop `for()`(lines 364-368) to calculate `reflectedSupply` and `tokenSupply` values. In the case of the big length of `_excludedFromReward` list, this function will be aborted with `OUT_OF_GAS` exception. Since this function used in most other functions, the performance of the entire contract will be broken.
The risk becomes a higher due Gas cost of storage reading that may grow in future node updates. For example, after applying the EIP-1884 the cost of [the SLOAD (0x54) operation changes from 200 to 800 gas](https://eips.ethereum.org/EIPS/eip-1884). And in [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929) discussing to increase the gas cost of `SLOAD (0x54)` to 2100.
This means that even if with current `_excludedFromReward` list length contract works now, it may stop works in the future when the cost of storage reading will change.

### Recommendation

1. Try to avoid long loops. 
2. Add limitation of `_excludedFromReward` list growing into function [excludeFromReward()](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L889):

```Solidity
require(_excludedFromReward.length < 50, "Excluded list too long");

```

## 3.5. Code optimization to reduce gas usage and risk of `OUT_OF_GAS` exception

### Severity: medium

### Description

Functions `_takeLiquidity` and `_takeBurn` to calculate [rLiquidity](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L994-L995) and [rBurn](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L1002-L1003) call function `_getRate()`  that calls function [_getCurrentSupply()](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L981) which use a lot of gas. However, `rLiquidity` and `rBurn` already calculated in function [_getRValues](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L970-L971) but does not used (function just returns `rAmount, rTransferAmount, rFee`).

Therefore, this issue increase risk of `OUT_OF_GAS` exception explained in [pt.3.4.](#34-function-_getcurrentsupply-has-a-risk-of-out_of_gas-exception) in three times.

### Recommendation

1. Rebuild function [_getRValues](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L973) to `return (rAmount, rTransferAmount, rFee, rLiquidity, rBurn);`
2. Rebuild function [_getValues](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L953-L957) accordingly to return `rLiquidity, rBurn`.
3. Add parameter `rLiquidity` to function `_takeLiquidity` and `rBurn` to function `_takeBurn` as below:

```Solidity
    function _takeLiquidity(uint256 tLiquidity, uint256 rLiquidity) private {
        _rOwned[address(this)] = _rOwned[address(this)].add(rLiquidity);
        if(_isExcluded[address(this)])
            _tOwned[address(this)] = _tOwned[address(this)].add(tLiquidity);
    }
    
    function _takeBurn(uint256 tBurn, uint256 rBurn) private {
        _rOwned[_burnWalletAddress] = _rOwned[_burnWalletAddress].add(rBurn);
        if(_isExcluded[_burnWalletAddress])
            _tOwned[_burnWalletAddress] = _tOwned[_burnWalletAddress].add(tBurn);
    }

```

## 3.6. Owner privileges

### Severity: owner privileges

### Description

Contract's owner has right to:

1. Exclude/include any account from/in fee, using functions [excludeFromFee](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L914) and [includeInFee](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L918).
2. Exclude/include any account from/in rewards, using functions [excludeFromReward](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L889) and [includeFromReward](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L901).
3. Change fees percentage without limitation, using functions [setBurnFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L922), [setTaxFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L926), [setLiquidityFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L930).
4. Change maximal amount per transaction (in percentage of `totalSupply`), using function [setMaxTxPercent](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L934).
5. Enable or disable adding liquidity to pool, using function [setSwapAndLiquifyEnabled](https://github.com/MermaidFinance/MermaidToken/blob/149ebb03ed65b358249f24956df5cb8fc34a65f0/contracts.sol#L940).



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
