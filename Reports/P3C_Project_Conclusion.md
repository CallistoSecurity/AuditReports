# [P3C Smart Contract](https://github.com/p3c-bot/p3c-bot.github.io/blob/master/contracts/P3C.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audit, conducted on P3C contract, concluded that the contract is safe to be used. Graphical analysis and the definition domains of the functions used for conversion (tokens to ether and ether to tokens) showed positive results. However, for better analysis the contract developers should provide the community with a white paper describing every aspect of the contract especial the conversion algorithms.

# 2. Low severity issues:

## 2.1. Known Issue of ERC20 Standard

### Description

This issue is just a reminder about ERC20 Tokens lack of transaction handling, that can cause tokens loss.
In the case of P3C contract, tokens can be accidentally sent to 0x0 address (no requirement is set to prevent it) or to a contract address not developed with the purpose of handling ERC20 tokens.

### Recommendation

Need to check if `_toAddress` address is not zero address.
```solidity
require(_toAddress != address(0));
```

# 3. Minor observation.

## 3.1. Fees avoidance.

### Description
There is a condition which checks if the purchaser address is not same as the referrer address, but this may be easily bypassed by registering a different account on the same user.

### Code snippet  
https://github.com/p3c-bot/p3c-bot.github.io/blob/master/contracts/P3C.sol#L536

## 3.2. Maybe incorrect 'buy price' estimation.

### Description

The 'buyPrice' function uses the 'tokensToEthereum_' function as the buy price estimator , but the ''tokensToEthereum_' function may act substantially different from the 'ethereumToTokens_' function in specific circumstances.

#### Code snippet  
https://github.com/p3c-bot/p3c-bot.github.io/blob/master/contracts/P3C.sol#L464


# Revealing audit reports:

https://gist.github.com/yuriy77k/4046d684441080fe1b312e631512123e

https://gist.github.com/yuriy77k/e6ce76b130a24906ef551a8d070eb0d4

https://gist.github.com/yuriy77k/873d7db0e3197070fa30eefe04c32d2a

