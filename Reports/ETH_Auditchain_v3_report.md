#  Auditchain Security Audit Report

# 1. Summary

[Auditchain](https://github.com/Auditchain/Private-Sale/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://auditchain.finance/private-sale

# 2. In scope

Commit [54b18eb12fe79941bb90bd057ea847fe0264ad8f](https://github.com/Auditchain/Private-Sale/tree/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts)

- AuditToken.sol
- DAI.sol
- Locked.sol
- Migrations.sol 
- ReentrancyGuard.sol
- Sale.sol
- UniswapPriceOracle.sol
- Vesting.sol
- WhiteList.sol

# 3. Findings

In total, **7 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issues.

- 2 low severity issues.

- 5 notes.

- 6 owner privileges.

## 3.1. Known vulnerability of ERC-20 token

### Severity: low

### Description

The [DAI](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/DAI.sol#L6-L19) token contract has lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Wrong condition

### Severity: medium

### Description

In the modifier `isNotLocked` member of contract `Locked` the [condition](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Locked.sol#L30) hes opposite action as described in comment.
It allows any user to bypass checking `_from` and `_to` addresses is locked, but only if address `_from` has admin role the addresses will be checked.

### Recommendation

Add `!` (NOT) into condition:
`if (!hasRole(DEFAULT_ADMIN_ROLE, _from))`

## 3.3. Possible wrong arguments

### Severity: note

### Description

The function [burnFrom()](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/AuditToken.sol#L104) member of `AuditToken` contract checks `isNotLocked(msg.sender, msg.sender)` but by contract logic it seems to be `isNotLocked(user, user)`. Please, pay attention to it.

## 3.4. Owner privileges

### Severity: owner privileges

### Description

The contract owner has `DEFAULT_ADMIN_ROLE` and may assign any role to any wallets.

The role `CONTROLLER_ROLE` has following rights in [AuditToken](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/AuditToken.sol#L11), [Locked](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Locked.sol#L8), [WhiteList](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/WhiteList.sol#L12) contracts:
1. Can [lock and unlock](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Locked.sol#L40-L63) any address;
2. Can [mint](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/AuditToken.sol#L92) any amount of `Auditchain` tokens to any address; 
3. Can [pause and unpause](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/AuditToken.sol#L108-L114) `Auditchain` tokens operations;
4. Can [add and remove](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/WhiteList.sol#L39-L82) users from/to whitelist.

Admin (Operator) of [Vesting](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L11) contract has next rights:

1. [revoke()](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L135) / [reinstate()](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L147) access to continue vesting of tokens of any user.
2. [Add funds](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L120) to the early investor or team member.

## 3.5. Unused variable

### Severity: note

### Description

The [_operator](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Sale.sol#L26) declared but is unused.

### Recommendation

Remove unused variable.

## 3.6. Double-check permission

### Severity: note

### Description

The functions [revoke()](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L135-L137) and [reinstate()](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L147-L149) have `isOperator` modifier, but also it require the `msg.sender == admin`, that is the same as required in modifier. 

### Recommendation

Remove duplicated `require(msg.sender == admin)` from functions body.

## 3.7. Inaccurate condition

### Severity: note

### Description

In the function `release()` you [require](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L172) `releasedAmount <= tokensToSend`, but it should be `releasedAmount < tokensToSend`, because if `releasedAmount == tokensToSend` than all tokens claimed.


## 3.8. The keyword `emit` missed

### Severity: note

### Description

In the function `claimStake()` when emit event [StakingRewardsReleased](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L211) the keyword `emit` was missed.

## 3.9. An operator may add more tokens than vesting required.

### Severity: Low

### Description

An operator may accidentally call a function [fundVesting()](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L218) more than once and transfer more tokens than required for vesting. These tokens will be locked on contract forever.

### Recommendation

Add `require(!fundingCompleted)` on [begging](https://github.com/Auditchain/Private-Sale/blob/54b18eb12fe79941bb90bd057ea847fe0264ad8f/contracts/Vesting.sol#L219) of function.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Stnadard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report in order to ensure the operability of the contract and prevent any issues which are not directly related to the code of this smart-contract.
