# Monsta Infinite Token & Presale Security Audit Report

# 1. Summary

[Monsta Infinite Token & Presale](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/tree/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

[Tokenomics of governance token $MONI.pdf](https://github.com/EthereumCommonwealth/Auditing/files/7074523/Tokenomics.of.governance.token.MONI.pdf)
[MONI Token Release Schedule.xlsx](https://github.com/EthereumCommonwealth/Auditing/files/7074525/MONI.Token.Release.Schedule.xlsx)

- Monsta Infinite Token
- Ticker : $MONI
- Total Supply : 270,000,000

# 2. In scope

Commit [02a4124c587b0339a80d32e891df87ab2cfb5885](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/tree/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts)

- contracts/MoniToken.sol 
- contracts/presale/MoniPresale1.sol
- contracts/presale/MoniPresale2.sol
- contracts/utils/SafeMath.sol


# 3. Findings

In total, **11 issues** were reported, including:

- 1 high severity issues.

- 4 medium severity issues.

- 6 low severity issues.

In total, **7 notes** were reported, including:

- 2 notes.

- 5 owner privileges.


## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(address recipient, ...)` function:

```
require(recipient != address(this));

```

## 3.2. ERC20 incompatibility wrong names.

### Severity: medium

### Description

According the [ERC20 Standard](https://eips.ethereum.org/EIPS/eip-20) a token contract must has function `name`, `symbol`, and `decimals`. But the variables `_name`, `_symbol`, `_decimals` declared as `public` will have the same getter functions names which is not compatible with ERC20 standard.

### Recommendation

Rename variables `_name`, `_symbol`, `_decimals` to  `name`, `symbol`, `decimals`.

## 3.3. `_name` and `_symbol` used appropriate way.

### Severity: low

### Description

The `_name` should contain full name of token and `_symbol` is short name. But [here](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/MoniToken.sol#L44-45) is the opposite.



## 3.4. Zero address check

### Severity: low

### Description

In the functions `transferOwnership` member of [MoniToken](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/MoniToken.sol#L575), [MoniPreSale1](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPresale1.sol#L45), [MoniPreSale2](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L44) contracts , [setPreSale1ContractNotYetSet](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/MoniToken.sol#L581), [setPreSale2ContractNotYetSet](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/MoniToken.sol#L586), and  is possible to pass `address(0)` as parameter by mistake.

### Recommendation

Add zero address checking:  `require(admin != address(0), "Zero address");` into the function.

## 3.5. Insufficient amount in one category blocks transfer in other categories

### Severity: low

### Description

In the function [_categoriesTransfer](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/MoniToken.sol#L684) the `require` revert transaction if `amount` is bigger than `_categoriesAmountCap`. 
Since function `transferLocalCategories` processing categories one by one, the revert in any of them will revert entire transaction.

### Recommendation

Function `_categoriesTransfer` should returns `false` instead of revert transaction.

## 3.6. Admin privileges

### Severity: owner privileges

### Description

`MoniToken` contract Admin can:

1. Transfer tokens that should be distributed by categories and preSales.
2. Pause/unpause token transfer.
3. Pause/unpause token transfer for selected address.

`MoniPreSale1` and `MoniPreSale2` contracts Admin can:

4. Add/remove addresses to/from whitelist.
5. Withdraw deposited BNB.

## 3.7. The function `removeAllDepositAddress()` may failed due `OUT_OF_GAS` exception

### Severity: low

### Description

The function `removeAllDepositAddress()` in [MoniPreSale1](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPresale1.sol#L58) and [MoniPreSale1](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L57) contracts may failed due `OUT_OF_GAS` exception if `_depositAddresses.length` very big.

### Recommendation

Alow remove deposit addresses by portions.

## 3.8. Date mismatch 

### Severity: note

### Description

The [Tokenomics of governance token $MONI.pdf](https://github.com/EthereumCommonwealth/Auditing/files/7074523/Tokenomics.of.governance.token.MONI.pdf) say:
>
> 90% will be distributed on 16 Oct 21.

But in the `MoniPreSale1` contract the date of 2nd distribution if [_Nov_16_2021](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPresale1.sol#L20).

## 3.9. Incorrect deposit checking

### Severity: note 

### Description

Deposit to `MoniPreSale1` contract should be allowed if `_bnbAmountCap` is not reached. 
But [require condition](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPresale1.sol#L70) allow to deposit if `_bnbAmountCap` already reached. 

### Recommendation

Use this `require(_totalAddressesDepositAmount < _bnbAmountCap, 'Deposit rejected, already reached the cap amount');`

## 3.10. The distribute functions may failed due `OUT_OF_GAS` exception

### Severity: medium

### Description

The distribute functions in [MoniPreSale1](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPresale1.sol#L92-99) and [MoniPreSale2](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L91-103) call `_distribute` function that use `for` loop to distribute tokens to all depositors. But in case of big number of depositors it may be failed due `OUT_OF_GAS` exception.

### Recommendation

Implement ability for depositors to claim tokens themselves or distribute tokens to depositors by parts (not to all depositors at one transaction).


## 3.11. No restriction to call function `distributeFirst` more than one time

### Severity: medium

### Description

The function `distributeFirst` in [MoniPreSale1](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPresale1.sol#L92) and [MoniPreSale2](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L91) can be called many times to distribute all tokens just after `_Sep_16_2021_1800`.

The same issue is in function [distributeSecond](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L96) of `MoniPreSale2` contract.

### Recommendation

Add checking that `distributeFirst` was called.

## 3.12. Incorrect require condition in `returnBNB` function

### Severity: low

### Description

The [require](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L80) condition should check that Presale2 is overed: `block.timestamp > _SEP_15_2021_18_00_00`.


## 3.13. The `returnBNB` function may failed due `OUT_OF_GAS` exception

### Severity: medium

### Description

The function [returnBNB](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L84-87) that use `for` loop to distribute tokens to all depositors. But in case of big number of depositors it may be failed due `OUT_OF_GAS` exception.

### Recommendation

Implement ability for depositors to claim BNB themselves or return BNB to depositors by parts (not to all depositors at one transaction).

## 3.14. No restriction to call function `returnBNB` more than one time

### Severity: high

### Description

The [returnBNB](https://gitlab.com/monsta-infinite/moni-smart-contracts/-/blob/02a4124c587b0339a80d32e891df87ab2cfb5885/contracts/presale/MoniPreSale2.sol#L79) function may be called many times until returns all available BNB.

### Recommendation

Add checking that `returnBNB` was called.


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

