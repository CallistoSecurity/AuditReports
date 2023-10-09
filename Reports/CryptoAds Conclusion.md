# [CryptoAds smart contract](https://etherscan.io/address/0x3637fcD710B99d2B643aF653d60b762d6C310A8B#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# Conclusion:

No critical issue, bug fixing is necessary. It is highly recommended to complete a bug bounty before use.


## Medium severity issues:

### 1. Zero address owner. 

#### Description

Owner address may be sent to `zero address` at function [`setOwner`](https://gist.github.com/yuriy77k/6f636b466df04c8ae0cc24e56b28e51a#file-cryptoads-sol-L417) and because of it owner will lose his access to smart contract.

#### Recommendation

Need to check if `_newOwner` is not zero address.
```solidity
require(_newOwner != address(0));
```


### 2. Transfer to zero address. 

#### Severity: medium

#### Description

Tokens could be sent to zero address, that means they will be locked and will not be able to use or burn. Functions [`transfer`](https://gist.github.com/yuriy77k/6f636b466df04c8ae0cc24e56b28e51a#file-cryptoads-sol-L193) and [`transferFrom`](https://gist.github.com/yuriy77k/6f636b466df04c8ae0cc24e56b28e51a#file-cryptoads-sol-L214).

#### Recommendation

Need to check if `_to` address is not zero address.
```solidity
require(_to != address(0));
```


### 3. Double-spend attack is possible.

#### Severity: medium

#### Description

In case the user wants to change the approved amount an double-spend attack is possible.

#### Recommendation

Can be reviewed [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit#).



# Revealing audit reports:

https://gist.github.com/yuriy77k/0642bdfd140789929904373bf3564123

https://gist.github.com/yuriy77k/df7339f021bed8366ed40da915da1a8f

