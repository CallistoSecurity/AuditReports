# Mint Manager and ERC721 Security Audit Report

# 1. Summary

[Mint Manager and ERC721](https://github.com/technine-IT/live4well-smartcontract-for-audit) smart contract security audit report performed by [GreyWolf](https://github.com/greywolf12)

# 2. In scope

Commit `bbf998a1abd71f159fcc47999e2fd432ad3cdc82`

- [MintManager.sol](https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/MintManager.sol)
- [ERC721-nftpass.sol](https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/ERC721-nftpass.sol)
- [IERC721-nftpass.sol](https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/IERC721-nftpass.sol)

# 3. Findings

In total, **2 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issues.

- 1 low severity issues.

In total, **6 notes** were reported, including:

- 2 minor observations.

- 4 owner privileges.

## 3.1. Double ownership

### Severity: low

### Description

The `NFTPass` contract use two contracts for access restriction: `Ownable` and `AccessControl`. 

If ownership will be transferred to another address in `Ownable`, the `DEFAULT_ADMIN_ROLE` will stay the same. So the new owner will not be able to [addMinter](https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/ERC721-nftpass.sol#L48) because function `grantRole` [require](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/access/AccessControl.sol#L122) the call to have admin role.
Moreover the default admin will be able to grant and role to any address calling function `grantRole` directly (without using `addMinter`).

The same applies to `MintManager` contract too (default admin can grant `PRIVATE_MINT` without being an Owner).

## 3.2. Can be minted more NFT per level than set in `quota`

### Severity: medium

### Description

The function `publicMintERC20` update `quota` variable after minting NFT. This allow to perform reentrancy attack and bypass the quota. 

### Code snippet
- https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/MintManager.sol#L97-L98

### Recommendation

Decrement `quota` before minting token.

```Solidity
        levelDetailMapping[_level].quota -= 1;
        erc721Contract.safeMint(_toAddress, _level);
```


### Recommendation

To avoid confusing better to use only `AccessControl` for access restriction. And replace `onlyOwner` modifier with `onlyRole(DEFAULT_ADMIN_ROLE)` modifier.

## 3.3. The function `_setupRole` is deprecated

### Severity: minor observation

### Description

The function `_setupRole` is deprecated in the [AccessControl](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/17c1a3a4584e2cbbca4131f2f1d16168c92f2310/contracts/access/AccessControl.sol#L204) contract and is removed from latest version: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/AccessControl.sol

### Code snippet

- https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/ERC721-nftpass.sol#L22
- https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/ERC721-nftpass.sol#L23
- https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/MintManager.sol#L25
- https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/MintManager.sol#L26
- https://github.com/CallistoSecurity/live4well-smartcontract-for-audit/blob/bbf998a1abd71f159fcc47999e2fd432ad3cdc82/smart-contract/MintManager.sol#L70

### Recommendation

Replace function `_setupRole` with the `_grantRole`.

## 3.4. Owner privileges

### Severity: owner privileges

### Description

Owner of `NFTPass` contract can:

1. Grant minter role to any address using `addMinter` or `grantRole` functions. Address with the minter role can mint any amount of NFT tokens.
2. Set or change URI for specific NFT token using function `setTokenURI` and for all tokens using function `setBaseURI`.

Owner of `MintManager` contract can:

1. Set or change price of NFT, quote, start and end time for any level even if mint period is started. 
2. Grant private mint role to any address. This role allow to mint NFT for free.

## 3.5. Follow good coding practice

### Severity: minor observation

### Description

1. Missing docstrings

The contracts in the code base need more documentation. It hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assessing security and correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality should also be documented, even if not public. Consider following the Ethereum Natural Specification Format (NatSpec) when writing docstrings.

2. Missing test suite

The contract needs a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure the contract functions and behaves as expected.

