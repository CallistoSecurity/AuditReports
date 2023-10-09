# 1. Summary

[MZT1](https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

1. [MZT1.sol](https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1)

# 3. Findings

In total, **7 issues** were reported including:

- 1 high severity issues.

- 1 medium severity issues.

- 4 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1.The distribution of the pot will happen not exactly in 24 hours.

#### Severity: minor observation

#### Code snippet

* [Line 295 ](https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L295)

#### Description

The project description says that
>  Every 24 hours the pot will open and distribute profit to players who win the bet.

But the pot distribution will happen in a first action after 24 hours. But it may be much more than 24 hours. It depends on the interest in the project.

#### Recommendation

It is necessary to include this point in the project description.

## 3.2. Hardcoded number of available colors.

### Severity: low

### Code snippet

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L977

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L991

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L1014

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L541-L546

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L476

### Description

The number of available colors(`totalNumberColor_`) is 5 by default and may be changed by `setColorBlock()`. But in some parts of the code a constant value of total colors is used.

With an increase in the number of colors drawing will still be held only among the first 5 colors.

### Recommendation

First of all it should to validate `setColorBlock` input values. Limit the minimum value of `totalNumberColor_` to 5. And use `totalNumberColor_` instead hardcoded number.

## 3.3. It's easy to guess the color.

### Severity: high

### Code snippet

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L758

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L979

### Description

Anyone can check `totalColorNumber()` and `timeUpdate_` to make the right bet at last moment  before determining the winning color. The game will not be interesting in such conditions.

## 3.4. Miner can rearrange his transaction to win.

### Severity: medium

### Code snippet

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L720

### Description

Anyone can check next time of determining the winner(`_lastAddress` premium) and many will strive to be the last. But miner can rearrange his transaction in the last block to win.

## 3.5. It is possible to bet on a non-existent color.

### Severity: low

### Code snippet

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L347

### Description

No validation of the incoming color existence. It is possible to bet on the color `0` or `1000` or another number. This does not look dangerous in this implementation, but it is better to protect yourself by checking incoming value.

## 3.6. Percentage fees deducted twice.

### Severity: low

### Code snippet

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L365-L370

* https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L297-L302

### Description

The function `winnerCheck(..)` calls `potOpen(..)` function which deducts the `_toMZBoss` and `_communityDistribution` fees, but these fees also deducted later in the [`guessColorCore(..)`](https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L649-L650) function and [`buyBlock(..)`](https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e/1991daf50d45766e77d977df297e77fe39c98361#file-mzt1-L301-L302) function, as part of game participation process.

## 3.7. Possibility of erase

### Severity: low

### Description

Function [`setColorBlock`](https://gist.github.com/Mizhentaotuo/39a8d67c1028807ad5d2b56ce5103e5e#file-mzt1-L476) can decrease amount of blocks even if they all already owned.

# 4. Conclusion

Critical vulnerabilities were detected, the contract can't be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/619ce171041d855393659eaa786bc26d

https://gist.github.com/yuriy77k/c184570371488d3c69413baf165108d7

https://gist.github.com/yuriy77k/d9fe6d36ccb46c9404802746f1577eb9
