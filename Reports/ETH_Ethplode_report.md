# Ethplode Security Audit Report

# 1. Summary

[Ethplode](https://ethplode.org) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol       : ETHPLO
	Name         : ETHplode
	Capped supply: 100,000,000
	Decimals     : 6 
	Standard     : ERC20

# 2. In scope

- [Token.sol](https://github.com/ETHplode/ETHplode-Source/blob/master/Token.sol) github commit hash ef823e10eeb853e669eb4c50b4c1672e019e2a0c.

# 3. Findings

In total, **4 issues** were reported including:

- 1 high severity issues.

- 1 medium severity issues.

- 2 low severity issues.

## 3.1. 0.5% Token Burning

### Severity: High

### Description

The token is implemented to burn 0.5% of the token transferred on every transaction, However in `transfer`:

```
    function transfer(address to, uint _tokens) public returns (bool success) {
        
        uint tokensBurn =  (_tokens/200);
        uint readyTokens = safeSub(_tokens, tokensBurn);
        burn(owner, tokensBurn);
        
        balances[msg.sender] = safeSub(balances[msg.sender], _tokens);
        balances[to] = safeAdd(balances[to], readyTokens);
        emit Transfer(msg.sender, to, readyTokens);
        return true;
    }
```

The tokens are added correctly to the new address subtracting 0.5% but when calling the `owner` address inputted where it should be the msg.sender address, once the owner balance is empty the transfers will freeze since `transfer` will throw at every execution.

Please note that this will cause compatibility issues with many dapps.

### Recommendation

```
    function transfer(address to, uint _tokens) public returns (bool success) {
        
        uint tokensBurn =  (_tokens/200);
        uint readyTokens = safeSub(_tokens, tokensBurn);
        burn(msg.sender, tokensBurn);
        
        balances[msg.sender] = safeSub(balances[msg.sender], readyTokens);
        balances[to] = safeAdd(balances[to], readyTokens);
        emit Transfer(msg.sender, to, readyTokens);
        return true;
    }
```

## 3.2. Transfer From

### Severity: medium

### Description

Users can avoid to burn 0.5% by using `transferFrom` since no mechanism is implemented to burn 0.5% on a transfer from transaction.

### Recommendation

Implement the same logic recommended in 3.1 for `transferFrom` function. 

## 3.3. Transfers to Address(0)

### Severity: low

### Description

`transfer` and `transferFrom` allow transfers to address 0, this issue has caused millions of losses for many tokens.

Developers should add a requirement to avoid `_to` address to be equal to zero.

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ae964fd6b195bc6fcefb89f93f82fd65

https://gist.github.com/yuriy77k/eb9cad6b0fe48e929798de11e1540d67

https://gist.github.com/yuriy77k/b2ff6b3cf50913dbad4645792b5bb103
