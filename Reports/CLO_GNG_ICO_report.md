# GNG ICO Security Audit Report

# 1. Summary

[GNG ICO](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `09141248ecb121ec509927bba067d1b5db2c297e`

[ICO_vesting.sol](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L663-L989)

# 3. Findings

In total, **6 issues** were reported, including:

- 1 high severity issues.

- 3 medium severity issues.

- 2 low severity issues.

In total, **9 notes** were reported, including:

- 5 notes.

- 4 owner privileges.


## 3.1. User will receive 140% of bought GNG tokens

### Severity: high

### Description

User can access to extra tokens because the amount of tokens given on [_finalizePayment](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L728) are not deducted from the amount that is going to be given per period nor the `claims_count` is incremented (this case only apply when `instant_delivery` is equal to `vesting_period_percentage`).

Example (`_finalizePayment`):

`_reward_amount` = 10

`vesting_period_percentage` = 200 (this is the value in the contract)

`instant_delivery` = 200 (this is the value in the contract)

`vesting_periods_total` = 6 (this is the value in the contract)

Then...

`purchases[_buyer].amountGNG` = 10

`purchases[_buyer].amount_per_period` = 2

This line is executed:
`IERC223(GnGToken_address).transfer(_buyer, _reward_amount * instant_delivery / 1000);`

User balance is then 2 but it still have 6 periods to claim so 6\*2 = 12, +2 it will get 14 instead of 10 (user will receive 40% more than bought).

### Recommendation

Deduct instant delivery amount from `purchases[_buyer].amount_per_period`

```solidity
    function _finalisePayment(uint256 _reward_amount, address _buyer) internal
    {
        purchases[_buyer].amountGNG         += _reward_amount; // Increment by _reward_amount because it can be not the first purchase from this address.
        purchases[_buyer].vesting_timestamp = end_timestamp;

        uint256 _instant_delivery = _reward_amount * instant_delivery / 1000;
        purchases[_buyer].amount_per_period += (_reward_amount - _instant_delivery) / vesting_periods_total;

        IERC223(GnGToken_address).transfer(_buyer, _instant_delivery);
    }
```


Note: `vesting_period_percentage` now is not necessary and should be removed.

## 3.2. Function buy(), tokenReceived() and receive() oversells GNG tokens can lead to DoS

### Severity: medium

### Description

The function [receive](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L759), [tokenReceived()](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L829) and [buy](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L793-L798) are used to trade in Native CLO or tokens data with respect to the PriceFeed contract. When the user purchases the GNG tokens for the respective USD value of the Native CLO or tokens the functions take into consideration selling the GNG tokens for the respective available balance that is the contract should have at least 1 GNG token available for trading. X% of GNG tokens are delivered to the user after the payment is finalized, and another (100-X)% is available for the user to claim after the respective vesting period for the claim has been reached.

The functions do not take into consideration tokens already sold, allowing users to purchase tokens that should have been locked for previous purchases.

This would lead to a lack of liquidity of GNG tokens in the contract when users try to claim the tokens after the vesting period has ended.

### Code snippet

- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L770
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L805
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L734

### Recommendation

When allowing the user to trade in CLO or tokens add checks to take into consideration of previously purchased GNG tokens that had been locked for the users.

## 3.3. Function buy allows users to purchase tokens that are not part of the asset list.

### Severity: medium

### Description

The function buy allows the user to trade arbitrary tokens for GNG tokens that are not part of the asset list as per the developer notes only tokens on the asset list and native CLO is allowed to be traded in for the GNG tokens.

### Code snippet

- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L793-L818

### Recommendation

Add additional checks to ensure that the token used to trade is part of the asset list.

## 3.4. Divide before multiply might result in incorrect values

### Severity: medium

### Description

Here https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L861 we are performing division before multiplication and it is well known that due to solidity not being able to handle floating points, division before multiplication might result in incorrect values. Taking this formula `_reward_overflow / 10000 * tokenPricePer10000) / PriceFeed(priceFeed).getPrice(msg.sender) * 1e18;` we are performing the `division(_reward_overflow / 10000)` before multiplication.

## 3.5. Admin privileges.

### Severity: low

### Description

Admin has right to  call [setup_contract](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L961-L971) and [setup_vesting](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L973-L981) function anytime even after ICO started. 

In [comments](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L674-L675) is said that admin cannot access funds, but it's not true. Admin can:

Set [GnGToken_addres](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L964) to any token that is on contract (SOY, CLOE), set [tokenPricePer10000](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L970) to 1 and [instant_delivery](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L979) to any big number (i.e. 100000). It will allow him to withdraw any tokens from contract using function to buy tokens and pay very small amount.

Also he can change [priceFeed](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L968) to malicious contract and manipulate tokens price.

Since `admin` address is set to [msg.sender](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L674) (deployer address) and can't be changed it's a backdoor for deployer. Also, If `admin` wallet is compromised, hacker can steal all tokens from ICO contract.

### Recommendation

Remove `admin`. Owner can do initial setup before transferring ownership to multisig contract.

## 3.6. modify_asset function missing check which could lead to overwriting of existing asset id.

### Severity: low

### Description
The function modify_asset allows the owner/admin to manage the asset list used for accepting Tokens allowed to be traded in. The function is missing a check to ensure that the Token asset data for a certain id already exists. Which could lead to overwriting of an existing state.
Also, assets can be added with any ID number. For example: CLO can has id=1, SOY - id=5, CLOE - id=100. There is no why to check how many assets were added and with which ID.

### Code snippet

https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L924-L932

### Recommendation

Use [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) from OpenZeppelin.

or use this simple solution code:

```solidity
mapping(address => bool) public allowedTokens;

event SetAllowedToken(address token, bool isAllowed);

function setAllowedToken(address token, bool isAllowed) external onyOwner {
    allowedTokens[token] = isAllowed;
    emit SetAllowedToken(token, isAllowed);
}
```

So anybody can get list of allowed tokens just reading `SetAllowedToken` events.

## 3.7. Use safeERC20 functions instead of ERC20

### Severity: note

### Description

Since GNG ICO contract transfers different third-party tokens, not all of them may follow ERC20 standard in transfer and transferFrom functions. So it's highly recommended to use safeTransfer and safeTranferFrom functions instead of them. For example here https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L937 we rescue ERC20 tokens, and due to using transfer we would not be able to rescue tokens which are using non-standard transfer declaration(USDT) , same with transferFrom.

### Recommendation

Use Openzeppelin's [safeERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/utils/SafeERC20.sol) library's safeTransfer and safeTransferFrom function.

## 3.8. Unnecessary gas usage.

### Severity: note

### Description

1. Is not necessary to add a place holder in contractName variable.

   Actual code:

   ```solidity
   string  public contractName = "NOT INITIALIZED";
   ```

2. In the functions [receive](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L759-L791), [tokenReceived](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L829-L880) and [buy](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L793-L827) the `PriceFeed(priceFeed).getPrice(...)` function is called 3 times.

3. In the functions [receive](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L759-L791), [tokenReceived](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L829-L880) and [buy](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L793-L827) the `IERC223(GnGToken_address).balanceOf(address(this))` function is called 3 times.

### Recommendation

1. Remove place holder to save gas (about 27K) on deploy.

   ```solidity
   string  public contractName;
   ```

2. Set a variable for the price of the coin, call the function once a use the variable instead. This will save about 10K gas per each buy transaction.
```solidity
uint256 _price = PriceFeed(priceFeed).getPrice(...);
```

3. Set a variable for the price of the coin, call the function once a use the variable instead. This will save about 10K gas per each buy transaction.
```solidity
uint256 _balance = IERC223(GnGToken_address).balanceOf(address(this));
```


## 3.9. Owner privileges

### Severity: owner privileges

### Description

1. [withdrawGNG](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L956-L959) and [ERC20Rescue](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L935-L938) functions can be used to withdraw GNG tokens in the contract during vesting period before user can claim then resulting in losses for users. As per the developer notes excess tokens must be burned after the ICO has ended.

If the intention of this function is to withdraw token that were not bought, used instead a counter for tokens bought against total deposited by owner, to get the amount of tokens not bought.

2. [setup_contract](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L961-L971) and [setup_vesting](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L973-L981) function can be used after ICO started and could break completely the purpose of the ICO.

Add a require condition to block this functions after ICO is started.

3. [delegateCall](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L985-L988) function can be used at any moment to change any storage slot of the contract making it unusable or do everything owner wants.

4. [withdraw](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L941-L953) and [ERC20Rescue](https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L935-L938) function can be used to withdraw any tokens from contract.

For better security recommended to use separate address that will receive all payments. For example, `bank` multisig wallet. It will help to split tokens which users pay from tokens which were transferred to contract by mistake.

### Recommendation

Since owner has unlimited rights to do everything, the ownership must be transferred to multisig contract.


## 3.10. Follow good coding practice

### Severity: note

### Description

#### 1. FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE.
Use camel case for all functions, parameters and variables and snake case for constants

Affected instances:

- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L666-L669
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L677-L684 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L689-L691 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L714 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L720 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L910 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L917 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L924 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L961 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L973

#### 2. Functions not used internally could be marked external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract.

Affected Instances: 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L973 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L961 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L956 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L941 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L935 
- https://github.com/Dexaran/GnG_ICO/blob/09141248ecb121ec509927bba067d1b5db2c297e/ICO_vesting.sol#L737

#### 3. Missing docstrings

Many functions in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security, but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be clearly documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

#### 4. Missing test suite.

The contract is missing a test suite to validate and verify the behavior of the contract functionalities, it is recommended to add tests to ensure that the contract functions and behaves as expected.




# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

https://gist.github.com/ESNJS/2dbad96b3d9d0405cddeb69c7a6e2f8b

https://gist.github.com/SakshamGuruji3/b40caf5c9aac8e246119ea2a22800dbe

https://gist.github.com/chhajershrenik/7f5cb17deca10497ab2e5f7f437a3f8b

