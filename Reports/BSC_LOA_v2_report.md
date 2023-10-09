# League-Of-Ancients V2. Security Audit Report

# 1. Summary

[League-Of-Ancients](https://github.com/L-o-A/SmartContracts/tree/51a1e02a9d8d883a58206faaf5279227a581b19c) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit: 51a1e02a9d8d883a58206faaf5279227a581b19c

- [Capsule.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Capsule.sol)
- [CapsuleData.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/CapsuleData.sol)
- [IUtil.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/IUtil.sol)
- [LOAUtil.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol)
- [LoANFT.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFT.sol)
- [LoANFTData.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol)
- [MultiSigAdmin.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/MultiSigAdmin.sol)
- [NFTMarket.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol)
- [Raffle.sol](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Raffle.sol)

# 2.1. Excluded
Calculation formulas were excluded from the audit due to a lack of contract documentation.

Standard OpenZeppelin contracts were excluded from the audit:

- @openzeppelin/contracts/token/ERC1155/ERC1155.sol
- @openzeppelin/contracts/access/Ownable.sol
- @openzeppelin/contracts/utils/Counters.sol
- @openzeppelin/contracts/security/ReentrancyGuard.sol
- @openzeppelin/contracts/utils/math/SafeMath.sol
- @openzeppelin/contracts/token/ERC1155/utils/ERC1155Holder.sol


# 3. Findings

In total, **11 issues** were reported, including:

- 1 critical severity issue.

- 3 high severity issues.

- 5 medium severity issues.

- 2 low severity issues.

In total, **17 notes** were reported, including:

- 10 notes.

- 12 owner privileges.



## 3.1. [Capsule](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Capsule.sol)

### 3.1.1. Unused import

#### Severity: note

#### Description

The `Capsule` contract inherits basic access control properties from Openzeppelin's [`Ownable`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Capsule.sol#L61) contract, which can be removed. As the contract utilizes the `MultiSigAdmin` contract using the `IAdmin` interface for provisioning access controls checks for the functions in the contract.

### 3.1.2. Implementation deviates from Docs

#### Severity: note

#### Description

According to the description of the function airdrop here https://github.com/L-o-A/SmartContracts/tree/main/contracts , a maximum of 5 capsules can be dropped at a time, but this condition is not implemented in the function `airdrop()`, according to the implementation admin can mint any number of `units` of the capsule to the `dropTo` address.

#### Code Snippet

- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Capsule.sol#102

### 3.1.3. Owner Privileges

#### Severity: owner privileges

#### Description

1. Function `putURI()` allows `admin` to `_capsuleTypeURI[capsuleType]` URL address for the ERC-1155 tokens.
2. Function `airdrop()` allows `admin` to airdrop ERC-1155 tokens to the users.

## 3.2. [CapsuleData](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/CapsuleData.sol)

### 3.2.1. Function getCapsuleSupply() implements unnecessary authorization checks

#### Severity: note

The view function `getCapsuleSupply()` implements an unnecessary `msg.sender` authorization check using the `validAdmin` modifier, which is not required as the contract storage state is publicly accessible.

#### Code Snippet

- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/CapsuleData.sol#L182-L184

### 3.2.2. Function addCapsuleSupply should be restricted to `validAdmin`

#### Severity: high

#### Description

The `addCapsuleSupply` function updates the supply for the provided levels for a capsule. Anybody can call this function and set any supply for any level broking contract functionality. 

#### Code snippet

- https://github.com/L-o-A/SmartContracts/blob/main/contracts/CapsuleData.sol#L105

#### Recommendation

This function should have been protected by a `validAdmin` modifier.

## 3.3. [Raffle](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Raffle.sol)

### 3.3.1. Raffle parameters can be changed when raffle already opened

#### Severity: owner privileges

#### Description

Function `setRaffleInfo()` allows `validAdmin` set the parameters for the raffle even if the raffle is already opened. 

## 3.4. [LOAUtil](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol)

### 3.4.1. Functions random(), randomNumber(), and sudoRandom() does not generate cryptographic secure random numbers

#### Severity: medium

#### Description

1. The function `random()` depends on `block.timestamp` as a seed for randomness to compute random numbers. The resulting random number can be influenced by the miners executing the transaction and pre-determine the resulting value.
2. The function `randomNumber()` generating pseudo-random numbers can be pre-determined based on the `_random_nos` numbers assigned by the admin using the function `fullfillRandom()`. This function is used in function [pickWinner()](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Raffle.sol#L227) of `Raffle` contract, so raffle result will be predictable if admin `fullfillRandom` while Raffle event is open.
3. The function `sudoRandom()` depends on `randomValue` and `slot` input for generating pseudo-random numbers which could be pre-determined by the miner processing the transaction.

#### Code snippet

- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L30-L35
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L37-L49
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L51-L56

#### Recommendation

Consider using the [Chainlink VRF](https://docs.chain.link/vrf/) pseudo-random number generator for critical functions.

### 3.4.2. Missing owner authentication leads to DoS/griefing

#### Severity: high

#### Description

The function `randomNumber()` can be used by an attacker to exhaust `_random_nos` available to a user by repeatedly calling the function with the `owner` address of the user. The user would have to re-request approval for generating a random using the function [`requestRandom()`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L62-L66) and admin would have full fill the approval using the function [`fullfillRandom()`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L68-L72). An malicious user can abuse this to grief user and admin resulting in increased gas cost for obtaining the random number, It is also possible for the malicious user to repeatedly exhaust all the `_random_nos` available to the user leading to DoS.

#### Code snippet

- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L37-L49

#### Recommendation

Consider using `msg.sender` for authentication for the `owner` address to authenticate the user.

### 3.4.3. Owner Privileges

#### Owner Privileges

#### Description

Function `fullfillRandom()` allows admin to full fill requests for random numbers for users. 

## 3.5. [LoANFT](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFT.sol)

### 3.5.1. Unused import

#### Severity: note

#### Description

The `LoANFT` contract inherits basic access control properties from Openzeppelin's [`Ownable`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/Capsule.sol#L61) contract, which can be removed. As the contract utilizes the `MultiSigAdmin` contract using the `IAdmin` interface for provisioning access controls checks for the functions in the contract.

### 3.5.2. LOA NFT URI can be changed in any time

#### Severity: owner privileges

#### Description

Function `putURI()` allows `admin` to update the URL for ERC-1155 tokens.

## 3.6. [LoANFTData](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol)

### 3.6.1. Takeover of the contract state is possible by reinitialization using the init() function

#### Severity: critical

#### Description

The `init()` function does not check if the contract state is already initialized and is missing authentication checks, allowing anyone to set the `_admin` address and gain access to privileged functions of the contract.

#### Recommendation

It is recommended to add checks to ensure that the contract state cannot be re-initialized and consider using OpenZeppelin's [Initializable](https://github.com/OpenZeppelin/openzeppelin-upgrades/blob/master/packages/core/contracts/Initializable.sol) and use `_disableInitializers()` in the constructor to avoid frontrunning. If re=initilaization is a requirement and add authentication checks to allow only authorized wallet/contract to re-initialize the contracts state.

### 3.6.2. Issues with using `sudoRandom`

#### Severity: medium

#### Description

In the function `randomSubList()` the [`index`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol#L918) can't be bigger than 99 even if `list.length` bigger than 99. Also, if `list.length` is less than 99 the lower index has a higher chance to be selected.

For example, if `list.length = 70` then indexes from `[0..29]` has twice more chance to be selected than indexes `[30..69]`. I.e. index = 1 will be selected if `sudoRandom` returns 1 or 71, but index 30 will be only if `sudoRandom` returns 30.

Moreover, `sudoRandom` has [cycle of 31](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LOAUtil.sol#L53) values. I.e if `startCount = 32` then `sudoRandom` returns the same value as for `startCount = 1`. It means that [units](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol#L917) can't be greater than 31.

#### Recommendation

Consider using another type of random generator.

### 3.6.3. addNFTSupply overwrite NFT total supply

#### Severity: low

#### Description

The [`nftSupply._total_supply`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol#L766) reset to 0 each time when admin call `addNFTSupply`. If admin wants to change supply just for 1 hero the [`total_supply`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol#L772) will be supply just of this hero (even if others previous heros have been `_consumed`).

### 3.6.4. addNFTAttributeLimits of LoANFTData contract does not allow maxValues = minValues

#### Severity: note

#### Description

According to the error message of [these requirements](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/LoANFTData.sol#L338-L343) `Max < Min` should be allowed values when `Max == Min`, but `require(maxValues[i] > minValues[i])` don't allow it.

#### Recommendation

Fix require condition:
```Solidity
require(maxValues[i] >= minValues[i], "Max < Min");
```

or fix error message:
```Solidity
require(maxValues[i] > minValues[i], "Max <= Min");
```

### 3.6.5. Owner Privileges

#### Severity: owner privileges

#### Description

1. Function `addNFTAttributeLimits()` allows `validAdmin` to modify/add NFT attributes.
2. Function `addNFTSupply()` allows `validAdmin` to sets the supply of different hero's for a particular level NFT.
3. Function `updateFees()` allows `validAdmin` to update fees for NFT minting from capsules of particular types.
4. Function `putNFTAttributeNames()` allows `validAdmin` to sets NFT attribute names. The function does not check if an attribute name already exists and could lead to overwriting existing NFT attribute names.

## 3.7. [MultiSigAdmin](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/MultiSigAdmin.sol)

### 3.7.1. Frontrunning of init() function possible

#### Severity: note

#### Description

An attacker can take over the contracts state by frontrunning the `init()` function. Even if the proxy contract is used the implementation contract state could still be taken over by an attacker.

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/MultiSigAdmin.sol#L32-L37

#### Recommendation

Consider using OpenZeppelin's [Initializable](https://github.com/OpenZeppelin/openzeppelin-upgrades/blob/master/packages/core/contracts/Initializable.sol) and use `_disableInitializers()` in the constructor to avoid frontrunning or implement authentication for the `init()` function.

### 3.7.2. Missing zero address checks

#### Severity: note

#### Description

In several places in the code, addresses are passed as parameters to functions. In many of these instances, the functions do not validate that the passed address is not the address 0.

#### Recommendation

While this does not currently pose a security risk, consider adding checks for the passed addresses being nonzero to prevent unexpected behavior where required, or documenting the fact that a zero address is indeed a valid parameter.

### 3.7.3. Owner Privileges

#### Severity: owner privileges

#### Description

1. Function `modifyRaffleAddress()` allows `admin` to add or remove `_raffleAddresses` in the list.
2. Function [modifyAdmin](https://github.com/L-o-A/SmartContracts/blob/main/contracts/MultiSigAdmin.sol#L141) allows to add the same admin address many times. But only one address can be removed from the list. We recommend to use `require(_admins[adminAddress] != 1, "Admin already added")` before adding new admin in the `add` condition.

## 3.8. [NFTMarket](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol)

### 3.8.1. Function list() prevents users to list NFT units in the marketplace

#### Severity: medium

#### Description

For a user to list an NTF unit on the market place the function `list()` checks if a user has exactly 1 NFT unit, to be able to list the NFT unit for the particular `tokenId`. If the user has more than one NFT unit the check would not allow the user to list any of the NFT units owned by the user for the particular `tokenId`. As the condition would fail and revert the transaction.

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L154

#### Recommendation

Consider changing the conditional statement as follows to allow the user to list NFT units owned by the user.

```solidity
require(erc1155.balanceOf(msg.sender, tokenId) >= 1, "User doesn't have enough NFT Units.");
```
(or)

```solidity
require(erc1155.balanceOf(msg.sender, tokenId) > 0, "User doesn't have enough NFT Units.");
```

### 3.8.2. Function unlist() locks NFT units of users in the contract

#### Severity: high

#### Description

If more than one NFT unit is listed on the marketplace using the function `list()` users cannot de-list the marketplace listing using the function `unlist()`. As the conditional check requires the contract to have exactly one NFT unit owned by the contract. An attacker can place an NFT list with a high price preventing users from de-list the NFT unit or forcing them to sell the NFT unit.

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L237

#### Recommendation

Consider changing the conditional statement as follows to allow the user to de-list the NFT unit from the marketplace.

```solidity
require( erc1155.balanceOf(address(this), tokenId) >= 1, "Contract doesn't have enough NFT Units.");
```
(or)

```solidity
require( erc1155.balanceOf(address(this), tokenId) > 0, "Contract doesn't have enough NFT Units.");
```

_**Note**:_ The following fix would introduce a re-entrancy issue and implement appropriate measures to mitigate the re-entrancy issue and follow the Checks-Effects-Interactions pattern to avoid re-entrancy attacks.

### 3.8.3. Function giftNFT() prevents users from gifting NFT units

#### Severity: medium

#### Description

The function `giftNFT()` can be used by the user when `_giftingEnabled` is set to True by the admin. The function `giftNFT()` requires users to have exactly one NFT unit, if a user owns multiple NFT units the conditional check does not allow the user to transfer NFT units when `_giftingEnabled` is set to True.

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L288

#### Recommendation

Consider changing the conditional statement as follows to allow the user to gift NFT units if the user owns more than one NFT unit.

```solidity
require(IERC1155(_admin.getNFTAddress()).balanceOf(msg.sender, id) >= 1, "NFT doest belong to you");
```
(or)

```solidity
require(IERC1155(_admin.getNFTAddress()).balanceOf(msg.sender, id) > 0, "NFT doest belong to you");
```

### 3.8.4. Possible reentrance 

#### Severity: medium

#### Description

In the functions `unlist()` and `buy()`  re-entrancy attack is possible to change the queue in `_listed_items`. 

#### Code Snippet

- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L239
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L321

#### Recommendation

It is recommended to follow the Checks-Effects-Interactions pattern to avoid re-entrancy, Call the function [`safeTransferFrom`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L239) at the end of `unlist()` function.

The same for [`buy()`](https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L321) function.

### 3.8.5. Same _erc20Token variable has different description and error messages

#### Severity: note

As per the comments and the error message in the contract NFTMarket, the variable `_erc20Token` is used for BUSD, and LOA ERC-20 token contracts. 
1. The comment in the `_erc20Token` variable refers to the BUSD token contract, 
2. In the function `list()` require `price` should be more than 1 BUSD token, but the error message say `"Price must be at least 1 wei"`
3. in the function `buy()` the error message refers to LOA tokens. 

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L64
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L149
- https://github.com/L-o-A/SmartContracts/blob/51a1e02a9d8d883a58206faaf5279227a581b19c/contracts/NFTMarket.sol#L310-L313

#### Recommendation

Fix error messages.

### 3.8.6. Payable `buy` function does not design to process BNB payment

#### Severity: low

#### Description

If a function is payable and does not use ETH/BNB for any purpose, ether sent by a user mistakenly to the function will be lost. 

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/main/contracts/NFTMarket.sol#L299

#### Recommendation

Consider removing the `payable` modifier in this case. 

### 3.8.7. Unused variable

#### Severity: note

#### Description

The variable `_itemsSold` is never used in the contract, from the looks of it seems like a variable that would have kept track of the total sold nft's in the contract for off-chain purposes, but has been not used.

#### Code Snippet
- https://github.com/L-o-A/SmartContracts/blob/main/contracts/NFTMarket.sol#L63

### 3.8.8. Owner Privileges

#### Severity: owner privileges

#### Description

Function `updateFees()` allows `_admin` to update `_listingFee` and `_transactionFee` for trading NFT units of NFT contracts. If the `_listingFee[contractAddresses[i]]` must be greater than 0 to allow users to list NFT units in the marketplace.



## 3.9. Follow good coding practice

### Severity: note

### Description

1. Unlocked Pragma

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.7, ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

2. Functions not used internally could be marked as external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract. It also helps in saving gas costs.

3. ETH transfers in the contracts are performed using the transfer function

Any smart contract that uses transfer() or send() is taking a hard dependency on gas costs by forwarding a fixed amount of gas: 2300.

In case when associated gas costs increase the function would fail, causing the transaction to revert and unable to withdraw ETH from the contract.

4. Multiple typo's in the code base

The contracts contain misspelled words in multiple instances.

5. Missing test suite

The contracts are missing a test suite to validate and verify the behavior of the contract functionalities. Adding tests is recommended to ensure that the contract functions and behaves as expected.

6. Missing docstrings

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

7. Use scientific notation for better readability

Use 1e18 instead of using 1_000_000_000_000_000_000 here https://github.com/L-o-A/SmartContracts/blob/main/contracts/NFTMarket.sol#L149


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

- https://gist.github.com/chhajershrenik/5f2dc72e6e531ca9570e763f479db22f

- https://gist.github.com/SakshamGuruji3/e3a22656ea929bf76ce3d85fab735de2

## 6.1 Comments for [chhajershrenik](https://gist.github.com/chhajershrenik/5f2dc72e6e531ca9570e763f479db22f) report

### 3.2.1. Integer overflow leads to overwriting
### 3.6.3. Integer overflow leads to overwriting
### 3.8.3. Integer overflow leads to loss/lock of user NFT unit
### 3.9.1. Integer overflow leads to the reassignment of the existing ticket id to the user

It's not an issue. Max uint256 value is so big so even if you will increase it billion times per second the our Universe will die sooner than you could overflow its value. 

### 3.3.1. Missing contracts characteristics in the interface

Isn't an issue. Interface may contain declaration of functions that should be used b other contracts. It's not necessary all functions there.

### 3.7.2. Functions can only be used once

It's not an issue. Since MultiSigAdmin address is used in constructor in many other contracts we can't use those contracts addresses in constructor of MultiSigAdmin, because those contracts weren't deployed yet.

### 3.8.7. Front-running is possible with function buy()
It's not an issue. Anybody can buy token on the market place with actual price. Owner can delist or change price if token was not sold. Doesn't matter if somebody buy token in the same block when owner try to delist or it was bough before it doesn't broke marketplace logic.

### 3.8.8. The listing fee is not refunded to the user if the NFT unit is unlisted
It's not an issue. User pay fee for listing. Why the fee should be returned? It's not a selling fee that will be taken only if token will be sold. For example, if projects pay for listing on exchange they don't receive refund in case of unlisting.

### 3.9.2. Issue using randomNumber()
This is the same issue as `3.4.1. Functions random(), randomNumber(), and sudoRandom() does not generate cryptographic secure random numbers` so does not considered as separate issue.

### Owner Privileges

We shouldn't include in the report all `Owner Privileges`. Many of them are required for initial contract setup (like set contracts addresses), others for normal workflow (like terminating raffle or rescue tokens from contract). We should include on audit report only dangerous `Owner Privileges` which can hurt users or broke normal contracts workflow.

## 6.2 Comments for [SakshamGuruji3](https://gist.github.com/SakshamGuruji3/e3a22656ea929bf76ce3d85fab735de2) report

### Ensure Every Event Has 3 indexed fields (If 3 or more parameters , if less than 3 then every field should be indexed)
Not an issue. Indexed parameters are used when require to find event by these parameters. If it's not required then indexed parameter is not necessary. 

### <= Should Be Used Instead Of < In buyTicket Function
Not an issue. It's up to developer to decide if start and end time is included to allowed period. In this case start time is excluded but end time is included into Raffle period. So actual raffle time is not reduced.

### uint8 Can Be Used Instead Of uint256
It's not correct. `capsuleIdsMinted` array should hold `capsuleIds` which has type `uint256`.

### Update Admin Should Be A 2 step Process
Is not necessary.

