# 1. Summary

[Silk Token](https://etherscan.io/address/0x9c78afb1b50170761fa2c842701e66e80e67d438#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The total amount of tokens: 15,000,000,000 SLK
The amount of circulation: 1,500,000,000 SLK

# 2. In scope

- [SilkToken.sol](https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c)

# 3. Findings

In total, **8 issues** were reported including:

- 4 medium severity issues.

- 2 low severity issues.

- 2 minor observation.

## 3.1. Presale Restrictions

### Severity: Medium

### Description

- Following the contract logic during the presale user should only be able to buy a limited amount of tokens (input of `startPreSales`), and the presale should start only after the call of `startPreSales`, however at any moment `procNormalSales` can be called without restriction and allow users to buy an unlimited amount of tokens (following the owner token balance) instead off `procNormalSales` that is restricted following the value of `isPreSales` state variable.

-  if a buyer buy a certain amount of tokens during the presale and that amount is higher than imposed limit, the `procPreSales` should revert and not just set `isPreSales` to false otherwise the amount dedicated to the presale will be surpassed.

### Code snippet

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L297#L307

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L310#L316

## 3.2. Truncated Bought Token Value

### Severity: Medium

### Description

To compute the amount of tokens bought by the investors at any phase, the msg.value if first divided by the `buyPrice` then multiplied, in consequences the buyers will lose ether since the computed value is truncated.
Depending on the buy price this issue can make the buyer lose a consequent amount of ether.

This issue is applicable for `procNormalSales`, `procPreSales` , `procNormalBuyBack` and `getTaiAMT`.

### Code snippet

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L274#L275

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L299#L300

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L311#L312

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L321#L322

### Recommendation

At first multiply by `10 ** uint256(decimals)` then divide by the `buyPrice`.  For example:
```
uint256 amount = amtETH * 10 ** uint256(decimals) / buyPrice;
```

## 3.3. Unlimited Minting

### Severity: Medium

### Description

`SilkToken` token contract is first setting an initial value of token to be credited to the contract creator address (`balanceOf[msg.sender] = totalSupply`) since it inherit from `TokenERC20` setting `totalSupply` to `initialSupply`.

however `mintToken` function allow the owner to mint an unlimited amount of tokens since no implemented logic is set to prevent this behavior in any way.

### Code snippet

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L45#L54

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L212#L217

## 3.4. Owner Privileges

### Severity: Medium

### Description

- `freezeAccount` function allow the contract owner to freeze any address and disable the transfer of the contained token value of that address.
- `setPrices` function allow the owner to change the buy and sell price of the tokens at any moment before, in or after the presale phase.
- According to the whitepaper, p.57 specified parameters of soft cup and hard cap, but in code we can't see these functions. The contract is managed manually by the owner which is not good for investors.

### Code snippet

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L222#L225

https://gist.github.com/yuriy77k/d750281258d42f6657a02ab66a46f70c#file-silktoken-sol-L230#L233

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.6. Transfer ownership to zero address.

### Severity: low

### Description

Possibility of setting zero address as newOwner at transferOwnership function.

### Recommendation

Need to check if newOwner address is not zero address.

```require(newOwner != address(0));```

## 3.7. ERC20 Compliance.

### Severity: minor observation

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.

## 3.8. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.16. It is suggested to use the latest version and fix all compiler warnings that arise.

# 4. Conclusion

The contract is not safe, the highlighted issues should be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ee3db460220fd85fb53d14080145b145

https://gist.github.com/yuriy77k/6f1be1d63256bf8e0c27391add4734f4

https://gist.github.com/yuriy77k/0470cb3f89df19f5475ec4d41300dd3d
