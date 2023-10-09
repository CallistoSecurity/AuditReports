# GNG ICO Security Audit Report V2

# 1. Summary

[GNG ICO](https://github.com/Dexaran/GnG_ICO/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `b731e4ea341361eee4371913ae5a33a14b3d7e94`

[ICO_vesting.sol](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L663-L996)

# 3. Findings

In total, **7 issues** were reported, including:

- 0 high severity issues.

- 3 medium severity issues.

- 4 low severity issues.

In total, **9 notes** were reported, including:

- 4 notes.

- 5 owner privileges.


## 3.1. Users can claim all tokens in two vesting periods

### Severity: medium

### Description

Users can claim all tokens in two vesting periods independently on `vesting_periods_total` value.

Example.

Assume `end_timestamp` is some `day_0`, `vesting_period_duration = 30 days`. After user's purchase `purchases[_receiver].vesting_timestamp = day_0`.

Let's say the user claims tokens in 31 days, so, according [this calculation](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L748-L749) we will get: 
- `_num_periods = (day_31 - day_0) / 30 days = 1`, 
- `purchases[_receiver].vesting_timestamp = day_0 + 1 * 30 days = day_30`,
- the user claims tokens for 1 period.

In 30 days (61 days after `end_timestamp`) user claims again. So we will have:
- `_num_periods = (day_61 - day_30) / 30 days = 1`, 
- `purchases[_receiver].vesting_timestamp = day_0 + 1 * 30 days = day_30`,
- the user claims tokens for 1 period.

Now user claim can instantly repeat claiming without waiting until receive all his tokens. On each claim request we will get the same values:
- `_num_periods = (day_61 - day_30) / 30 days = 1`, 
- `purchases[_receiver].vesting_timestamp = day_0 + 1 * 30 days = day_30`,
- the user claims tokens for 1 period.

### Code snippet
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L748-L749

### Recommendation

Use [here](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L749):
```solidity
        purchases[_receiver].vesting_timestamp += (_num_periods * vesting_period_duration);
```

## 3.2 Functions `setup_contract` and `setup_vesting` allows the owner to change ICO parameters which could lead to loss or additional gain of GNG tokens purchased by the user

### Severity: medium

### Description

The functions `setup_vesting()` and `setup_contract()` allows the owner of the contract to modify ICO contracts parameters which could lead to multiple incorrect computations leading to the user being unable to claim all GNG tokes purchased or additional tokens might be available for claim.

For instance, if `end_timestamp` is modified to extend the ICO period and the user has already made one or more claims and decides to purchase additional GNG tokens, after the vesting period. The user would not be able to claim all the GNG tokens purchased.

In another instance where `vesting_periods_total` is a modified user might be able to claim additional tokens if increased and lose the amount of GNG tokens if the value is decreased.

### Code Snippets

- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L972-L981
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L983-L988

### Recommendation

Consider adding checks to verify modifications to the ICO parameters by the functions `setup_vesting()` and `setup_contract()` are possible only before the ICO has started.

## 3.3. Divide before multiply might result in incorrect values

### Severity: medium

### Description

In the functions `receive()`, `buy()`, `tokenReceived()` we are performing division before multiplication and it is well known that due to solidity not being able to handle floating points, division before multiplication might result in incorrect values. 

For example, according this formula `_reward_overflow / 10000 * tokenPricePer10000) / PriceFeed(priceFeed).getPrice(msg.sender) * 1e18;` user can lost his tokens in amount below of 1 token. For example, if user pays with ETH he can lost amount which is less than 1 ETH.


### Code Snippets

- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L789
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L831
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L877

- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L780
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L819
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L868

### Recommendation

Modify formulas to use multiplication before division.



## 3.4. modify_asset function missing check which could lead to overwriting of existing asset id.

### Severity: low

### Description

The function `modify_asset` allows the owner to modify the asset list used for accepting Tokens allowed to be traded in. The function is missing a check to ensure that the Token asset data for a certain id already exists. Which could lead to overwriting of an existing state.


### Code snippet

- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L937-L943

### Recommendation
Consider adding a boolean check to ensure that the modification is intentional and that overwriting of existing assets in the list does not happen due to an error.

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


## 3.5. Missing zero address check

### Severity: low

### Description

In several places in the code, addresses are passed as parameters to functions. In many of these instances, the functions do not validate that the passed address is not the address 0.

### Code snippet

- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L972-L981
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L992-L995


### Recommendation

While this does not currently pose a security risk, consider adding checks for the passed addresses being nonzero to prevent unexpected behavior where required, or documenting the fact that a zero address is indeed a valid parameter.


## 3.6. Use safeTransfer functions instead of ERC20/223

### Severity: low

### Description

Since GNG ICO contract transfers different third-party tokens, not all of them may follow ERC20 standard in transfer and transferFrom functions. So it's highly recommended to use safeTransfer and safeTranferFrom functions instead of them. For example here https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L946-L949 we rescue ERC20 tokens, and due to using transfer we would not be able to rescue tokens which are using non-standard transfer declaration(USDT) , same with transferFrom.

### Code Snippets
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L948
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L835

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

## 3.7. `amountGNG` doesn't contain real "vesting" amount

### Severity: low

### Description

Accordingly comments the `purchases[_buyer].amountGNG` should record "vesting" amount of each user. But when a user claims tokens the `amountGNG` does not decrease by claimed value. So `purchases[_buyer].amountGNG` does not contain "vesting" amount of the user anymore.

If this variable is used in UI to show a user's "vesting" amount remaining - it will not show real value. 

### Code Snippets
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L733-L736

### Recommendation

Add into the [claim()](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L760) function this string:

`purchases[_buyer].amountGNG -= (purchases[_receiver].amount_per_period * _num_periods);`

## 3.8. Minimal deposit value is set in 1 token

### Severity: low

### Description

Since users can pay with different tokens with different price the [requirement](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L814) to deposit at least 1 tokens puts them in an unequal position. Because users who will pay with ETH should deposit minimum 1 ETH (about $1600) but users who pay with CLO can buy just for $0.003.

This requirements has no any logical sense and should be removed, because we already have limitation of [minimum purchase](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L837) amount.

### Code Snippets

- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L814
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L771
- https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L853

### Recommendation

Remove requirement to deposit at least 1 token/coin.

## 3.9. Unnecessary gas usage.

### Severity: note

### Description

1. Is not necessary to add a place holder in contractName variable.

   Actual code:

   ```solidity
   string  public contractName = "NOT INITIALIZED";
   ```

2. In the functions [receive](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L766-L801), [tokenReceived](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L843-L896) and [buy](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L803-L841) the `PriceFeed(priceFeed).getPrice(...)` function is called 3 times.

3. In the functions [receive](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L766-L801), [tokenReceived](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L843-L896) and [buy](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L803-L841) the `IERC223(GnGToken_address).balanceOf(address(this))` function is called 3 times.


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


## 3.10. Owner privileges

### Severity: owner privileges

### Description


1. [modify_asset](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L937-L943) allows the owner to control what token contracts from the price feed contract would be accepted as payment for GNG tokens.

2. [withdraw](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L952-L964) allows the owner to withdraw native CLO from the contract before users have claimed all the GNG tokens.

3. [withdrawGNG](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L967-L970) and [ERC20Rescue](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L946-L949) functions can be used to withdraw GNG tokens in the contract during vesting period before the user can claim then resulting in losses for users. As per the developer notes excess tokens must be burned after the ICO has ended.
If this function intends to withdraw tokens that were not bought, used instead as a counter for tokens bought against the total deposited by the owner, to get the number of tokens not bought.

4. Functions [setup_contract](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L972-L981) and [setup_vesting](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L983-L988) function can be used after ICO started and could break completely the purpose of the ICO.

5. [delegateCall](https://github.com/Dexaran/GnG_ICO/blob/b731e4ea341361eee4371913ae5a33a14b3d7e94/ICO_vesting.sol#L992-L995) function can be used at any moment to change any storage slot of the contract making it unusable or do everything owner wants.

Consider using a [lifecycle model](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/Pausable.sol) to manage unexpected risks as an alternative.

### Recommendation

Since the owner has unlimited rights to do everything, the ownership must be transferred to a multi-sig contract.

## 3.11. Follow good coding practice

### Severity: note

### Description

#### 1. FUNCTIONS, PARAMETERS AND VARIABLES IN SNAKE CASE.
Use camel case for all functions, parameters and variables and snake case for constants

#### 2. Functions not used internally could be marked external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract.

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

https://gist.github.com/chhajershrenik/edbb15e2cae741932d8032ecda5ca90b


