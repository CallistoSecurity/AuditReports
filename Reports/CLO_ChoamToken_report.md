# CHOAM Token Security Audit Report

# 1. Summary

[CHOAM token](https://github.com/EthereumCommonwealth/CHOAM_token) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

CHOAM token must have the following features in addition to the basic ERC20/ERC223 standard implementations:

- The token can only be transferred between "whitelisted" addresses
- Token must grant the rights to claim dividends to it's holder based on "payment date", when payment date occurs (which is indicated by the function nextPaymentRoundSnapshot: https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L1004) users balances are recorded. After that a rigorous "calculation" on the side of the reward provider takes place off-chain and it takes a couple of days - then the reward is deposited and users become eligible to claim it https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L1011
- We don't care for how long the tokens were held. A user that acquired tokens 1 second before the snapshot will get a full amount of round reward for his quantity of tokens


# 2. In scope

- CHOAM token contract & revenue contract: https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol

- Whitelist contract: https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/Whitelist.sol

- STO contract: https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/STO.sol

## 2.1. Excluded

[Whitelist.sol](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/Whitelist.sol) and [STO.sol](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/STO.sol) contracts are excluded from the scope of the audit and only provided as a reference of logic that CHOAM contract is intended to interact with.

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **9 notes** were reported, including:

- 3 notes.

- 6 owner privileges.


## 3.1. Two getters for owner's address

### Severity: note

### Description

The variable [_owner](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L365) has `public` visibility it means the compiler creates getter function `_owner()` to returns `_owner` variable value. But contract already has function [owner()](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L380-L382).

### Recommendation

To avoid duplication of functions with the same behavior declare `_owner` as `internal` instead of `public`.

## 3.2. Unused code

### Severity: note

### Description

1. [This part](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L36-L139) of the Address library is not used.

2. The functions [safe32()](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L968-L971) and [getChainId](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L973-L977) are unused.

### Recommendation

Delete unused code to increase code readability and reduce deployment cost.


## 3.3. Owner privileges

### Severity: owner privileges

### Description

The owner of CHOAM token contract has right to:
1. Set and change the [whitelist](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L455) contract;
2. Set and change the [revenue](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L956-L959) contract;
3. Burn tokens from [blacklisted](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L961-L966) address;

The owner of Revenue contract has right to:

4. Set and change the (CHOAM) [token contract](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L999-L1002) address to work with.
5. [Make snapshot](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L1004-L1009) for payment round at any time;
6. [Deposit reward](https://github.com/EthereumCommonwealth/CHOAM_token/blob/37ac0a30129f298d401c9daef9f8394b48a5c778/ERC223ChoamToken.sol#L1011-L1016) for payment round. Reward may be added many times to the same round;


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [x] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract can be deployed. No severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.



