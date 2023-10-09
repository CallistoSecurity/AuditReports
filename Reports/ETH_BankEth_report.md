# BankEth Security Audit Report

# 1. Summary

[BankEth](https://rinkeby.etherscan.io/address/0xdAD71f838315f5802cBED49e7Db4a07014D74874#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> BankEth is an ERC20 which applies a tax on buy/sell transfers and sells that tax back into Eth. That Eth then becomes available to holders of the token which they can then claim through a dashboard.


# 2. In scope

https://rinkeby.etherscan.io/address/0xdAD71f838315f5802cBED49e7Db4a07014D74874#code

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **7 notes** were reported, including:

- 3 notes.

- 4 owner privileges.

No critical security issues were found.


## 3.1. Token transfer always failed in the `DividendPayingToken` contract

### Severity: note / high

### Description

In the function `_transfer()` (line 146 in `DividendPayingToken.sol`) there is `require(false);` which always revert token transferring.
Since `DividendPayingToken` contract is used in the `BankEthDividendTracker` where token transfer is not allowed (line 384 in `BankEth.sol`) it's not an issue. But in case you would like to reuse `DividendPayingToken` contract in other project it may cause an issue.

### Recommendation

The `require(false);` in the function `_transfer()` in `DividendPayingToken` contract is duplicate the same restriction in the `BankEthDividendTracker` contract and may be removed.


## 3.2. Address of `uniswapV2Pair` should has the same privileges ad `uniswapV2Router` address

### Severity: low

### Description

In the constructor of the `BankEth` contract, you allowed `uniswapV2Router` address to transfer before trading, but did not do the same with `uniswapV2Pair` address. But uniswap router just rout (forward) tokens to the appropriate pair. Therefore if you want to allow/disallow something to `uniswapV2Router`, you must do the same to `uniswapV2Pair` address.


## 3.3. Unused variable and event in the `BankEthDividendTracker` contract

### Severity: note

### Description

The `lastClaimTimes` (line 371 in `BankEth.sol`) mapping variable and event `Claim` (line 377 in `BankEth.sol`) is unused.



## 3.4. Transferring tokens between address that not an AMM pair is excluding from fees

### Severity: note / medium

### Description

In the function `_transfer()` member of `BankEth` contract there is condition when do not take fees (line 279 in `BankEth.sol`). But according this condition the fee may be taken only if sender or receiver of tokens is an AMM pair. 

Is it correct?


## 3.5. Owner privileges

### Severity: owner privileges

### Description

The `BankEth` contract owner has rights to:

1. Exclude any account from fees using functions `excludeFromFees()` (line 133 in `BankEth.sol`) and `excludeMultipleAccountsFromFees()` (line 140 in `BankEth.sol`).
2. Exclude any account from daily limit using function `excludeFromDailyLimit()` (line 165 in `BankEth.sol`).
3. Allow any account pre trading using function `allowPreTrading()` (line 170 in `BankEth.sol`).
4. Update dividend tracker contract using function `updateDividendTracker` (line 110 in `BankEth.sol`).


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit. Pay attention on notes.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
