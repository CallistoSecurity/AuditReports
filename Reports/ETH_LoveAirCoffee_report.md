# 1. Summary

[LoveAirCoffee](https://github.com/LOVEAirCoffee/tokensale/blob/master/LAC.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [LAC.sol](https://github.com/LOVEAirCoffee/tokensale/blob/master/LAC.sol) github commit hash 05f146321b43d89b4f59e965099f2c1f37d0862b.

# 3. Findings

In total, **7 issues** were reported including:

- 2 medium severity issues.

- 2 low severity issues.

- 3 minor observation.

## 3.1. Investors can make a purchase but not receive tokens.

### Severity: medium

### Description

If the token trade is open, but `transferTokenNow` is `false` then user can pay but can't get the tokens.

### Code snippet

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L72

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L97-L100

### Recommendation

Remove this condition or use another safety solution.

## 3.2 The owner can stop the movement of tokens.

### Severity: medium

### Description

The owner can `setFrozenCoin()` to true. This will block all movements of tokens until the owner starts them again. If a hacker steals the owner`s key, he could block all investors tokens. 

### Code snippet

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L125

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L132

## 3.3. `tokensPerOneEther` issues.

### Severity: low

### Description

1. Price rate in `tokensPerOneEther` is zero by default. Users will not receive anything if they make a purchase before the price is set.

2. It is necessary to check `tokensPerOneEther` for zero-value when setting a new value.

### Code snippet

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L98

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L89

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into the function `transfer(address _to, ... )` following code:
```
require( _to != address(this) );
```

## 3.5. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.18. It is suggested to use the latest version and fix all compiler warnings that arise.

## 3.6. Extra lines in code.

### Severity: minor observation

### Description

1. `uint256 public totalSupply;` already declared at contract `ERC20Basic`.
2. `require(state == State.Enabled);` used twice in fallback function.

### Recommendations

Those repeated lines may be deleted.

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L29

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L96

## 3.7. Checking values.

### Severity: minor observation

### Description

1. It is good to check initial supply at `LoveAirCoffee` constructor() for zero-value.
2. It is necessary to check `minEther` and `maxEther` in function `startBuyingTokens`, like this:

```solidity
require(minEther > 0);
require(maxEther > minEther);
```
### Code snippet

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L65

https://github.com/LOVEAirCoffee/tokensale/blob/05f146321b43d89b4f59e965099f2c1f37d0862b/LAC.sol#L73-L74

# 4. Conclusion

The smart contract has medium and low severity issues, and you have to fix it before deploying.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c00e149b8c1e69e567103ad2c0c216ca

https://gist.github.com/yuriy77k/29660da193703b5b5d6dc91064110513

https://gist.github.com/yuriy77k/8e87d19c8070d19e114dceed728fce4f
