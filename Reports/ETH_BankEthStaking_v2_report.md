# BankEthStaking v.2. Security Audit Report

# 1. Summary

[BankEthStaking](https://github.com/SNOSDEV/BankEthStaking/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope
Commit [5cd72a04c75ae00ce0b3632ca5b52394e45faa26](https://github.com/SNOSDEV/BankEthStaking/blob/5cd72a04c75ae00ce0b3632ca5b52394e45faa26/contracts/)

- BankEthStaking.sol
- Context.sol
- DividendPayingTokenInterface.sol
- DividendPayingTokenOptionalInterface.sol
- ERC20.sol
- IBankEth.sol
- IBankEthDividendTracker.sol
- IBankEthStaking.sol
- IERC20.sol
- IERC20Metadata.sol
- IUniswapV2Pair.sol
- IUniswapV2Router.sol
- Ownable.sol
- ReentrancyGuard.sol
- SafeMath.sol
- SafeMathInt.sol
- SafeMathUint.sol

## 2.1. Excluded

The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **2 notes** were reported, including:

- 0 notes.

- 2 owner privileges.

No critical security issues were found.

## 3.1. The owner privileges

### Severity: owner privileges

### Description

The owner of the `BankEthStaking` contract has right to:
- Add/remove tokens to/from contract 
- Add/remove tiers.

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
