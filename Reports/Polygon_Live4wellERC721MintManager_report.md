# Live4well ERC721 token and Mint Manager Security Audit Report

# 1. Summary

[Live4well](https://github.com/technine-IT/live4well-smartcontract-for-audit) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/CallistoSecurity/Smart-contract-auditing)

# 2. In scope

Commit `ade227c74405b4b50055fc4f63786ad66efaecb4`

- [MintManager.sol](https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol)
- [ERC721-nftpass.sol](https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/ERC721-nftpass.sol)
- [IERC721-nftpass.sol](https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/IERC721-nftpass.sol)

# 3. Findings

In total, **4 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issue.

- 3 low severity issues.

In total, **14 notes** were reported, including:

- 4 minor observations.

- 10 owner privileges.


## 3.1. Function `publicMintERC20()` is vulnerable to re-entrancy.

### Severity: medium

### Description

The function `publicMintERC20()` allows users to mint ERC-721 tokens in exchange for the ERC-20 tokens. The users can mint tokens up to the `quota` limit determined by the owner for the level. The function `publicMintERC20()` calls the external function `erc721Contract.safeMint()` and the function `safeMint()` checks whether the tokens are not unintentionally minted to a contract by checking first whether that contract has implemented ERC721Receiver, i.e. marking itself as a willing recipient of NFTs. But the call `_checkOnERC721Received()` is an external call to the receiving contract, allowing arbitrary execution. Allowing users to mint ERC-721 tokens exceeding the quota.


To avoid this issue the `quota` should be decreased before minting the token.
```Solidity
        levelDetailMapping[_level].quota -= 1;
        erc20Contract.safeTransferFrom(msg.sender, address(this), levelDetailMapping[_level].unitPriceERC20[_erc20Address]);
        erc721Contract.safeMint(_toAddress, _level);
```

### Code Snippet

- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L80
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L97-L98
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/ERC721-nftpass.sol#L38-L46
- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC721/ERC721.sol#L305-L307
- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/token/ERC721/ERC721.sol#L313-L317

### Recommendation

Consider these protective measures to prevent re-entrance attacks:

- Make sure all internal state changes are performed before the call is executed. This is known as the [Checks-Effects-Interactions pattern](https://solidity.readthedocs.io/en/latest/security-considerations.html#use-the-checks-effects-interactions-pattern)

- Use a reentrancy lock (ie. [OpenZeppelin's ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol


## 3.2. Double ownership

### Severity: low

### Description

The `NFTPass` contract uses two contracts for access restriction: `Ownable` and `AccessControl`. 

If ownership will be transferred to another address in `Ownable`, the `DEFAULT_ADMIN_ROLE` will stay the same. So the new owner will not be able to [addMinter](https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/ERC721-nftpass.sol#L48) because function `grantRole` [require](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/access/AccessControl.sol#L122) the call to have admin role.
Moreover, the default admin will be able to grant any role to any address calling function `grantRole` directly (without using `addMinter`).

The same applies to `MintManager` contract too (default admin can grant `PRIVATE_MINT` without being an Owner).

## 3.3. Apply the Checks-Effects-Interactions pattern

### Severity: low

### Description

According to the Solidity documentation, it is recommended to follow the following pattern[ Checks-Effects-Interactions](https://docs.soliditylang.org/en/v0.4.21/security-considerations.html#use-the-checks-effects-interactions-pattern). 

### Code Snippet
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L110

### Recommendation
Consider updating the following statement in the function`privateMint`

```solidity
erc721Contract.safeMint(_to, _level);
levelDetailMapping[_level].quota -= 1;
```

to

```solidity
levelDetailMapping[_level].quota -= 1;
erc721Contract.safeMint(_to, _level);
```

## 3.4. There is no check for array length and zero quota value.

### Severity: low

### Description

In the `initialiseNFTLevelInfo` function, the length of the address and price arrays is not checked, and there is no check for a null quote value.

### Code Snippet

- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L29

### Recommendation
Add length checking of the `_erc20AddressList` and `_price` arrays for the same length and empty array value and the null value of `_quota`

## 3.5. Owner Privileges

### Severity: owner privileges

### Description

The `ERC721-nftpass.sol` and `MintManager.sol` contract inherits features of the OpenZeppelin [AccessControl](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol) contract, allowing the administrator to manage administrators. This role can be abandoned, and it will result in blocking access to critical functions of the contract.

- [NFTPass](https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/ERC721-nftpass.sol)
1. The `safeMint` function allows any user with `MINT_ROLE` permissions to mint an unlimited number of NFTs.
2. The `addMinter` function allows the administrator to grant NFT minter role to any address.
3. Function `setBaseURI` allows the owner to modify the `baseURI_` of the NFT tokens.
4. Function `setTokenURI()` allows owner to override the `baseURI_` of NFT tokens to custom URI.

- [MintManager](https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol)
1. The `privateMint` function, allows any address with `PRIVATE_MINT` rights, to min any number of NFTs for free, depending on the quota.
2. The `changeLevelPeriod` function, allows the administrator to change the time range for a mint
3. Function `changeLevelquota`, allows the administrator to change the quota for the NFT mint
4. Function `addNewERC20Price`, allows the administrator to add any ERC20 tokens including poisoned tokens as payment for NFTs
5. Function `setPrivateMintRole`, allows the administrator to give rights to any address to allow private minting.
6. The `transferERC20Token` function allows the administrator to sign out any tokens from the contract.

### Recommendation

Since the owner has unlimited rights to do everything, the ownership must be transferred to a multi-sig contract. And also the addresses to whom the rights `PRIVATE_MINT`, `MINT_ROLE` are given must be trusted.


## 3.6. Optimization of variables

### Severity: minor observation

### Description

The `quota` variable, can be converted from `uint256` to `uint128` value, which will be almost impossible to achieve. Moreover, the variables `startTime` and `endTime` can be packed, `uint64` since the timestamps can be mapped `uint64` to many years to come.

## 3.7. The ERC-20 tokens price is set manually

### Severity: minor observation

### Description

The functions `initializeNFTLevelInfo()` and `addNewERC20Price()` are used by the owner to set the price of ERC-721 tokens in ERC-20 tokens. For non-stable coins, the value might fluctuate and the prices are required to be updated in case of a change in value. Allowing users to mint the ERC-721 token at an increased or decreased value depending on the price fluctuation.

### Code Snippet

- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L36
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L66

### Recommendation

Consider using a price oracle to get the current value of the ERC-20 tokens and convert the value to a stablecoin to determine the amount of ERC-20 tokens required to mint an NFT by a user.

## 3.8. The function `_setupRole` is deprecated

### Severity: minor observation

### Description

The function `_setupRole` is deprecated in the [AccessControl](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/17c1a3a4584e2cbbca4131f2f1d16168c92f2310/contracts/access/AccessControl.sol#L204) contract and is removed from latest version: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol

### Code snippet

- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/ERC721-nftpass.sol#L22
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/ERC721-nftpass.sol#L23
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L25
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L26
- https://github.com/technine-IT/live4well-smartcontract-for-audit/blob/ade227c74405b4b50055fc4f63786ad66efaecb4/smart-contract/MintManager.sol#L70

### Recommendation

Replace function `_setupRole` with the `_grantRole`.

## 3.9. Follow good coding practice

### Severity: minor observation

### Description

1. Unlocked Pragma.

Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the floating pragma, i.e. by not using ^ in pragma solidity ^0.8.4, ensures that contracts do not accidentally get deployed using a compiler version with unfixed bugs.

2. Missing docstrings.

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

3. Missing test suite.

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.

# 4. Security practices

- [ ] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is withdrawn in a short period until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native tokens deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed before the usage of this contract.

Users should pay attention to unlimited owner's rights.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Revealing audit reports

- https://gist.github.com/chhajershrenik/f66a2379b6f02e2d47699633f309de49
- https://gist.github.com/oipkasec/d45068163d94b4bdf807e79633926de5
- https://gist.github.com/greywolf12/3bc0c04a714c25e7efe0a3fb4402dae6
