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

Commit hash: `a63dba87af325bd8aff1531457681d452c3cac35`

1. [CryptoCardPacks.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardPacks.sol)
2. [CryptoCards.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCards.sol)
3. [CryptoCardsController.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsController.sol)
4. [CryptoCardsERC20.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsERC20.sol)
5. [CryptoCardsERC721.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsERC721.sol)
6. [CryptoCardsGum.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsGum.sol)
7. [CryptoCardsLib.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsLib.sol)
8. [CryptoCardsOracle.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsOracle.sol)
9. [CryptoCardsTreasury.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsTreasury.sol)
10. [strings.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/strings.sol)
11. [usingOraclize.sol](https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/usingOraclize.sol)


# 3. Findings

In total, **8 issues** were reported including:

- 2 medium severity issues.

- 5 low severity issues.

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

## 3.2. `CryptoCardsGum` input values checks

### Severity: low

### Description

1. `setReserveAccounts` uses only four parameters and it should be `throw` if the number of input array elements is not equal to 4. For example, now it is possible to put 5 elements there and there will be no errors. It can put you into oblivion.

2. Also, it is required check input addresses for an zero-address. Otherwise it is possible to lose tokens by sending to `0x0` address.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsGum.sol#L117-L124

## 3.3. Owner's privileges

### Severity: low

### Description

Owner can change `gumToken` contract address,  `CryptoCardsTreasury` address, `cryptoCardPacks` address.

The same situation is in other contracts. New contracts may be no audited therefore can't be trusted.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsGum.sol#L96

## 3.4. Required check for an `0x0` address

### Severity: low

### Description

It is possible to send tokens to 0x0 address by accidentally in the function `claimPackGum`.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsGum.sol#L163

### Recommendation

Use condition like `require(_to != address(0))` in the `claimPackGum` function.

## 3.5. Incorrect calculation of funds for the refund

### Severity: medium

### Description

For example, there are 20,000 tokens left for sale. And user send 1 ETH to call the `buyGum` function. Look at code and comments for get calculations results:
```solidity
    uint256 tokens = amountPaid * (baseSalePrice / (10**18)); // 1 * 10**18 * (40000 * (10**18) / (10**18)) = 40 000 * 10**18 (Gum tokens)
    uint256 refund = 0;

    // Sell only tokens that are available
    if (tokens > saleGumAvailable) {
        uint256 newTokens = saleGumAvailable; // 20000 * 10**18
        uint256 newAmount = newTokens * (baseSalePrice / (10**18)); // 20000 * 10**18 * (40000 * (10**18) / (10**18)) = 800 000 000 * 10**18 (ETH)
        refund = amountPaid - newAmount; // 1 * 10**18 - 800 000 000 * 10**18 = 1.1579209 * 10**77 (ETH)
        amountPaid = newAmount;
        tokens = newTokens;
    }

    // ...

    if (refund > 0) {
        _to.transfer(refund);
    }
```

In this case, the refund amount is ~`115 * 10^57 * 10^18` ETH (!). Of course, there will be an `not enough funds` exception.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsGum.sol#L179

### Recommendation

Use division instead multiplication on `newAmount` formula.

## 3.6. It is not possible to withdraw a referral balance by referrer

### Severity: medium

### Description

1. There is `withdrawMyReferralBalance` function that should to withdraw a referral balance of referrer that called this function. But it calls to another function `withdrawForReferrer` which has `onlySelfOrController` modifier that restrict using for common user.

2. And the same issue with the `withdrawMyMemberBalance` function.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsTreasury.sol#L188-L192

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsTreasury.sol#L260-L264

### Recommendation

Use `this.withdrawForReferrer(msg.sender)` instead `withdrawForReferrer(msg.sender)`.

## 3.7. User payout index may be reset

### Severity: low

### Description

There is no check if the user is exist. If the address of an existing user is used as an input value, its `outsourcedMembers_payoutIndex` will be reset. And he will be able to get the payments he has already received.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/a63dba87af325bd8aff1531457681d452c3cac35/contracts/CryptoCardsTreasury.sol#L140

## 3.8. Wrong calculation

### Severity: minor observation

### Description

In function  `deposit` wrong `outsourcePortion` calculation. Because as written in [comments](https://github.com/phatcats/cryptocards_contracts/blob/master/contracts/CryptoCardsTreasury.sol#L16) before the code up to 30% of received funds should be sent to `outsourcePortion`, but only 1/30 will be sent each time.

### Code snippet

https://github.com/phatcats/cryptocards_contracts/blob/9b24efceda51859aec8880fb204ab49608a3da14/contracts/CryptoCardsTreasury.sol#L322

### Recommendation

There should be mechanism to change variable for calculating percent amount, because otherwise there always will be 1/30 for `outsourcePortion` or change distribution logic.

# 4. Conclusion

The audited smart contract cannot be used. Highlighted issues have to be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/79c544bf4e01b95640ac5a5c2c50408e

https://gist.github.com/yuriy77k/a66a1453f7fb81a69dcf4cab09670834

https://gist.github.com/yuriy77k/a81415149a6c14775a53dc2b9f23d2ee

