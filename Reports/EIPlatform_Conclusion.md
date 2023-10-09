# [EIPlatform](https://github.com/EIPlatform/EMI-Token/blob/master/EMI.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# Conclusion:

The contract hasn't critical vulnerabilities but has a couple of notes.

# Low severity issues:

## 1. Known vulnerabilities of ERC-20 token

### Code snippet
* [Line 161](https://github.com/EIPlatform/EMI-Token/blob/9312d6ddb537b1c3e4299522786594d47c24a94c/EMI.sol#L161)

### Description

It is possible to re-approve attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

### Recommendation

The approval of a new amount must be made only when allowance is 0 or using `increaseApproval` function.

## 2. `totalSupply` variable duplicated.

### Code snippet
* [Line 320](https://github.com/EIPlatform/EMI-Token/blob/9312d6ddb537b1c3e4299522786594d47c24a94c/EMI.sol#L320)

### Description

BasicToken already has `totalSupply_` variable. And this variable changes when `mint()` call. But another `totalSupply` variable in EMI contract means only initial supply. These two variables can be misleading and lead to errors in EIPlatform.

### Recommendation

Rename `totalSupply` variable to `initSupply` and mark it as `constant`.

## 3. Minting to zero address. 

### Description

In function [`mint`](https://github.com/EIPlatform/EMI-Token/blob/9312d6ddb537b1c3e4299522786594d47c24a94c/EMI.sol#L295) minting to zero address is possible.

### Recommendation

Need to check if `_to` address is not zero address.
```solidity
require(_to != address(0));
```



# Revealing audit reports:

https://gist.github.com/yuriy77k/ac41af28ffd395ff82f7eb4f5346cf95

https://gist.github.com/yuriy77k/ea99ff38c093b3b72c6213fc81351d64

https://gist.github.com/yuriy77k/a48617779d4ed3d8d37605d46bbb2cb5

