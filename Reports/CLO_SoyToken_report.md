# Soy Token Security Audit Report

# 1. Summary

[Soy token](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol

# 3. Findings

In total, **5 issues** were reported, including:

- 1 high severity issues.

- 1 medium severity issues.

- 3 low severity issues.

In total, **4 notes** were reported, including:

- 3 notes.

- 1 owner privileges.


## 3.1. Lack of function `standard` - ERC223 incompatibility

### Severity: low

### Description

Due to ERC223 standard the [ERC223 tokens must explicitly return "erc223" on standard() function call](https://github.com/Dexaran/ERC223-token-standard/blob/e44d4c1f54033d9ca1a9f3749107da4ab70bad40/token/ERC223/ERC223.sol#L36-L42).
I the Soy token contract the function `standard()` is absent.

### Recommendation

Add to the [ERC223](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L305) contract: `string constant public standard = "erc223";`

## 3.2. Using the callback function before actually transferring the tokens TransferData

### Severity: high

### Description

Due to ERC223 standard the callback function [tokenReceived()](https://github.com/Dexaran/ERC223-token-standard/blob/e44d4c1f54033d9ca1a9f3749107da4ab70bad40/token/ERC223/ERC223.sol#L114-L118) must be called after transferring of tokens, but in the function [_transfer](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L499-L505) the callback calls before changing of balance. 

This will make the transfer inoperable when the recipient has to use tokens for subsequent transfers. For example, in a swap, when the received tokens should be immediately transferred to the pool. 

### Recommendation
Replace wrong code in the [_transfer](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L499-L505) function with:

```Solidity
        _balances[sender] = _balances[sender] - amount;
        _balances[recipient] = _balances[recipient] + amount;
        if(recipient.isContract()) {
            IERC223Recipient(recipient).tokenReceived(sender, amount, data);
        }
```

## 3.3. Two getters for owner's address

### Severity: note

### Description

The variable [_owner](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L628) has `public` visibility it means the compiler creates getter function `_owner()` to returns `_owner` variable value. But contract already has function [owner()](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L639-L641).

### Recommendation

To avoid duplication of functions with the same behavior declare `_owner` as `internal` instead of `public`.

## 3.4. Unused code

### Severity: note

### Description

[This part](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L168-L271) of the Address library is not used.

### Recommendation

Delete unused code to reduce cost on deployment.

## 3.5. Irrelevant comments. 

### Severity: note

### Description

This [comment](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L37) not relevant to the code.

Also in many places used `ERC20` instead of `ERC223`. 

## 3.6. Owner privileges

### Severity: owner privileges

### Description

Owner may [add/remove](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L734) addresses of minter who may mint Soy tokens.


## 3.7. No event emit os add/remove minter.

### Severity: low

### Description

The minter address stored in the [mapping](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L42) so there is no why to see who is the minter. We just can check some address is it minter or not.

The minter role is very important for token usage. Therefore event `SetMinter(address minter, bool status)` should be emitted when owner [add/remove](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L734) minter.

## 3.8. Minter can't be removed when debug mode disabled.

### Severity: medium

### Description

The owner can [add/remove](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L734) minter only in Debug mode. But once debug mode will be [disabled](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L739-L742) it can't be enabled any more. Therefore in case of compromising minter address there is no ability to remove it and set another minter.

## 3.9. No `TransferData` event emitted - ERC223 incompatibility

### Severity: low

### Description

Due to ERC223 standard the event [TransferData](https://github.com/Dexaran/ERC223-token-standard/blob/e44d4c1f54033d9ca1a9f3749107da4ab70bad40/token/ERC223/ERC223.sol#L120) should be emitted on token transfer.
However, in the function [_transfer](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L493-L507) the `TransferData` event does not emit.

### Recommendation

Add `emit TransferData(_data);` after [Transfer](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L506) event.

Add to the [interface IERC223](https://github.com/SoyFinance/smart-contracts/blob/ed3043a984d84e918bda502b86938d15afa21624/SoyTokenERC223.sol#L61-L133) the next code:

```Solidity
     /**
     * @dev Additional event that is fired on successful transfer and logs transfer metadata,
     *      this event is implemented to keep Transfer event compatible with ERC20.
     */
    event TransferData(bytes data);
```



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [x] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.



