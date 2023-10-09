# SHUSKY Security Audit Report

# 1. Summary

[SHUSKY](https://etherscan.io/address/0x236d53148f83706c3d670064809577385f923a75#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://SHUSKYTOKEN.com
- https://twitter.com/SHUSKYTOKEN
- https://t.me/SHUSKYTOKEN
- https://reddit.com/r/SHUSKYTOKEN
- https://discord.gg/XGPhdcp9nA

# 2. In scope

https://etherscan.io/address/0x236d53148f83706c3d670064809577385f923a75#code

# 2.1 Excluded

The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.

# 3. Findings

In total, **4 issues** were reported including:

- 2 medium severity issues.

- 1 low severity issues.

- 1 owner privileges.


## 3.1. Known vulnerabilities of ERC-20 and BEP-20 token

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
function rescueERC20(address _token, uint256 _amount) external onlyOwner {
    IERC20(_token).transfer(owner(), _amount);
  }
```


## 3.2. Function includeAccount() waste a Gas and has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function `includeAccount()`(lines 400-411) use the loop `for()` to find and remove address from the `_excluded` list. It requires a lot of Gas if many addresses are excluded. In the case of the long excluded list, the function will be aborted with `OUT_OF_GAS` exception.

### Recommendation

Use [EnumerableSet](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/structs/EnumerableSet.sol) instead of address array (line 500). So you could remove record using function [remove()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/243adff49ce1700e0ecb99fe522fb16cff1d1ddc/contracts/utils/structs/EnumerableSet.sol#L157).

## 3.3. Function _getCurrentSupply() has a risk of `OUT_OF_GAS` exception.

### Severity: medium

### Description

The function `_getCurrentSupply()`(lines 731-741) use the loop `for()`(lines 734-738) to calculate `rSupply` and `tSupply` values. In the case of the big length of `_excluded` list, this function will be aborted with `OUT_OF_GAS` exception. Since this function used in most other functions, the performance of the entire contract will be broken.
The risk becomes a higher due Gas cost of storage reading that may grow in future node updates. For example, after applying the EIP-1884 the cost of [the SLOAD (0x54) operation changes from 200 to 800 gas](https://eips.ethereum.org/EIPS/eip-1884). And in [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929) discussing to increase the gas cost of `SLOAD (0x54)` to 2100.
This means that even if with current `_excluded` list length contract works now, it may stop works in the future when the cost of storage reading will change.

### Recommendation

1. Try to avoid long loops. 
2. Add limitation of `_excluded` list growing into function `excludeAccount()`(lines 615-617):

```Solidity
require(_excluded.length < 50, "Excluded list too long");

```

## 3.4 Owner privileges

### Severity: owner privileges

### Description

- Owner can include (line 624) and exclude (line 615) any account.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.


