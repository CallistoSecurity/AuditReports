# BankEthStaking Security Audit Report

# 1. Summary

[BankEthStaking](https://github.com/SNOSDEV/BankEthStaking/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope
Commit [951bdc2635077c59e4693b7045d610e47a749ae9](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/)

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

In total, **3 issues** were reported, including:

- 0 high severity issues.

- 2 medium severity issues.

- 1 low severity issues.

In total, **3 notes** were reported, including:

- 3 notes.

- 0 owner privileges.


## 3.1. Unclear logic of function `removeStakingTokens()`

### Severity: note

### Description

Why `owner` has to [transfer](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L72) his token to contract for removing tokens from staking? I think, it's not correct.


## 3.2. Wrong condition

### Severity: medium

### Description

In the function `removeTier()` the index of array [compared](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L88) with `daysToStake` and then [assigned to element](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L89) last index. It's not correct.

It should be like this:
```Solidity
            if (tiers[i] == daysToStake) {
                tiers[i] = tiers[tiers.length - 1];
                tiers.pop();
                break;
            }
```

Also using the loop is not a good practice. Better to store array index in the `Tier` structure, and use it when need to get element from array.


## 3.3. A user may not receive bonus

### Severity: medium

### Description

If a user calls the function [stakeForEth](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L103-L107) with `_days` parameter that does not exist in the [rewardRates](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L154) he will get Zero bonus.

### Recommendation

Add `require(rewardRates[_days].active, "BankEthStaking: Tier is not populated");` to validate user's input.

## 3.4. Unclear logic of function `releaseEthStake()`

### Severity: note

### Description

In the function [releaseEthStake()](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L128-L140) users receive back their [contribution](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L138) without [bonus](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L137). What sense of such staking?

## 3.5. Unclear logic of function `pendingDividendOf()`

### Severity: note

### Description

Pay attention to [calculation](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L242-L243) in the function `pendingDividendOf()`. It looks incorrect in compare with function [pendingDividends](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L276-L288).

## 3.6. Inappropriate usage of `balanceOf`

### Severity: low

### Description

In the function `swapEthForTokens()` to get [tokenAmount](https://github.com/SNOSDEV/BankEthStaking/blob/951bdc2635077c59e4693b7045d610e47a749ae9/contracts/BankEthStaking.sol#L210-L219) checks `balanceOf(account)`. But here should be used `bankEth.balanceOf(account)`.



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
