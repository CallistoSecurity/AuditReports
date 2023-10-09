# VIPWARZ Security Audit Report

# 1. Summary

[VIPWARZ](https://github.com/VIPSVERSE/VVT-Token) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- [VIPWARZ tokenomics](https://docs.google.com/document/d/1TTXJrlTQeIdGe6KSpavppLuNixVQdrgf38SfmlPTvws/edit#)

# 2. In scope

Commit [a5e9ef8cc915be7d6779bfd6f92d6d6a60253173](https://github.com/VIPSVERSE/VVT-Token/tree/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173)

- ICO.sol
- LockedFund.sol
- VVT.sol

# 3. Findings

In total, **4 issues** were reported, including:

- 2 high severity issues.

- 2 medium severity issues.

- 0 low severity issues.

In total, **1 notes** were reported, including:

- 0 notes.

- 1 owner privileges.



## 3.1. Incorrect calculation in function getCurrentPrice()

### Severity: medium

### Description

In [this](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L218) calculation the coefficient is added to the `basePrice` but it should be multiplied.

### Recommendation

Calculation should be:

```Solidity
        return basePrice * (100 + ( (block.timestamp - startTime) / roundDuration) * priceIncrease) / 100;
```

## 3.2. The function getPriceInToken() does not take in account price decimals.

### Severity: medium

### Description
The function [getPriceInToken()](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L223-L231) should return price with decimals (18 decimals by default). In other case the function will return 0 if token price is higher than base price.

The `PriceFeed` contract returns token price in USD with 18 decimals. Therefore, when [divide price by token price](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L226) you should multiply it by `1e18` before.

```Solidity
            return getCurrentPrice() * 1e18 / priceToken;
```

Also, in final cost calculation [here](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L259) and [here](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L272) you have t divide it by `1e18`. For example:

```Solidity
        uint256 cost = getPriceInToken(_token) * amount / 1e18;
```

## 3.3. `transferFrom` without `approve`

### Severity: high

### Description

In the functions [claimForAddress](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L288) and [claimAllAvailable](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/ICO.sol#L304) you have used `transferFrom` without call `approve` before. The function [transferFrom](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/VVT.sol#L417) of `VVT` token require `approve` even if sender and spender is the same.


### Recommendation

Use function `transfer` instead of `transferFrom` or call `approve(address(this), type(uint256).max);` in constructor.

## 3.4. Owner privileges.

### Severity: owner privileges.

### Description

Owner of VVT token can set/change any address as [farm](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/VVT.sol#L679-L681) that have permission to mint tokens.



## 3.5. Incorrect calculation in function `claimToken()`

### Severity: high

### Description

[This](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/LockedFund.sol#L149-L157) calculation looks incorrect.

```Solidity
        uint256 activeTime = block.timestamp - (startTime+initialLockTime);
        uint256 currentRound = activeTime/ roundDuration;
        uint256 wantToClaim = initialBalance * (currentRound * releasePerRound ) / 1000; 
        uint256 finalAmount = wantToClaim - (initialBalance  - claimedBalance);
        claimedBalance += finalAmount;
        if( (claimedBalance + finalAmount) > initialBalance){//just to be sure there is no any cummulative calculation error and we can't claim last round
            finalAmount = initialBalance - claimedBalance;
        }
```
1. The first [round](https://github.com/VIPSVERSE/VVT-Token/blob/a5e9ef8cc915be7d6779bfd6f92d6d6a60253173/LockedFund.sol#L150-L151) is 0 (when `activeTime < roundDuration`). So in this round the tokens aren't able to claim. 
If you want to allow claim just after `startTime+initialLockTime` you should start rounds from 1: `uint256 currentRound = activeTime/ roundDuration + 1;`

2. `finalAmount` calculation cause underflow exception. Let's say we call function `claimToken()` first time. So `wantToClaim < initialBalance` and `claimedBalance = 0`. Then `finalAmount = (wantToClaim - initialBalance) < 0`. It will cause underflow exception.

3. In condition `if( (claimedBalance + finalAmount) > initialBalance)` you add `finalAmount` twice because you already did it in previous line `claimedBalance += finalAmount;`


### Recommendation

I think, this calculation should be:

```Solidity
        uint256 activeTime = block.timestamp - (startTime+initialLockTime);
        uint256 currentRound = activeTime / roundDuration + 1;
        uint256 wantToClaim = initialBalance * (currentRound * releasePerRound ) / 1000; 
        uint256 finalAmount = wantToClaim - claimedBalance;
        if( (claimedBalance + finalAmount) > initialBalance) {
            finalAmount = initialBalance - claimedBalance;
        }
        claimedBalance += finalAmount;
```


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
