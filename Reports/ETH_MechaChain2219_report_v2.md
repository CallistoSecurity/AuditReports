# Ethereum MechaChain - 2219 NFT collection Security Audit Report

# 1. Summary

[MechaChain - 2219 NFT collection](https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/main/contracts/ERC721/MechaPilots2219V1.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit: 5a161c0c58f6cbac8e475c1fc6378099997a94e4

- [MechaPilots2219V1.sol](https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/5a161c0c58f6cbac8e475c1fc6378099997a94e4/contracts/ERC721/MechaPilots2219V1.sol)

## 2.1. Excluded

1. There is [import](https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/5a161c0c58f6cbac8e475c1fc6378099997a94e4/contracts/ERC721/MechaPilots2219V1.sol#L14) of file `UpdatableOperatorFiltererUpgradeable.sol` but it does not exist in provided repository so it was excluded from audit.

2. Contracts from OpenZeppelin library that were already audited.

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issue.

- 1 low severity issues.

In total, **14 notes** were reported, including:

- 2 notes.

- 12 owner privileges.

No critical security issues were found.

## 3.1. Lock of user funds when user overpays.

### Severity: low

### Description

Internal function `_roundMint()` is used by functions `mintWithValidation()` and `mint()` which allow the user to mint `n` amount of tokens for an active round with or without validator signature respectively. The function `_roundMint()` accepts native chain currency ETH as payment for minting tokens.

The statement in the function `_roundMint()` checks if the total price of the token is less than the amount of eth attached to the transaction `require(round.price * amount <= msg.value, "Wrong price");`.

If the `msg.value` for the transaction is greater than the value to of specific amount of tokens, the excess ETH amount does not get refunded back to the User, and the contract does not implement any measures for users to claim back excess funds. Leading to users overpaying for the token(s).

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/5a161c0c58f6cbac8e475c1fc6378099997a94e4/contracts/ERC721/MechaPilots2219V1.sol#L806

### Recommendation

Change the following statement to the following to accept the exact ETH amount for the token(s) value
```solidity
require(roundPrice(roundId) * amount == msg.value, "Wrong price");
```

(or)

Implement pay back excess funds to user:
```solidity
uint256 correctPrice = round.price * amount;
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

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/5a161c0c58f6cbac8e475c1fc6378099997a94e4/contracts/ERC721/MechaPilots2219V1.sol#L423-L454

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
11. Functions `setTokenURI()` and `setTokenURIPerBatch()` allow authorized address with `URI_UPDATER_ROLE` set URI for any tokens
12. Functions `setDefaultRoyalty()` and `deleteDefaultRoyalty()` allow owner to set/delete royalty receiver and percentage.


### Recommendation

Since the owner has unlimited rights to do everything, the ownership must be transferred to a multi-sig contract.


## 3.4. Contract accepts ETH transfers

### Severity: note

### Description

Since Solidity v0.4.0 functions can be explicitly marked as payable to accept ETH, the function `receive()` is implemented in the contract to accept ETH directly to the contract and is not necessary for the contract's logic.

### Code snippet

- https://github.com/MechaChain/MechaChain-Smart-Contracts/blob/5a161c0c58f6cbac8e475c1fc6378099997a94e4/contracts/ERC721/MechaPilots2219V1.sol#L998

### Recommendation

Consider removing the `receive()` function to revert unintentional transfers.


## 3.5. Follow good coding practice

### Severity: note

### Description

1. Functions not used internally could be marked as external

It's a good coding practice to mark a function external when it's not called within the contract but only from outside the contract. It also helps in saving gas costs.

2. Missing docstrings

Few functions and Enums in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be clearly documented as well.

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can't be considered as safe since `UpdatableOperatorFiltererUpgradeable.sol` was not available for audit. In the audited code only low severity issue was found.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Previous audit report

https://gist.github.com/yuriy77k/e4f7eb6aa793f47c5af7eed20c27a933
