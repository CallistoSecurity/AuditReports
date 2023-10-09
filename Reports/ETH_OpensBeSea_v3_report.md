# OpensBeSea v3 Security Audit Report

# 1. Summary

[OpensBeSea](https://github.com/oleksiivinogradov/openbisea_smartcontracts/tree/main/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Website: https://openbisea.io/
- Telegram: https://t.me/openbisea_en

# 2. In scope

Commit [4c1b355a32618c0ddbac83e66b353bb1803454bc](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/)

- AbsNFT.sol
- AbsNFT1155.sol
- IOpenBiSea.sol
- IOpenBiSeaAuction.sol
- IOracle.sol
- OBS.sol
- OpenBiSea.sol
- OpenBiSeaAuction.sol
- Oracle.sol
- StakingOBS.sol

## 2.1. Excluded

Standard OpenZeppelin contracts were excluded from audit:
- @openzeppelin/contracts/access/Ownable.sol
- @openzeppelin/contracts/utils/Counters.sol
- @openzeppelin/contracts/token/ERC1155/ERC1155.sol
- @openzeppelin/contracts/token/ERC721/ERC721.sol
- @openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol
- @openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol
- @openzeppelin/contracts/token/ERC721/IERC721Receiver.sol
- @openzeppelin/contracts/token/ERC721/IERC721.sol
- @openzeppelin/contracts/token/ERC721/extensions/IERC721Metadata.sol
- @openzeppelin/contracts/token/ERC1155/extensions/IERC1155MetadataURI.sol
- @openzeppelin/contracts/token/ERC1155/IERC1155Receiver.sol
- @openzeppelin/contracts/token/ERC1155/IERC1155.sol
- @openzeppelin/contracts/token/ERC20/ERC20.sol
- @openzeppelin/contracts/token/ERC20/IERC20.sol
- @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol
- @openzeppelin/contracts/utils/math/SafeMath.sol
- @openzeppelin/contracts/utils/Address.sol
- @openzeppelin/contracts/utils/structs/EnumerableSet.sol

# 3. Findings

In total, **6 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 3 low severity issues.

In total, **5 notes** were reported, including:

- 1 notes.

- 4 owner privileges.


## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

OpenBiSea Token (OBS) has known vulnerabilities of ERC-20 token:

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).


## 3.2. Anybody can mint AbsNFT tokens

### Severity: note

### Description

The function [mint()](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/c5b535936d8f6ecc1647860823f7592a78f10e11/contracts/AbsNFT.sol#L18) has no restriction and alow anybody to mint `AbsNFT` tokens.

This ability may be used by scammers to create new token with `tokenUri` of another token on this contract. However `tokenId` of both tokens will be different.


## 3.3. Owner privileges

### Severity: owner privileges

### Description

1. The owner of `AbsNFT1155` contract can:
   1. Set and change [URI](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/AbsNFT1155.sol#L19).
   2. [mint](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/AbsNFT1155.sol#L23) tokens.
2. The owner of `OBS` contract can: 
   1. [mint](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/OBS.sol#L11) 
   2. [burn](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/OBS.sol#L14) OpenBiSea Token to/from any account.
3. The owner of `StakingOBS` sale contract has right to [withdraw](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/StakingOBS.sol#L150-L156) any amount of tokens from contract included users' staked tokens.


## 3.4. High gas consumption when use `contractsWhitelisted`

### Severity: low

### Description

Functions that work with [contractsWhitelisted](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/OpenBiSeaAuction.sol#L59-L78) use loop `for` to go throw entire list of contracts to check if contract is whitelisted. This operation required the more gas the bigger list is. 

### Recommendation

Create `mapping(address => uint256) public indexOfContractsWhitelisted;` and use it get index of whitelisted contract instead of `for` loop. Start index from 1.

## 3.5. High gas consumption of function `contractsTokenIdsListUpdated`

### Severity: low

### Description

Functions that work with [contractsTokenIdsListUpdated](https://github.com/oleksiivinogradov/openbisea_smartcontracts/blob/4c1b355a32618c0ddbac83e66b353bb1803454bc/contracts/OpenBiSeaAuction.sol#L250-L260) use loop `for` to go throw `contractsTokenIdList`.  This operation required the more gas the bigger list is. 

Try to avoid loops when work with array without limits of length.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

