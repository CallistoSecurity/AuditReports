# League of Ancients Security Audit Report

# 1. Summary

[League of Ancients](https://gitlab.com/jingyi2811/luo/-/tree/main/original-contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Smart Contract info : https://docs.google.com/document/d/1Obi4nCMnSF3Ji3aBWW-r0k0ZTysZYe2mFMwPuX3kMBM/edit
- Token Unlock Schedule : https://docs.google.com/spreadsheets/d/1S25D8yl6l0J9ZHHpFxeYfmuTFv6iUQuwqPec27Q7JWk/edit#gid=93878185
- Website : https://leagueofancients.com
- Twitter : @loaofficialmoba

# 2. In scope

Commit [95bae9f606968270baa830480167b6ce74198edb](https://gitlab.com/jingyi2811/luo/-/tree/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts)

## 2.1 Excluded

The schedule correctness of tokens distribution was excluded from audit.

# 3. Findings

In total, **2 issues** were reported, including:

- 1 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **8 notes** were reported, including:

- 3 notes.

- 5 owner privileges.


## 3.1. High gas usage on deployment

### Severity: note

### Description

In the [constructor](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaPresale.sol#L42) the loop `for` was used to fill 200 elements of mapping `_distributeDatesNo` with the index number of day. It will use a lot of gas on contract deployment.

### Recommendation

The only usage of `_distributeDatesNo` is to assign [currentTimestampNo](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaPresale.sol#L141). To safe gas we recommend to replace `_distributeDatesNo` mapping with function:
```Solidity
function _distributeDatesNo(uint timestamp) public pure returns(uint) {
    if (timestamp < _DEC_12_2021_00_00_00 || timestamp > _DEC_12_2021_00_00_00 + 86_400 * 199) return 0;
    return (timestamp - _DEC_12_2021_00_00_00) / 86_400;
}
```

## 3.2. Reducing gas usage in `returnBNB` function

### Severity: note

### Description

The [send](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaPresale.sol#L118) function will be called even if `giveBackBNBAmount == 0`. To reduce gas usage you may add `if` condition for this code:
```Solidity
        if (giveBackBNBAmount != 0) {
            bool success = payable(depositor).send(giveBackBNBAmount);
            if (!success) emit NotRefund(depositor, giveBackBNBAmount);

            uint contributedAmount = deposited * _bnbAmountCap / totalAddressesDepositAmount;
            _depositAddressesAwardedErc20CoinAmount[depositor] = _depositAddressesAwardedErc20CoinAmount[depositor] + contributedAmount * _dailyRewardPerBnb;
        }

```

## 3.3. Require will not show error message

### Severity: note

### Description

In the function `transferPresale` the [require](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L642) will not show error message if `_preSaleAmountCap - amount < 0` because transaction will failed on underflow protection.

### Recommendation

Use this `require(_preSaleAmountCap >= amount, 'No more amount allocates for preSale');` to be able to see error message.

## 3.4. Distribution values mismatch

### Severity: high

### Description

1. The category [PlayToEarn](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L228) has 250 000 000 cap amount, but to [_monthlyCoinDistribution](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L325-380) was added just 80 000 000 tokens.
2. The category [NFTStaking](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L226) has 80 000 000 cap amount, but to [_monthlyCoinDistribution](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L437-486) was added 250 000 000 tokens.
3. To category [Team](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L253) is added `46` instead of `46 * eighteen_decimals_value`.

## 3.5. Issues in the function dailyTransfer

### Severity: low

### Description

1. In the function `setDailyDistribution()` used maximum number of days = [1827](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L234) (from 0 to 1826). But in the function `dailyTransfer()` used [1828](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L676) days (from 0 to 1827).

2. The [_dailyCategoryIndex[category]](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L673) contain some index which is using in very wired manner. The [for](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L678) loop always starts from `i=0` but `last` will increase by 60 on every function call until it reach 1828. We can't get the sense of this logic.

3. Using mapping `_dailyCoinDistribution` will require a lot of gas to initialize its values in the function [setDailyDistribution()](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L231-268).

4. Calling function [_categoriesTransfer()](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L686) in the loop for everyday token transfers will require a the more gas the more unpaid days. Will be better to calculate total unpaid amount and call `_categoriesTransfer()` for that amount after the loop.

### Recommendation

Refactor function `dailyTransfer()`. And remove function [setDailyDistribution()](https://gitlab.com/jingyi2811/luo/-/blob/ac9d61404bc8acb8424e2b21ce5e29a1434e278a/contracts/LoaToken.sol#L231-268).

```Solidity
    function dailyTransfer() external {
        require(block.timestamp >= _DEC_12_2021, "Too early for daily transfer");
        string memory category = '';
        uint dailyDistribution;

        if(_categoriesAddress['Advisors1'] == msg.sender){
            category = 'Advisors1';
            dailyDistribution = 5_476 * eighteen_decimals_value;
        } else if(_categoriesAddress['Advisors2'] == msg.sender){
            category = 'Advisors2';
            dailyDistribution = 5_476 * eighteen_decimals_value;
        } else if(_categoriesAddress['Advisors3'] == msg.sender){
            category = 'Advisors3';
            dailyDistribution = 5_476 * eighteen_decimals_value;
        } else if(_categoriesAddress['Advisors4'] == msg.sender){
            category = 'Advisors4';
            dailyDistribution = 5_476 * eighteen_decimals_value;
        } else if(_categoriesAddress['Team'] == msg.sender){
            category = 'Team';
            dailyDistribution = 109_529 * eighteen_decimals_value;
        } else if(_categoriesAddress['Marketing'] == msg.sender){
            category = 'Marketing';
            dailyDistribution = 45_662 * eighteen_decimals_value;
        }

        require(bytes(category).length > 0, 'Invalid sender address for daily transfer');

        uint amountLeft = _categoriesAmountCap[category];
        require (amountLeft > 0, "All amount paid");
        uint maxDayToPay = amountLeft / dailyDistribution;
        uint lastPaid = _dailyCategoryIndex[category];  // index of last paid day
        uint day = (block.timestamp - _DEC_12_2021) / 86400 + 1 - lastPaid; // number of unpaid days
        if (day > maxDayToPay + 1) day = maxDayToPay + 1;
        uint amount = day * dailyDistribution;  // total amount to pay for passed days
        if (amount > amountLeft) amount = amountLeft;   // transfer all tokens that left
        if (amount > 0) {
            bool canTransfer = _categoriesTransfer(msg.sender, amount, category);
            if(canTransfer) {
                _dailyCategoryIndex[category] = day;    // store last paid day
            }
        }
    }
```


## 3.6. Admin privileges

### Severity: owner privileges

### Description

`LoaToken` contract Admin can:

1. Pause/unpause token transfer.
2. Pause/unpause token transfer for selected address.

`LoaPresale` contracts Admin can:

3. Add/remove addresses to/from whitelist.
4. Withdraw deposited BNB.
5. Finish `LoaPresale` earlier.

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
