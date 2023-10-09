# Ethereum MechaChain - 2219 NFT collection Security Audit Report

# 1. Summary

[MechaChain - 2219 NFT collection](https://github.com/MechaChain/MechaChain-Smart-Contracts/tree/9cb6aee43a03610751f4599ceff3631e720417bb) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit: 9cb6aee43a03610751f4599ceff3631e720417bb

- [MechaPilots2219V1.sol](https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol)

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issue.

- 0 low severity issues.

In total, **18 notes** were reported, including:

- 7 notes.

- 11 owner privileges.

No critical security issues were found.

## 3.1. Lock of user funds when the price of the token changes or the user overpays.

### Severity: medium

### Description

Internal function `_roundMint()` is used by functions `mintWithValidation()` and `mint()` which allow the user to mint `n` amount of tokens for an active round with or without validator signature respectively. The function `_roundMint()` accepts native chain currency ETH as payment for minting tokens, the price of token(s) is computed using the function `roundPrice()`.

The statement in the function `_roundMint()` checks if the total price of the token is less than the amount of eth attached to the transaction `require(roundPrice(roundId) * amount <= msg.value, "Wrong price");`.

As the price of the token for the round is reduced over time if the round validator exists else the price remains constant.

If the msg.value for the transaction is greater than the value of tokens computed using the function `roundPrice()`, the excess ETH amount does not get refunded back to the User, and the contract does not implement any measures for users to claim back excess funds. Leading to users overpaying for the token(s).

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L677

### Recommendation

Change the following statement to the following to accept the exact ETH amount for the token(s) value
```solidity
require(roundPrice(roundId) * amount == msg.value, "Wrong price");
```

(or)

Implement pay back excess funds to user:
```solidity
uint256 correctPrice = roundPrice(roundId) * amount;
// Correct price
require(correctPrice <= msg.value, "Wrong price");
if (correctPrice < msg.value) 
    payable(msg.sender).transfer(msg.value - correctPrice);
}
```


## 3.2. Functions setupMintRound() allows the owner to change round parameters which could lead to multiple issues.

### Severity: owner privileges

### Description

Function `setupMintRound()` allows the owner to modify existing round parameters after the round has been active.

If a round is reinitialized during or after the round period.
- This would allow the owner to re-initialize or extend/shorten the round if startTime or duration is modified respectively.
- If the validator address is modified then the new validator would have to provide new signatures for existing purchases or the users would not be able to mint token(s) with existing signatures for the modified round.
- Modifications to maxPrice, minPrice, priceDecreaseTime, or priceDecreaseAmount would lead users to buy token(s) at a higher price or lower price based on the modification of the parameters.

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L367-L417

### Recommendation

Consider adding a check to prevent modification to round parameters for active or completed rounds.

```solidity
if(round.startTime != 0) {
  require(round.startTime > block.timestamp, "Round in progress")
}
```

## 3.3. Owner privileges

### Severity: owner privileges

### Description

1. Contract MechaPilots2219V1 is implemented using a UUPS proxy pattern, allowing the owner to change any storage slot or upgrade the contract logic of the contract at any moment making it unusable, or do everything the owner wants.
2. Function `revealToken()` allows authorized addresses by the owner to reveal the user's token using the signature of a URI_UPDATER_ROLE which is moderated by the admin role using openzepplin's Access Control Upgradeable contract's implementation.
3. Function `airdrop()` allows owner to airdrop token(s) to any Externally owned account (EOA).
4. Function `setupMintRound()` allows the owner to set up an arbitrary number of rounds for minting tokens (public and whitelisted).
5. Functions `pause()/unpause()` allows the owner to disable/enable mints, transactions, and burns for the users.
6. Functions `setBaseURI()` and `setBaseExtension()` allows owners to modify/set baseURI and base metadata extension for the unrevealed tokens respectively.
7. Function `setBurnable()` allows the owner to configure whether users can burn token(s) or not.
8. Function `setMaxMintsPerWallet()` allows the maximum number of tokens that can be minted by an Externally owned account (EOA) for a public round.
9. Functions `withdraw()` allows the owner to transfer ETH from the contract.
10. `withdrawTokens()` allows owner to transfer/recover ERC-20 tokens from the contract.


### Recommendation

Since the owner has unlimited rights to do everything, the ownership must be transferred to a multi-sig contract.



## 3.4. Signature can be reused in the function mintWithValidation().

### Severity: note

### Description

The function `mintWithValidation()` allows the user to mint token(s) with a signature provided by an authorized validator. If the maxMint is greater than the amount (no. of token(s)) that can be minted by a user. This would allow users to mint multiple times using the same signature until the maxMint is reached for the round.

### Code snippet
- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L284-L287

### Recommendation

Consider adding a nonce with mapping to the roundId for the validator for the Externally owned account (EOA), and validate the nonce during signature verification to prevent signature replay.


## 3.5. Unused import

### Severity: note

### Description

ReentrancyGuardUpgradeable is imported into the contract but is not used.

### Code snippet
- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L10

### Recommendation

Consider removing the unused import.



## 3.6. Contract accepts ETH transfers

### Severity: note

### Description

Since Solidity v0.4.0 functions can be explicitly marked as payable to accept ETH, the function `receive()` is implemented in the contract to accept ETH directly to the contract and is not necessary for the contract's logic.

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L857

### Recommendation

Consider removing the `receive()` function to revert unintentional transfers.

## 3.7. Function setupMintRound() violates the dev requirement for roundId.

### Severity: note

### Description

As per [dev documentation](https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L372) for the function, `setupMintRound()` the roundId for the round can be zero but the required statement prevents the roundId from being zero.

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L395

### Recommendation

Consider updating the documentation or removing the check as per requirements.


## 3.8. Missing check for priceDecreaseTime value

### Severity: note

### Description

The [comment](https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L381-L382) says: `The number of seconds to wait between each decreasing (must be > 900)`, but there is no check of that. Also looks like comments confuse description for `priceDecreaseTime` and `priceDecreaseAmount`

### Recommendation
Add requirement:

```solidity
require(priceDecreaseTime == 0 || priceDecreaseTime > 900);
```


## 3.9. totalSupplyByFaction does not reflect burned tokens.

### Severity: note

### Description

`totalSupply()` returns the value of minted tokens minus burned tokens but `totalSupplyByFaction` only returns minted tokens.

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L570-L586

### Recommendation

Add counter `totalBurnedByFaction` or reduce `totalSupplyByFaction` using `tokenFaction` map to get the token´s faction.

## 3.10. Follow good coding practice

### Severity: note

### Description

1. Functions not used internally could be marked as external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract. It also helps in saving gas costs.

2. Missing docstrings

Few functions and Enums in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be clearly documented as well.

3. It's always a good practice to adhere to the Checks-Effects-Interaction pattern , violating this might lead to serious vulnerabilities
like re-entrancy. 

There are 2 instances where this was violated:

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/main/contracts/ERC721/MechaPilots2219V1.sol#L741-L749

Here `_mint` is called before the state updates of `_totalMinted` and `_totalMintedByFaction`

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/main/contracts/ERC721/MechaPilots2219V1.sol#L697-700

Here ` ownerToRoundTotalMinted` is updated after the `_safeMintCall`


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

- https://gist.github.com/chhajershrenik/23491cfee03165b74b2fc343de1264b9
- https://gist.github.com/SakshamGuruji3/6a6abc58ccc99010411a8f6beabb4cb5
- https://gist.github.com/ESNJS/41d9f699236acf725d4773e927572be4

## 6.1 Comments for [chhajershrenik](https://gist.github.com/chhajershrenik/23491cfee03165b74b2fc343de1264b9) report

### 3.1. Lock of user funds when the price of the token changes or the user overpays.
Severity was changed from "critical" to "medium", because this issue affect only on users who send bigger value than should be. In case price of the token changes after user's payment it's not a "critical" issue, because user agreed to buy token by this price.

### 3.3. Function mintWithValidation() is vulnerable to a signature replay attack.
Severity was changed from "medium" to "note", because it does not change purpose of validation. Anyway user can't  buy more tokens than `maxMint`. Implementation of `nonce` will require to use additional storage slot in the smart contract to store already processed `nonce` which will increase transaction gas cost without any advantage for security.

## 6.2 Comments for [SakshamGuruji3](https://gist.github.com/SakshamGuruji3/6a6abc58ccc99010411a8f6beabb4cb5) report

### 1. DIVIDE BEFORE MULTIPLE WILL YIELD 0 decreaseBy VALUE FOR CERTAIN CONDITIONS
It's not an issue. Here `decreaseBy` calculates as number of `decreaseTime` period multiply by `decreaseAmount` (decrease amount per period).

### 7. USER WOULD NOT BE ABLE TO MINT EVEN IF WITHIN THE ROUND
Not an issue. The requirement considers entire round duration. it starts 

https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/9cb6aee43a03610751f4599ceff3631e720417bb/contracts/ERC721/MechaPilots2219V1.sol#L669-L672

### 12. Initializers could be front-run
Not an issue. In the `constructor` is called `_disableInitializers();`. 


## 6.3 Comments for [ESNJS](https://gist.github.com/ESNJS/41d9f699236acf725d4773e927572be4) report

### 1. For loop in _safeMint function
Is not an issue. If `maxMintsPerWallet` is too big, users can mint tokens using `amount` of tokens that can be minted.



