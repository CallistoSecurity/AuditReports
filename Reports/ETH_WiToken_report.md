# 1. Summary

[WiToken](https://github.com/witoken/erc20) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> WiToken is a bold community-centric ecosystem involving 10 handy platforms for practical everyday use and to encourage wider adoption of cryptocurrency. It is the world’s first unified blockchain enabled e-commerce, gaming, freelancing, stock music, game skin trading, and digital advertisement ecosystem.

http://witoken.global/

# 2. In scope

- [WiT Smart Contract.sol](https://github.com/witoken/erc20/blob/master/WiT%20Smart%20Contract.sol) github commit hash bf0268fd32573314a5de392142c7402a90b4ad7e.

# 3. Findings

In total, **5 issues** were reported including:

- 2 medium severity issues.

- 3 low severity issues.

## 3.1. Owner Privileges

### Severity: medium

### Description

- Freeze & Unfreeze token transfer at any moment without any restriction to alll users.
- Freeze & Unfreeze token transfer at any moment without any restriction to a specified address.
- Unlimited token minting using `mintToken` function. Token minting mechanism should follow dedicated rules in order for the investors to trust the project and not be deceived.

### Code snippet

https://github.com/RideSolo/erc20/blob/master/WiT%20Smart%20Contract.sol#L350

https://github.com/RideSolo/erc20/blob/master/WiT%20Smart%20Contract.sol#L363

https://github.com/RideSolo/erc20/blob/master/WiT%20Smart%20Contract.sol#L392

https://github.com/RideSolo/erc20/blob/master/WiT%20Smart%20Contract.sol#L316#L331

## 3.2. Token Minting

### Severity: low

### Description

Two functions are implemented to mint tokens `mintToken` and `createTokens` both accessible externally by the owner, `createTokens` is limited by `MAX_TOKEN_COUNT` and  `mintToken` is not, the usage of both function doesn't make sense since the whole limitation is no longer applicable. `mintToken` usage cannot be justified by "future use only when we will need more tokens for our main application" as commented.

### Code snippet

https://github.com/RideSolo/erc20/blob/master/WiT%20Smart%20Contract.sol#L288

https://github.com/RideSolo/erc20/blob/master/WiT%20Smart%20Contract.sol#L316

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.4. Required check for an `0x0` address

### Severity: low

### Description

It is possible to remain out of contract control by accidentally calling `setOwner` function without parameter.

### Code snippet

* https://github.com/witoken/erc20/blob/bf0268fd32573314a5de392142c7402a90b4ad7e/WiT%20Smart%20Contract.sol#L343

### Recommendation

Use condition like `require(_newOwner != address(0))`.

## 3.5. ERC20 Compliance

### Severity: medium

### Description

From ERC-20 specification:

> The function SHOULD `throw` if the `_from` account balance does not have enough tokens to spend.

But in this implementation it just returns `false`. This can lead to serious consequences. Because checking the return value of this function is rare.
For example, external contract may use this token contract as:
```solidity
WiToken.transferFrom(recipient, this, value);
points[recipient] += value;
```
In this case recipient can get any value of points, but he may not have enough money and the code will succeed.

### Code snippet

https://github.com/witoken/erc20/blob/bf0268fd32573314a5de392142c7402a90b4ad7e/WiT%20Smart%20Contract.sol#L102

https://github.com/witoken/erc20/blob/bf0268fd32573314a5de392142c7402a90b4ad7e/WiT%20Smart%20Contract.sol#L126

### Recommendation

Use `require(...)` instead of `if ()` when check sender balance.

For example: `require (accounts [msg.sender] >= _value);` in line 102

and `require (accounts [_from] >= _value);` in line 126. 

# 4. Conclusion

The audited token contract represent a risk for the investors in case of compromised owner's private key or malicious act of the owner. Also it has problem with ERC20 Compliance.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/674f12673652be7d15311fc74c55023d

https://gist.github.com/yuriy77k/908403ea88141d8a910d9d8ad9d7395b

https://gist.github.com/yuriy77k/9fe41b77aeabcf652c769bb538e53334
