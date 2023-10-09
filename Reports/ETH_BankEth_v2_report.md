# BankEth v2 Security Audit Report

# 1. Summary

[BankEth](https://rinkeby.etherscan.io/address/0x9107b1c60549618fB9587DCc9ba6Ac33E275C058#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> BankEth is an ERC20 which applies a tax on buy/sell transfers and sells that tax back into Eth. That Eth then becomes available to holders of the token which they can then claim through a dashboard.


# 2. In scope

https://rinkeby.etherscan.io/address/0x9107b1c60549618fB9587DCc9ba6Ac33E275C058#code

# 3. Findings

In total, **2 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 2 low severity issues.

In total, **6 notes** were reported, including:

- 0 notes.

- 6 owner privileges.

No critical security issues were found.



## 3.1. Owner privileges

### Severity: owner privileges

### Description

The `BankEth` contract owner has rights to:

1. Exclude any account from fees using functions `excludeFromFees()` (line 141 in `BankEth.sol`) and `excludeMultipleAccountsFromFees()` (line 140 in `BankEth.sol`).
2. Exclude any account from daily limit using function `excludeFromDailyLimit()` (line 173 in `BankEth.sol`).
3. Allow any account pre trading using function `allowPreTrading()` (line 178 in `BankEth.sol`).
4. Update dividend tracker contract using function `updateDividendTracker` (line 118 in `BankEth.sol`).
5. Set maximum purchase amount using function `setMaxPurchaseAmount` (line 189 in `BankEth.sol`).
6. Transfer inactive users (who did not transfer tokens more than 12 weeks) dividends to `dividendTracker` contract using function `reinvestInactive` (line 213 in `BankEth.sol`).

## 3.2. FTPAntiBot contract not verified

### Severity: low

### Description

[FTPAntiBot](https://etherscan.io/address/0x590C2B20f7920A2D21eD32A21B616906b4209A43) deployed on ETH main net, and its code is not verified. This contract has the right to:
1. Disallow tokens transferring to/from AMM swap pools added to `automatedMarketMakerPairs` list (lines 288-292 in `BankEth.sol`).
2. Maybe block any transfers of `BankEth` tokens if function `antiBot.registerBlock(from, to);` will be reverted (line 347 in `BankEth.sol`).


These issues have low severity only due to the possibility of disabling `FTPAntiBot` usage.

### Recommendation

Use try/catch statement while call `antiBot.registerBlock(from, to);` (line 347 in `BankEth.sol`).


## 3.3. The variables `antibotEnabled` and `maxPurchaseEnabled` are private

### Severity: low

### Description

There is no easy way to check the current state of `antibotEnabled` and `maxPurchaseEnabled` variables.

To know the current state is crucial for `antibotEnabled` because function `toggleAntiBot()` (line 411 in `BankEth.sol`) toggle this variable state, and the owner could not know the current state.

### Recommendation

Declare variables `antibotEnabled` and `maxPurchaseEnabled` are `public` (lines 23-24 in `BankEth.sol`).


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
