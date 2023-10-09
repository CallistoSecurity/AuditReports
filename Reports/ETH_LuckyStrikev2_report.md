# Security Audit Report

# 1. Summary

**Lucky Strike v2** [Game contract](https://ropsten.etherscan.io/address/0xd61bdebb9d831ed3e7d746dc3deef8760f4b3b86#code) and  [Tokens contract](https://ropsten.etherscan.io/address/0x9d44c76e2a31ed6e8dc693b5b51d43255cbacf40#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [LuckyStrikeTokens_v2.sol](https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18)
- [LuckyStrike_v2.sol](https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945)

# 3. Findings

In total, **12 issues** were reported including:

- 1 high severity issues.

- 3 medium severity issues.

- 5 low severity issues.

- 2 minor observation.

## 3.1. No Distributed Dividends 

### Severity: medium

### Description

Following the name `takeDividends` this function suggest that dividends are computed for every investor and can be withdrawn by them, by definition dividends are a sum of money paid regularly by the contract to its token holders out of the profit made by the game contract, 

- However the function is a sell function where tokens are sold against ether and there isn't any sort of dividends to be taken from the game contract incomes or at least just a small part. investors are at risk and can be deceived.
- Also adding the initial token distribution: 70 million tokens are initially distributed to four different addresses reducing the internal token sell price for investors since there is already 70m tokens not backed by ether into circulation.

Please note that the game contract income are sent to the token contract through this [function](https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L2132L2143).

- Once the `hardCap` or `salePeriod` reached the sale will be closed and all token minting will stop, therefore if everyone sells its tokens which is a possible case, only `withdrawAllByOwner` will be able to withdraw all the game contract income meaning that the contract owners will get all the game contract returns.

- If `hardCap` is set low enough compared to the 70M tokens distributed initially the contract owners or the four addresses first set by the owner will be guaranteed a minimum return percentage, following the actual contract, the production hard cap will be equal to 4500 ether and the token price will be 0.00015 ether meaning that only 30M tokens will be sold which will guarantee 70% of the contract returns to the four addresses set by the owner.

### Code snippet

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L164#L182

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L149#L152

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L188

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L138#L139

## 3.2. Truncated Users Ticket Ether

### Severity: low

### Description

As described previously [here](https://gist.github.com/RideSolo/153c8479fec609d30a29a8122faa1c28#34-users-ether-loss-invest--play), and even as explained by the developers the remaining ether is not sent back because of gas optimization, the issue is still applicable. 

The ticket price is set to be equal to 0.02 ether:
```
uint256 public ticketPriceInWei = 20000000000000000; // 0.02 ETH
``` 
equivalent to 2.7702 USD at the moment of writing, , knowing that a transfer function consume 2100 gas and supposing gas price to be set to 20gwei the amount to be saved will be 0.00000276 USD at the moment of writing.
The described issue expose any user to a loss of an amount lower than 2.7702 USD, the optimization is not worth the risk since 2.7702 >> 0.00000276.

Please note that the truncated ether will be lost in the contract itself.

### Code snippet

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1404

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1733

## 3.3. Truncated Investors Token Value

### Severity: medium

### Description

Lucky strike token decimals value is equal to zero, which means that no value after the decimal point is saved. As a similar issue to 3.2 when computing the token to mint for an investor, the fifth of the sent value when calling `investAndPlay` function is used to compute the investment and the other part is used to buy tickets, the fifth of the value is divided by `tokenPriceInWei` meaning that any value that is not a multiple of `tokenPriceInWei` will be truncated and the remaining value given to the marketing fund without compensation.

- Knowing that the token price in usd at the moment of writing is 0.0207, the described issue will be expose any user to a loss of value lower than 0.0207 USD and same as described before the gas optimization is not worth it.

- Since 1/5 of the value sent through `investAndPlay` is used to buy token and 4/5 is used to place a bet, therefor there will be a smaller part of ether that will be lost in the contract or taken without giving token or a fraction of it to back the full investment since `0.00015 * 5 != n tokenPriceInWei + ticketPriceInWei`.

- The cumulative value of issue 3.2 and 3.3 can be significant.

### Code snippet

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1782

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1797

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1404#L1405

## 3.4. Owner Privileges

### Severity: medium

### Description

- `adjustAllocation` function member of `LuckyStrike` allow the owner to reset the rates of the different jackpots and income rate, combined with issue 3.1 the risk for investors can be clear. 

### Code snippet

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1575#L1611

## 3.5. Game Contract Init 

### Severity: low

### Description

The ether sent through `init` member of `LuckyStrike` game contract will be lost in the contract since it is not placed in `sum` array in any way.
Depending the amount to be sent the severity can vary from low to medium.

### Code snippet

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1499

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1506 

## 3.6. Max Block Number difference

### Severity: minor observation

### Description

Since the bet is placed, the users cannot wait more than 256 blocks to play it otherwise the blockhash used as seed will be zero, however the maximum number of block allowed since the last bet is set to 250 which is reducing the playing time of the users by 6 blocks, This logic is penalizing the users with no direct reason.

### Code snippet

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1906

### Recommendation

The condition should be changed to be less than 256.

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.8. Front-Running attack

### Severity: high

### Description

The calculation of the jackpot winner in this contract is done with the help of oraclize. Calling the `requestRandomFromOraclize` function gives a random value. A random value is returned as an argument of `__callback` function. But the transaction stack can be intercepted and a random value can be obtained by the attacker before the block will be mined. The definition of the winner is as follows:

```solidity
    // _result -- argument of __callback function

    bytes32 hashOfTheRandomString = keccak256(_result);
    uint256 randomNumberSeed = uint256(hashOfTheRandomString);
    uint256 randomNumber = randomNumberSeed % ticketsTotal;

    address winner = theLotteryTicket[randomNumber];
```

Thus knowing the random number, and changing the `Total tickets` through the purchase of new tickets and substituting its transaction above the transaction with `__callback` function the attacker can manipulate the result of jackpot.

### Code snippet

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L2091

### Recommendation

Form the list of participants of the jackpot in `startJackpotPlay` function rather than in `__callback` function.

## 3.9. Zero address checking required

### Severity: low

### Description

In functions `init` member of `LuckyStrikeTokens` and `init` member of `LuckyStrike` there are no zero address checking for `luckyStrikeContractAddress`.

### Code snippet

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L133

https://gist.github.com/yuriy77k/769fc987f0e7680c70255ff999aa2945#file-luckystrike_v2-sol-L1499

### Recommendation

Add zero address checking.
```solidity
require(luckyStrikeContractAddress != address(0));
```

## 3.10. Wrong function name

### Severity: minor observation

### Description

As mentioned in function name `withdrawAllByOwner` only owner should withdraw this balance, but team address will withdraw.

### Code snippet

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L188

## 3.11. Possibility of minting more than hardCap

### Severity: low

### Description

In function `mint` there is possibility of minting more than `hardCap`. 

### Code snippet

https://gist.github.com/yuriy77k/0dd00c458d10ecc40dc553eafe4c7a18#file-luckystriketokens_v2-sol-L329

### Recommendation

You should check `(invested + _invested) > hardCap` before minting and if it's true, mint only `hardCap - invested` number of tokens and return remainder to investor.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ec08e4dc98ad15d3acf5f98fa46698f8

https://gist.github.com/yuriy77k/390c1e56bad8cfea523d27e9441a026d

https://gist.github.com/yuriy77k/027c72baabd8c4193dca80024a3b2758
