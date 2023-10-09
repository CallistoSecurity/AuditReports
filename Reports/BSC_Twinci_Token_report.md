# Twinci Token Security Audit Report

# 1. Summary

[Twinci Token](https://twinci.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> Twinci Token is a Burnable BEP20 with Fixed supply 5.000.000 tokens, it is a main token for app.twinci.io project
> The token is locked in a Vesting contract with 3.000.000 tokens in 365 days where:
> 
> - Developer: receive 25% (750.000 TWIN), every day the system will release 1/365 of 750.000 TWIN
> - Marketing: receive 40% (1.200.000 TWIN), every day the system will release 1/365 of 1.200.000 TWIN
> - Eco system: receive 35 % (1.050.000 TWIN), lock in 365 days and release one time when lock time is over
>
> Anyone can call claim methods but the receiver addresses are fixed.

- Website: https://twinci.io/ 
- FB: https://www.facebook.com/twinciio/
- Twitter: https://twitter.com/twinciio
- Telegram: https://t.me/twinciio_chat
- Instagram: https://www.instagram.com/twinci.io/
- Discord: https://discord.gg/QmZrtr6VYc
- Youtube: https://www.youtube.com/c/Twinci
- Medium: https://twinci.medium.com/
- Github: https://github.com/twinciio

# 2. In scope

1. Token: https://bscscan.com/address/0xAF83F292fcED83032f52ced45ef7DBDdb586441a#code

2. Vesting Contract: https://bscscan.com/address/0x08582ffe7df4400b2dd966fC78BC2bBe81e9b905#code


# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. Known vulnerabilities of BEP-20 token

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

## 3.2. Unnecessary gas wasting.

### Severity: note

### Description

In the contract `TwinciVestingContract` the function `erc20safeTransferFrom()` member of `ERC20TransferProxy` contract used for transfer token from contract address to destination address. But in compare with `token.transfer(to)` function it require much more Gas due using cross-contract calls and calling `approve()` function. 

Also, calling the function `erc20safeTransferFrom()` required that caller (`TwinciVestingContract` contract) should has an `Operator` role. That means that `Owner` may remove `Operator` role from `TwinciVestingContract` contract and disallow token transferring from it.


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.




