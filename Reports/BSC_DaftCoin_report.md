# DaftCoin Security Audit Report

# 1. Summary

[DaftCoin](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Website: https://daftco.in/

# 2. In scope

Commit `eb81a1417ff3a7009b2b6100a0f2219244f8654d`

- [DaftCoin.sol](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol)


# 2.1 Excluded

The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.

# 3. Findings

In total, **7 issues** were reported including:

- 2 medium severity issues.

- 1 low severity issues.

- 3 notes.

- 1 owner privileges.


## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

2. ERC20 is a widely used standard across the Ethereum ecosystem. It is reasonable to assume that ERC20 tokens could be "accidentally" deposited into this contract even though it is not intentional.

Every user on the entire Ethereum ecosystem can send ERC20 tokens to this contract and he will have no ability to extract it back unless there is a special "ERC20-rescue" function implemented in your contract. It is advised to implement this function.

*Example: here is [BAT contract address](https://etherscan.io/address/0x0d8775f648430679a709e98d2b0cb6250d2887ef). As you can see the contract itself holds $497,000 worth of different ERC20 tokens - all these tokens are permanently "stuck" inside the contract and therefore uselessly lost.*

### Recommendation

A simple "ERC20-rescue" function can solve the problem.

```js
interface IERC20 {
  function transfer(address _to, unit _amount);
}

function rescueERC20(address _token, uint256 _amount) external onlyOwner {
    IERC20(_token).transfer(owner(), _amount);
  }
```


## 3.2. Function includeAccount() waste a Gas and has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function [includeAccount()](https://github.com/daftcoin/contracts/blob/f5fa1245d3505a14c90d89e366ce22268efe88e8/contracts/DaftCoin.sol#L582-L589) use the loop `for()` to find and remove address from the `_excluded` list. It requires a lot of Gas if many addresses are excluded. In the case of the long excluded list, the function will be aborted with `OUT_OF_GAS` exception.

### Recommendation

Use [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) instead of [address array](https://github.com/daftcoin/contracts/blob/f5fa1245d3505a14c90d89e366ce22268efe88e8/contracts/DaftCoin.sol#L456). So you could remove record using function [remove()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/243adff49ce1700e0ecb99fe522fb16cff1d1ddc/contracts/utils/structs/EnumerableSet.sol#L157).

## 3.3. Function _getCurrentSupply() has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function [_getCurrentSupply()](https://github.com/daftcoin/contracts/blob/f5fa1245d3505a14c90d89e366ce22268efe88e8/contracts/DaftCoin.sol#L700-L710) use the loop [for()](https://github.com/daftcoin/contracts/blob/f5fa1245d3505a14c90d89e366ce22268efe88e8/contracts/DaftCoin.sol#L703-L707) to calculate `rSupply` and `tSupply` values. In case of big length of `_excluded` list this function will be aborted with `OUT_OF_GAS` exception. Since this function used in most other functions the performance of the entire contract will be broken.
The risk becomes higher due Gas cost of storage reading may grow in future node updates. For example, after applying the EIP-1884 the cost of [the SLOAD (0x54) operation changes from 200 to 800 gas](https://eips.ethereum.org/EIPS/eip-1884). And in [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929) discussing to increase the gas cost of `SLOAD (0x54)` to 2100.
This means that even if with current `_excluded` list length contract works now, it may stop works in the future when the cost of storage reading will change.

### Recommendation

1. Try to avoid long loops. 
2. Add limitation of `_excluded` list growing into function [excludeAccount()](https://github.com/daftcoin/contracts/blob/f5fa1245d3505a14c90d89e366ce22268efe88e8/contracts/DaftCoin.sol#L570):

```Solidity
require(_excluded.length < 50, "Excluded list too long");

```

## 3.4 Specification mismatch

### Severity: note

### Description

The function [includeAccount()](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L580) is intended to remove the "excluded" status from an account but it is not intended to work with accounts that are not currently excluded. The `require` error message says "Account is already excluded" which is a copy&paste of another require error text of the [excludeAccount()](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L570) function. It seems that the correct message should say "Account is not currently excluded" in the case of the `incudeAccount()` function.

### Code snippet

https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L581

### Recommendation

Update the message text.

## 3.5 Ownership can be permanently removed

### Severity: note

### Description

The renounceOwnership() function allows the owner to be removed permanently. This opens up the possibility of human error, and some steps can be taken to minimize this.

### Code snippet

https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L431-L434

### Recommendation

It is strongly advised to use a multisig as an `owner` account.

## 3.6 Function mutability specifiers

### Severity: note

### Description

Function state mutability can be restricted to `pure` instead of `view`. This applies to `name()` , `symbol()` , `decimals()` , and `_getMaxTxAmount()` functions.


## 3.7 Owner privileges

### Severity: owner privileges

### Description

- Owner can change [tax fee](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L716) and [burn fee](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L721) to any number in range 1 - 10 percents.
- Owner can [include](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L580) and [exclude](https://github.com/daftcoin/contracts/blob/eb81a1417ff3a7009b2b6100a0f2219244f8654d/contracts/DaftCoin.sol#L570) any account to reflection.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/MrCrambo/3c360ca69ec0ff989bef1555e9469ef3

https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734

## 5.1. Dexaran [report](https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734) notes

- Issue [3.2 Private variables](https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734#32-private-variables) was excluded from the final report due to using `private` variables intend not to hide something but reduce contract length and deployment cost.
- Issue [3.3 Ownership can be permanently removed](https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734#33-ownership-can-be-permanently-removed) severity was changed from `low` to `note`. We assume that the contract owner knows what he does, and does not interact with the contract directly (should be some interface). If talk about the possibility of human error, then the owner has the same risk to transfer ownership to a wrong address, but you didn't include the function `transferOwnership()` into the issues list.
- Issue [3.5 Indirect logical expression](https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734#35-indirect-logical-expression) was excluded from the final report. This part of the code is recommended by Binance for BEP20 tokens. Using `decreaseAllowance()` in that case will increase gas usage.
- Issues [3.6 Known ERC20 vulnerabilities](https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734#36-known-erc20-vulnerabilities) and [3.7 Inherent ERC20 problems](https://gist.github.com/Dexaran/ff7d0063bd25decc6d4a35ced4cee734#37-inherent-erc20-problems) combine into [3.1. Known vulnerabilities of ERC-20 token](#3.1). In addition, disabling `transfer()` tokens to the other contracts will cause problems to use tokens with multisig wallets and some other contracts like Uniswap, PancakeSwap, etc.