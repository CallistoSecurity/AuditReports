# KuCoin Shares Security Audit Report

# 1. Summary

[KuCoin Shares](https://etherscan.io/address/0x039b5649a59967e3e936d7471f9c3700100ee1ab#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Audit Top 200 CoinMarketCap tokens.

https://www.kucoin.com/

# 2. In scope

1. [KuCoin Shares](https://etherscan.io/address/0x039b5649a59967e3e936d7471f9c3700100ee1ab#code)

# 3. Findings

In total, **5 issues** were reported including:

- 2 medium severity issues.

- 3 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```
## 3.2. ERC20 Compliance: methods missing

### Severity: medium

### Description

In the ERC-20 standard [here](https://eips.ethereum.org/EIPS/eip-20) should be `approve`, `transferFrom`, `allowance`, `balanceOf` functions, but here its are missing.

## 3.3. ERC20 Compliance: event missing.

### Severity: low

### Description

An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

Line 33.
```js
        function MyToken(
        uint256 initialSupply,
        string tokenName,
        uint8 decimalUnits,
        string tokenSymbol
        ) {
        balanceOf[msg.sender] = initialSupply;              // Give the creator all initial tokens
        totalSupply = initialSupply;                        // Update total supply
        name = tokenName;                                   // Set the name for display purposes
        symbol = tokenSymbol;                               // Set the symbol for display purposes
        decimals = decimalUnits;                            // Amount of decimals for display purposes
        }
```

## 3.4. ERC20 compliance: zero value transfer forbidden.

### Severity: medium

### Description

`balances[_to] + value` should be able to be equal to `balance[to]` since `value` can be zero and the ERC20 standard states that transfers with value zero MUST be allowed. As it stands, this implementation threatens to break ERC20 compliance.

### Code snippet

Line 43.

```solidity
        function _transfer(address _from, address _to, uint _value) internal {
        require (_to != 0x0);                               // Prevent transfer to 0x0 address. Use burn() instead
        require (balanceOf[_from] > _value);                // Check if the sender has enough
        require (balanceOf[_to] + _value > balanceOf[_to]); // Check for overflows
        balanceOf[_from] -= _value;                         // Subtract from the sender
        balanceOf[_to] += _value;                            // Add the same to the recipient
        Transfer(_from, _to, _value);
        }

```
### Recommendation

```js
        require (balanceOf[_from] >= _value);                
        require (balanceOf[_to] + _value >= balanceOf[_to]);
```

## 3.5. ERC20 Compliance: `transfer` not returns

### Severity: low

### Description

According to ERC20 standard the `transfer` function should return bool value:

> `function transfer(address _to, uint256 _value) public returns (bool success)`

But this contract is not implemented this.

### Code snippet

* Line 53.

# 4. Conclusion

The audited smart contract has many ERC20 noncompliance and can't be used as ERC20 token.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f26078e655429fe7bf9a121623468c08

https://gist.github.com/yuriy77k/df8339da6c241ab016ef9814efd42476

https://gist.github.com/yuriy77k/8fd11672a300f961f3f0887eaf6da7df
