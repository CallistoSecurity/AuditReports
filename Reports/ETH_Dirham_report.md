# Dirham Token Security Audit Report

# 1. Summary

[@openzeppelin/contracts-ethereum-package Token](https://github.com/DirhamCrypto/DirhamToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> Dirham is a fiat collateralized stablecoin backed by AED.
> It is the native to Dirham crypto where bonds are introduced to blockchain for the first time ever. Dirham holders earn 4% interest every week. Paying interest done by calling the rebase function in smart contract.

- https://dirhamcrypto.io
- https://twitter.com/DirhamCrypto

# 2. In scope

Commit `e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1`

- [Dirham.sol](https://github.com/DirhamCrypto/DirhamToken/blob/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/Dirham.sol)
- [ERC20Detailed.sol](https://github.com/DirhamCrypto/DirhamToken/blob/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/ERC20Detailed.sol)

## 2.1. Excluded
Openzeppelin library:
- [@openzeppelin/contracts-ethereum-package](https://github.com/DirhamCrypto/DirhamToken/tree/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/%40openzeppelin/contracts-ethereum-package)

# 3. Findings

In total, **5 issues** were reported including:

- 1 low severity issue.

- 4 owner privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```
2. ERC20 is a widely used standard across the Ethereum ecosystem. It is reasonable to assume that ERC20 tokens could be "accidentally" deposited to this contract even though it is not intentional.

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

## 3.2. Owner privileges

### Severity: owner privileges

### Description
1. Owner [can emit](https://github.com/DirhamCrypto/DirhamToken/blob/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/Dirham.sol#L143) fake transfer events, this could be risky if exchanges will work with this token and evaluate transfers using `Transfer` event. 
2. User with `MINTER_ROLE` [can mint](https://github.com/DirhamCrypto/DirhamToken/blob/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/Dirham.sol#L44) any amount of tokens.
3. User with `REBASER_ROLE` [can set rebase factor](https://github.com/DirhamCrypto/DirhamToken/blob/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/Dirham.sol#L72-L75) to any value without restriction and can call function [rebase()](https://github.com/DirhamCrypto/DirhamToken/blob/e4a9dc34f9020e7733a289b9b9b4a3d74daee1a1/contracts/Dirham.sol#L65-L70) as often as he wants. In this case the smart contract can't guarantee that `Dirham holders earn 4% interest every week` as was said in description.
4. Owner has `DEFAULT_ADMIN_ROLE` and can set/remove `MINTER_ROLE` and `REBASER_ROLE` to any address.


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit. 

# 5. Revealing audit reports

https://gist.github.com/MrCrambo/dd3f22539e06d502b8b678b7cc705112
