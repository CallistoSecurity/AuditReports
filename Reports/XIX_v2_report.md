# XIX-Token v2. Security Audit Report

# 1. Summary

[XIX-Token](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/tree/master/token/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/tree/master/token/contracts


# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issues.

- 0 low severity issues.

In total, **2 notes** were reported, including:

- 2 notes.



## 3.1. Assign to `newToken` the `_oldToken`

### Severity: medium

### Description

In the function `updateOldTokenAddress` you are assign the `_oldToken` value to [newToken](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/mock/NewTokenMock.sol#L40).

### Recommendation

It should be: `oldToken = IMigrableToken(_oldToken);`


## 3.2. Duplicate function

### Severity: note

### Description

The token contract already has function `balanceOf(address account)`, so no need to create duplicate function [balanceOfAccount](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/lib/token/Token.sol#L244-246) with the same functionality.

### Recommendation

1. Remove function [balanceOfAccount()](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/lib/token/Token.sol#L244-246).
2. Replace `balanceOfAccount()` calls with `balanceOf()`.

## 3.3. Gas wasting

### Severity: note

### Description

In the functions [migrateTrigger()](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/mock/NewTokenMock.sol#L53-56) and [migratePoolTrigger()](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/mock/NewTokenMock.sol#L71-74) you are calling `oldToken` contract twice. But you may modify function [migrateToNewToken](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/lib/token/Token.sol#L233-238) to returns user's balance, it will save gas for one cross contract calling.

### Recommendation

1. Update function [migrateToNewToken](https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/lib/token/Token.sol#L233-238) to:
```Solidity
    function migrateToNewToken(address user) external override returns(uint amount) {        
        require(msg.sender == newToken, "Can be called only by newToken contract.");

        amount = balanceOf(user);        
        _transfer(user, newToken, amount);
    }
```

2. Use `uint value = oldToken.migrateToNewToken(user);` instead of:

https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/mock/NewTokenMock.sol#L53-56

https://gitlab.gentlebit.cz/xixoio/smart-contracts/-/blob/master/token/contracts/mock/NewTokenMock.sol#L71-74

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

