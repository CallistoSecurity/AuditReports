# GNG ICO Security Audit Report V3

# 1. Summary

[GNG ICO](https://github.com/Dexaran/GnG_ICO/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `f50556c44a6971462d122b0494311398556f3502`

[ICO_vesting.sol](https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L663-L994)

# 3. Findings

In total, **1 issue** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issue.

In total, **7 notes** were reported, including:

- 2 notes.

- 5 owner privileges.


## 3.1. The minimal deposit value is set in 1 token

### Severity: note

### Description

Since users can pay with different tokens (with different prices), the [requirement](https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L822) to deposit at least 1 tokens puts them in an unequal position. Because users who will pay with ETH should deposit a minimum 1 ETH (about $1600), but users who pay with CLO can buy just for $0.003.

This requirement has no logical sense and should be removed because we already have a limitation of [minimum purchase](https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L845) amount.



### Code Snippets
- https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L822
- https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L776
- https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L861

### Recommendation

Remove the requirement to deposit at least 1 token/coin.

## 3.2. Use safeTransfer functions instead of ERC20/223

### Severity: low

### Description

Since GNG ICO contract transfers different third-party tokens, not all of them may strictly follow the ERC20 standard in `transfer` and `transferFrom` functions. So it's highly recommended to use `safeTransfer` and `safeTranferFrom` functions instead of them. For example, in the function [buy](https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L843) if `transferFrom` does not return any value transaction will fail, and the user can't participate in ICO. But if it returns `false` when tokens can't be transferred, the user will get GNG tokens for free because the returns value is not checked in this contract.

Also, in the function `ERC20Rescue`, it is better to use `safeTransfer()` to rescue non-standard ERC20 tokens. Although the function `delegateCall` can help rescue such tokens, it's required to deploy a particular contract with the correct `transfer` function (it will require more work).


### Code Snippets
- https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L843
- https://github.com/Dexaran/GnG_ICO/blob/f50556c44a6971462d122b0494311398556f3502/ICO_vesting.sol#L938

### Recommendation

Replace `transfer()` and `transferFrom()` for tokens transferring by `safeTransfer()` and `safeTransferFrom()`:
```solidity
    function safeTransfer(address token, address to, uint value) internal {
        // bytes4(keccak256(bytes('transfer(address,uint256)')));
        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(0xa9059cbb, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), 'TransferHelper: TRANSFER_FAILED');
    }

    function safeTransferFrom(address token, address from, address to, uint value) internal {
        // bytes4(keccak256(bytes('transferFrom(address,address,uint256)')));
        (bool success, bytes memory data) = token.call(abi.encodeWithSelector(0x23b872dd, from, to, value));
        require(success && (data.length == 0 || abi.decode(data, (bool))), 'TransferHelper: TRANSFER_FROM_FAILED');
    }
```

## 3.3. Owner privileges

### Severity: owner privileges

### Description


1. The function `modify_asset` allows the owner to modify the list of assets allowed to be traded in. The function is missing a check to ensure that the Token asset data for a certain id already exists, which could lead to overwriting an existing state.

Consider adding a boolean check to ensure that the modification is intentional and that overwriting existing assets in the list does not happen due to an error.

**Note: Consider using [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) from OpenZeppelin to manage and keep track of the asset list.**

```solidity
function modify_asset(uint256 _id, address _token_contract, string memory _name, bool _modify) external // onlyOwner
{
    require(msg.sender == owner() || msg.sender == admin, "ICO: asset access restriction error");
    // We are setting up the price for TOKEN that will be accepted as payment during ICO.
    require (_token_contract != address(0));

    if (asset_index[_token_contract] !=0)
        {
         require(_modify == true,  "Overwriting existing asset!");
        }

    assets[_id].contract_address = _token_contract;
    assets[_id].name = _name;
    asset_index[_token_contract] = _id;
}
```

2. `withdraw` allows the owner to withdraw native CLO from the contract before users have claimed all the GNG tokens.

3. `withdrawGNG` functions can be used to withdraw GNG tokens in the contract during the vesting period before the user can claim, resulting in losses for users. 
As per the developer notes, excess tokens must be burned after the ICO has ended (should not be withdrawn).
If this function intends to withdraw tokens that were not bought, it should be used a counter for tokens bought against the total deposited by the owner to get the number of tokens not bought.

4. The functions `setup_vesting()` and `setup_contract()` allow the contract owner to modify ICO contracts parameters which could lead to multiple incorrect computations leading to the user being unable to claim all GNG tokes purchased or additional tokens might be available for claim.

For instance, if `end_timestamp` is modified to extend the ICO period and the user has already made one or more claims and decides to purchase additional GNG tokens after the vesting period. The user would not be able to claim all the GNG tokens purchased.

In another instance where `vesting_periods_total` is modified user might be able to claim additional tokens if increased and lose the amount of GNG tokens if the value is decreased.

Consider adding checks to verify modifications to the ICO parameters by the functions `setup_vesting()` and `setup_contract()` are possible only before the ICO has started.

5. `delegateCall` function can be used at any moment to change any storage slot of the contract making it unusable, or do everything the owner wants.

### Recommendation

Since the owner has unlimited rights to do everything, the ownership must be transferred to a multi-sig contract.

## 3.4. Follow good coding practice

### Severity: note

### Description

#### 1. Functions, parameters, and variables in snake case.
Use camel case for all functions, parameters, and variables and snake case for constants.

#### 2. Functions not used internally could be marked external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract.

#### 3. Missing docstrings

Many functions in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security, but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be clearly documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

#### 4. Missing test suite.

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low-severity issues were found during the audit. 

Users should be aware of unlimited owner's rights that can destroy the entire ICO process.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

