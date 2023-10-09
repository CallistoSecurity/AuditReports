# 1. Summary

[Crypto-Cards](https://github.com/phatcats/cryptocards_contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Allows anyone to buy, trade and sell Digital Trading Cards over the Ethereum Network as non-fungible tokens following the ERC721 standard. Users can:

* buy packs of 8 random cards from the system (while quantities last),
* trade individual cards with other users,
* buy or sell cards with other users,
* buy or sell packs with other users.

https://www.phatcats.co/blog/how-the-trading-platform-works
https://crypto-cards.io/

# 2. In scope

Сommit hash 59d09053deeed62cf1b31ed1d756f82db62474d8.

- [CryptoCardPacks.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardPacks.sol).
- [CryptoCards.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCards.sol).
- [CryptoCardsController.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsController.sol).
- [CryptoCardsERC20.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsERC20.sol).
- [CryptoCardsERC721.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsERC721.sol).
- [CryptoCardsGum.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsGum.sol).
- [CryptoCardsLib.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsLib.sol).
- [CryptoCardsOracle.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsOracle.sol).
- [CryptoCardsTreasury.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsTreasury.sol).
- [strings.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/strings.sol).
- [usingOraclize.sol](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/usingOraclize.sol).


Сommit hash c6b9e47aa94a58b8e20bbc725ca1d32eb638e31e.

- [CryptoCardsCardToken.sol](https://github.com/phatcats/cryptocards_token_contracts/blob/master/contracts/CryptoCardsCardToken.sol).
- [CryptoCardsERC20.sol](https://github.com/phatcats/cryptocards_token_contracts/blob/master/contracts/).
- [CryptoCardsERC721.sol](https://github.com/phatcats/cryptocards_token_contracts/blob/master/contracts/CryptoCardsERC721.sol).
- [CryptoCardsGumToken.sol](https://github.com/phatcats/cryptocards_token_contracts/blob/master/contracts/CryptoCardsGumToken.sol).
- [CryptoCardsPackToken.sol](https://github.com/phatcats/cryptocards_token_contracts/blob/master/contracts/CryptoCardsPackToken.sol).
- [Migrations.sol](https://github.com/phatcats/cryptocards_token_contracts/blob/master/contracts/Migrations.sol).


# 3. Findings

In total, **3 issues** were reported including:

- 2 low severity issues.

- 1 minor observation.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.2. It is necessary to check the input address to the zero-address.

### Severity: low

### Description

In the setProxyRegistryAddress function, the input address is not checked for a null value and the funds can be transferred to a 0x0-address.

### Code snippet

https://github.com/danbogd/cryptocards_token_contracts/blob/c6b9e47aa94a58b8e20bbc725ca1d32eb638e31e/contracts/CryptoCardsERC721.sol#L35

## 3.3. Owner's privileges

### Severity: minor observation

### Description

Owner can change `gumToken` contract address,  `CryptoCardsTreasury` address, `cryptoCardPacks` address.

The same situation is in other contracts. New contracts may be no audited therefore can't be trusted.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsGum.sol#L76

https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsGum.sol#L71

# 4. Conclusion

The audited smart contract is safe to deploy. Some low severity issues were found.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0d759e6187946635579025995c87c745

https://gist.github.com/yuriy77k/bb172c5464f36048f6050d6e88a3805a

https://gist.github.com/yuriy77k/9c9e84cb091a72a05245a6db6b4c74af
