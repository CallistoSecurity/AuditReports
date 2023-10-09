# Enduracoin token Security Audit Report

# 1. Summary

[Enduracoin token](https://github.com/Enduracoin/EnduracoinToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit `930514c1a657112403c7dc79317cac139da1449a`

- [EnduracoinToken.sol](https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinToken.sol)
- [EnduracoinValue.sol](https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol)


# 3. Findings

In total, **3 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issue.

- 2 low severity issues.

In total, **10 notes** were reported, including:

- 7 notes.

- 3 owner privileges.


## 3.1. `timestampOfLastVGAChange` never updates to the actual timestamp

### Severity: medium

### Description

The function `setDailyValueGainAdjustment()` should update `timestampOfLastVGAChange` accordingly `block.timestamp` but it just update it with the static value `timestampOfLastVGAChange = ((upTime * 86400) + startDate);`, where `upTime` and `startDate` are static and set on contract deployment. 

It will cause incorrect calculation of `currentValue`, because functions `updateValue()` and `getCurrentValue()` don't take into account real timestamp when `dailyValueGain` was updated.

### Code snippet

- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L27

### Recommendation

Use the following:

`timestampOfLastVGAChange = ((block.timestamp - startDate) / 86400) * 86400) + startDate;`

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.3. `getCurrentValue` may return the same value for the current and next day 

### Severity: low

### Description

In the function `getCurrentValue()` if `dailyValueGain` was changed today, it will return value `baseValue + dailyValueGain`, but on the next day, when `((block.timestamp - timestampOfLastVGAChange) / 86400)` = 1 it returns the same value `baseValue + dailyValueGain`.

### Code snippet

- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L52-L53

### Recommendation

This function should contain only the following:

`return convertToString(baseValue + dailyValueGain * ((block.timestamp - timestampOfLastVGAChange) / 86400));`


## 3.4. Owner privileges

### Severity: owner privileges

### Description

1. 50 Billion Enduracoin will be pre-minted to the owner's wallet. If tokens are burnt, the owner has the right to mint new tokens up to 50 Billion in total supply.
2. The owner has the right to pause/unpause Enduracoin token contract functions: `transfer()`, `transferFrom()`, `mint()`, `burn()`, `burnFrom()`. 
3. The owner can set any value in the `EnduracoinValue` contract. So the `currentValue` in the `EnduracoinValue` contract does not get a real market value of Enduracoin in a decentralized way.

## 3.5. Getter functions in `EnduracoinValue` contract with `onlyOwner` modifier 

### Severity: note

### Description

 The `view` functions `getCurrentValue()`, `getBaseValue()`, `getSafetyLock()`, `getTimeDiff()`, `getTimeStamp()`, `getUpTime()`, `getDailyValueGain()`, `gettimestampOfLastVGA()`, and `getDaysSinceLastVGA()` utilizes the modifier `onlyOwner` restricting the function to the owner, which does not help to keep values privately. Since these are view functions, they don't require the sender's signature to call them. Anybody can use the `owner's` address to call those functions. Moreover, the value of any variable (even private) can be read from the blockchain.

### Code snippet

- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinToken.sol#L58
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L51
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L56
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L95
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L99
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L103
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L107
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L111
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L115
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L119

### Recommendation

The modifier `onlyOwner` can safely be removed as the check is not necessary. Those functions don't change contract state.

## 3.6. The function burnFrom() can be optimized for unlimited approvals

### Severity: note

### Description

The function `burnFrom()` allows the owner to burn approved tokens of a wallet, the function rebases the allowance based on the amount of tokens to be burned. The function does not take into account unlimited approvals of a wallet where the rebase of approval would not be required.

#### Code Snippet
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinToken.sol#L41-L48

#### Recommendation

Consider adding the following check to the function `burnFrom()` to optimize the code for unlimited approvals.

``` Solidity
    function burnFrom(address account, uint256 amount) public virtual onlyOwner override {
        uint256 currentAllowance = allowance(account, _msgSender());
        require(currentAllowance >= amount, "ERROR: Burn amount exceeds allowance");
        if (currentAllowance != type(uint256).max) {
            unchecked {
                _approve(account, _msgSender(), currentAllowance - amount);
            }
        }
        _burn(account, amount);
    }
```

or, even better, to use function [_spendAllowance()](https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/%40openzeppelin/contracts%404.8.3/token/ERC20/ERC20.sol#L336-L348) which already contain required code.

``` Solidity
    function burnFrom(address account, uint256 amount) public virtual onlyOwner override {
        address spender = _msgSender();
        _spendAllowance(account, spender, amount);
        _burn(account, amount);
    }
```

## 3.7. The events `EnduracoinDailyValueGainChanged` and `EnduracoinBaseValueChanged` contain duplicated values

### Severity: note

### Description

The events `EnduracoinDailyValueGainChanged` and `EnduracoinBaseValueChanged` should contain previous and new value of changed variables.
But `_dailyValueGainAdjustment, dailyValueGain` is equal because event emits after updating of `dailyValueGain`. The same is for `_baseValueAdjustment, baseValue`.

Also event `EnduracoinBaseValueChanged` contain variable `timestampOfLastVGAChange` which does not change when function `setBaseValueAdjustment()` is called.

### Code snippet

- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L29
- https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L35

### Recommendation

Emit events `EnduracoinDailyValueGainChanged` and `EnduracoinBaseValueChanged` before `updateValue()` function call.

## 3.8. Contract with backend logic

### Severity: note

### Description

As there is no documentation for the contract and the backend logic, all of the contract's functionalities are only accessible by the privileged owner. It is unclear currently how the contract interacts with the users and the severity of the backend is unknown as it is not in the scope of the audit.


## 3.9. Consider using a fresh startDate

### Severity: note

Currently the code https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L12 uses 1 Jan 2023 as the start date
which is used to calculate `uptime` which is used to calculate `baseValue`. For meaningful results set the `startDate` to the date when the contract
will be deployed, or set it in the constructor on deployment.

## 3.10. Redundant Code

### Severity - note 

There are a couple of instances where a line of code can be omitted.

1. Double assignment of `dailyValueGain` here: https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L28

and here: https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L45

one of them can be omitted.

2. `onlyOwner` modifier in function `updateValue()` can be omitted, because it's internal function and called only from function `setBaseValueAdjustment()` and `setDailyValueGainAdjustment()` which already has `onlyOwner` modifier.

https://github.com/Enduracoin/EnduracoinToken/blob/930514c1a657112403c7dc79317cac139da1449a/EnduracoinValue.sol#L42


## 3.11. Follow good coding practice

### Severity: note

### Description

1. Unlocked Pragma

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.20, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

2. Missing docstrings

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

3. Missing test suite

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.

4. Functions not used internally could be marked as external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract.

5. Use scientific notation instead of using decimal representation for readability

Use scientific notation instead of using decimal representation for readability. For example, instead of `50000000000000000` may be used `50 * 1e9 * 1e6`.



# 4. Security practices

- [ ] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/chhajershrenik/55f018ea2313b3e9946c3e62184a187a
- https://gist.github.com/greywolf12/de2f1d18cef1025a7fb2cf5643807da3
- https://gist.github.com/SakshamGuruji3/8998cc3fda3eae4ef9fb0a5f318470ed
