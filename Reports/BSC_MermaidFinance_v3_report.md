# Mermaid Finance v3 Security Audit Report

# 1. Summary

[Mermaid Finance](https://github.com/MermaidFinance/MermaidToken/blob/main/contracts.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://mermaid.finance/
- https://twitter.com/mermaid_finance

# 2. In scope

Commit `bd41a755a64eebc916707ca8880941d6466a6450`

[contracts.sol]https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol)

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issues.

- 0 low severity issues.

In total, **6 notes** were reported, including:

- 1 notes.

- 5 owner privileges.


## 3.1. The addresses can't be exclude from reward

### Severity: medium

### Description

In the function `excludeFromReward()` the [require(_excluded.length > 50)](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L892) will always revert transaction because on beginning `_excluded.length` less then 50.

### Recommendation

Replace [this line](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L892) with:
```Solidity
require(_excludedFromReward.length < 50, "Excluded list too long");

```

## 3.2. Function state mutability can be restricted to pure

### Severity: note

### Description

The functions [name](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L803), [symbol](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L807), [decimals](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L811) state mutability can be restricted to `pure`

### Recommendation

Use `pure` instead of `view` in those functions declaration.


## 3.3. Owner privileges

### Severity: owner privileges

### Description

Contract's owner has right to:

1. Exclude/include any account from/in fee, using functions [excludeFromFee](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L914) and [includeInFee](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L918).
2. Exclude/include any account from/in rewards, using functions [excludeFromReward](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L891) and [includeFromReward](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L901).
3. Change fees percentage without limitation, using functions [setBurnFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L922), [setTaxFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L926), [setLiquidityFeePercent](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L930).
4. Change maximal amount per transaction (in percentage of `totalSupply`), using function [setMaxTxPercent](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L934).
5. Enable or disable adding liquidity to pool, using function [setSwapAndLiquifyEnabled](https://github.com/MermaidFinance/MermaidToken/blob/bd41a755a64eebc916707ca8880941d6466a6450/contracts.sol#L940).



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
