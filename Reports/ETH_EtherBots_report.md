# EtherBots Security Audit Report

# 1. Summary

[EtherBots](https://github.com/EtherBots/NonFungibleToken/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 8f1f1752cb2942184df695e0442b04a38f0807ba.

- [TestMintableNFT.sol](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/test/TestMintableNFT.sol).
- [DetailedERC721.sol](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/DetailedERC721.sol).
- [ERC721.sol](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/ERC721.sol).
- [Migrations.sol](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/Migrations.sol).
- [MintableNonFungibleToken.sol](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/MintableNonFungibleToken.sol).
- [NonFungibleToken.sol](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol).

# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. Wrong return value name

### Severity: notes

### Description

In functions [`name`](https://github.com/EtherBots/NonFungibleToken/blob/8f1f1752cb2942184df695e0442b04a38f0807ba/contracts/NonFungibleToken.sol#L47), [`symbol`](https://github.com/EtherBots/NonFungibleToken/blob/8f1f1752cb2942184df695e0442b04a38f0807ba/contracts/NonFungibleToken.sol#L55), [`totalSupply`](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol#L63), [`balanceOf`](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol#L71), [`ownerOf`](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol#L79), [`tokenMetadata`](https://github.com/EtherBots/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol#L87) there are return value name is different from return value.

### Recommendation

Change the return value name in brackets or delete it.

## 3.2. Token ID Re-approval

### Severity: low

### Description

Once a tokenID approved to an address the token owner cannot re-approve it to another address without resetting the approved address of the token to zero, this mechanism is used to partially solve ERC20 double withdrawal issue but in this case a token is non fungible once the token transferred from an address, the first owner do no posses it anymore and cannot reapprove it.

This implementation require two transactions to set the approved address to another address and can cause compatibility issue with Dapp that uses a non modified ERC721 standard approval function (check reference example for more information [here](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC721/ERC721.sol)).

### Code snippet

https://github.com/RideSolo/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol#L95

https://github.com/RideSolo/NonFungibleToken/blob/master/contracts/NonFungibleToken.sol#L102#L103

### Recommendation

Remove the condition that block the owner from resetting approval address for a tokens id, since it does not add any security (just extra gas consumption and complication to reset the address).

## 3.3. Known vulnerabilities

### Severity: low

### Description

Same as ERC-20, ERC-721 is vulnerable to lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7a811c673d25995b8a57d14d56ab16be

https://gist.github.com/yuriy77k/c16cb068cb75489bb31303f3e9ff2ee9

https://gist.github.com/yuriy77k/d1a1f71ce701674f137d4946c415821a
