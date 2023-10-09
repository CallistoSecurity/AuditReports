# Mermaid Finance Security Audit Report

# 1. Summary

[Mermaid Finance](https://github.com/MermaidFinance/MermaidToken/blob/main/contracts.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://mermaid.finance/
- https://twitter.com/mermaid_finance

# 2. In scope

Commit `8da46e13058b8319c6a394537e29e3c8fd5ef038`

[contracts.sol](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol)

# 3. Findings

In total, **5 issues** were reported, including:

- 0 high severity issues.

- 4 medium severity issues.

- 1 low severity issues.

In total, **8 notes** were reported, including:

- 3 notes.

- 5 owner privileges.


## 3.1. Possible incorrect message in `require` function.

### Severity: note

### Description

The [require(now > _lockTime , "Contract is locked until 7 days");](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L521) the message indicates a specific locking period, but function [function lock()](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L511) allow to lock to any period.

### Recommendation

Replace with message without specific locking period.


## 3.2. Declare `burnWallet` as a `constant`

### Severity: note

### Description

To reduce gas usage the [burnWallet](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L770) should be declared as `constant`.

## 3.3. Function includeInReward() waste a Gas and has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function [includeInReward()](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L915-L923) use the loop `for()` to find and remove address from the `_excludedFromReward` list. It requires a lot of Gas if many addresses are excluded. In the case of the long excluded list, the function will be aborted with `OUT_OF_GAS` exception.

### Recommendation

Use [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) instead of address array (line 204). So you could remove record using function [remove()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/243adff49ce1700e0ecb99fe522fb16cff1d1ddc/contracts/utils/structs/EnumerableSet.sol#L157).

## 3.4. Function _getCurrentSupply() has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function [_getCurrentSupply()](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1014-L1018) use the loop `for()`(lines 364-368) to calculate `reflectedSupply` and `tokenSupply` values. In the case of the big length of `_excludedFromReward` list, this function will be aborted with `OUT_OF_GAS` exception. Since this function used in most other functions, the performance of the entire contract will be broken.
The risk becomes a higher due Gas cost of storage reading that may grow in future node updates. For example, after applying the EIP-1884 the cost of [the SLOAD (0x54) operation changes from 200 to 800 gas](https://eips.ethereum.org/EIPS/eip-1884). And in [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929) discussing to increase the gas cost of `SLOAD (0x54)` to 2100.
This means that even if with current `_excludedFromReward` list length contract works now, it may stop works in the future when the cost of storage reading will change.

### Recommendation

1. Try to avoid long loops. 
2. Add limitation of `_excludedFromReward` list growing into function [excludeFromReward()](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L904):

```Solidity
require(_excludedFromReward.length < 50, "Excluded list too long");

```


## 3.5. The function `setMaxTxPercent` has 1 percent step

### Severity: low

### Description

By default the [_maxTxAmount](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L784) was set to 0.2% of total supply. Using function [setMaxTxPercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L966-L969) the owner can change it only with 1% step (i.e., 1%, 2%, 3%, etc.). Is not possible to set decimals of percent (i.e, 0.2%, 0.3%, 1.5%, etc.) 

### Recommendation

To use percentage with 1 decimal accuracy you have to divide to `1000` instead of [10**2](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L968). It allow to set 0.1% of total supply by passing `1` as `maxTxPercent` parameter. To set 1% of total supply the `maxTxPercent` should be set to `10`.

## 3.6. After transfer to burn address and charity wallet the fees may be restored to old value.

### Severity: medium

### Description

If owner changed `_taxFee` or `_liquidityFee`, using functions [setTaxFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L958), [setLiquidityFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L962) accordingly, the `_previousTaxFee` and `_previousLiquidityFee`will contain old values. 

Therefor when you [temporarily remove fees to transfer to burn address and charity wallet](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1202-L1211) the old values of fees will be restored.


## 3.7. Inappropriate function may be called to burn and transfer charity.

### Severity: medium

### Description

The function [_transferStandard](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1206-L1207) calls to transfer tokens to `dead` and `charity` addresses. But this function should be called when both `sender` and `receiver` is [not excluded](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1193-L1194) from reward.

We may assume that `dead` and `charity` addresses is not excluded from reward (but cant guarantee it), but the `sender` address may be excluded. So function `_transferFromExcluded` should be called in that case.

### Recommendation

Remove [this code](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1201-L12110) and integrate burn and charity fee processing into `_transferFromExcluded`, `_transferToExcluded`, `_transferStandard`, `_transferBothExcluded` functions like it was done with liquidity fee. 


## 3.8. Redundant Code

### Severity: note

### Description

The condition [!_isExcluded[sender] && !_isExcluded[recipient]](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1193-L1194) can be included in [else](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1197-L1198).

### Recommendation

The following [code](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L1193-L1194) can be removed:

```Solidity
        } else if (!_isExcluded[sender] && !_isExcluded[recipient]) {
            _transferStandard(sender, recipient, (amount.sub(burnAmt).sub(charityAmt)));
```


## 3.9. Owner privileges

### Severity: owner privileges

### Description

Contract's owner has right to:

1. Exclude/include any account from/in fee, using functions [excludeFromFee](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L937) and [includeInFee](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L941).
2. Exclude/include any account from/in rewards, using functions [excludeFromReward](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L904) and [includeFromReward](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L913).
3. Change fees percentage without limitation, using functions [setChartityFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L949), [setBurnFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L953), [setTaxFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L958), [setLiquidityFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L962).
4. Change maximal amount per transaction from 1 to 100 percents of total supply (with 1 percent step), using function [setMaxTxPercent](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L966).
5. Enable or disable adding liquidity to pool, using function [setSwapAndLiquifyEnabled](https://github.com/MermaidFinance/MermaidToken/blob/8da46e13058b8319c6a394537e29e3c8fd5ef038/contracts.sol#L972).



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
