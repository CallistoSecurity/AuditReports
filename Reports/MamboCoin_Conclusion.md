# [Mambocoin](https://mb24.io/docs/MamboCoin.txt) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited contracts were probably not fully tested, multiple breaches of operability were highlighted. The WP doesn't back the token minting logic, which represent a risk for investors. In conclusion, this contract is not operable and not safe.

# 2. High severity issues:

## 2.1. Token Minting

### Description

The white paper states that "A special aspect of the cryptocurrency is the absence of external influence on Mbc. Emission is possible only at the time of ICO and is not regulated by anything except investors demand."
The above statement is not true, for following multiple reasons:

- An initial token amount is minted to the contract owner, while deploying the token contract.
- At each of `createTokens` call, a token value following a certain formula is sent to the contract owner since restricted is owner ((the bought token amount + bonus)*25/75).
- Token contract owner can call `setMintAgent` to set a mint allowed address (following the WP, only the ICO address should be able to mint tokens).
- `finishMinting` is not called at any moment in the code (following the WP, it should be called by the ICO contract), which means that even after the ICO end, the token contract owner can mint tokens at his will, except if he calls `finishMinting`.

In conclusion, absence external influence of token minting is false.

### Code snippet

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L259#L264

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L369#L370

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L221#L224

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L232#L237

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L243#L249


# 3. Medium severity issues:

## 3.1. Token Transfer to Address 0x0

### Description

MBC Token do not require the `to` address to be non null before `transfer`. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L73#L78

## 3.2. Bonus Computation

### Description

- When computing the bonus `tokens`should be first multiplied by the numerator, then divided by the denominator otherwise the output will be truncated. This issue is applicable for almost every bonus computation.

### Code snippet

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L335#L365


## 3.3. ICO Phases

### Description

The time of the different ICO phases is set with a few minutes difference between them. the time set doesn't respect the project calendar.

### Code snippet

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L307#L312

### Recommendation

All dates should be changed and recalculated to correct ones.
It should be as below (not similar, it's just an example)
```solidity
endPrivateSale = now + 25 * 1 days;  // 1536624000; // (2018-09-11 00:00:00)
```
## 3.4. Wrong comparison of time. 

### Description

In [`createTokens`](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L320) function there is wrong period calculation and comparison of second and days, in lines [351](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L351), [358](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L358), [360](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L360), [362](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L362).

### Recommendation

No need of multiplying to days, because there are comparing in seconds.

## 3.5. Wrong bonus logic.

[Bonus logic](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L334) are wrong, if user will buy for more than 245 ethers he will get just 35% discount, but should get 40.
Also first if statement and third are equal they should be different and first if should have more value checking.
And also there should be bonus with 20%.

### Recommendation

In first if statement there should be max ether amount comparation and max bonus, like below.
```solidity
if (msg.value > 490 * 1 ether) {
  bonusTokens = tokens.div(2); // 50%
} else if (msg.value > 245 * 1 ether) {
  bonusTokens = tokens.div(10).mul(4); // 40%
} else if (msg.value > 160 * 1 ether) {
  bonusTokens = tokens.div(100).mul(35); // 35%
}
```



# 4. Low severity issues:

## 4.1. Approve Racing Condition

### Description

The proposed `approve` function doens't mitigate the race condition that causes the double spending attack, the proposed function will mitigate the race condition only if the user check his balance before and after setting his value to zero. 

### Code snippet

https://gist.github.com/RideSolo/fc29f2e7a619784ce08448582b81be6e#file-mambocoin-sol-L125#L136

## 4.2. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4.3. No zero address checking.

### Description

In [`constructor`](https://gist.github.com/yuriy77k/9289450a91335b968f00d613661a7c50#file-mambocoin-sol-L297) there are no zero address checking for `addressOfTokenUsedAsReward` and `addressOfMarketing`.

### Recommendation

Add zero address checking for protecting from wrong setting
```solidity
require(addressOfTokenUsedAsReward != address(0));
require(addressOfMarketing != address(0));
```


# Revealing audit reports:

https://gist.github.com/yuriy77k/82de2e6af2d212522d38f53d2cb187ff

https://gist.github.com/yuriy77k/3375b0ac574003355134e360926453f0

https://gist.github.com/yuriy77k/befcbfbb98ab03aa87338e1be171772b

