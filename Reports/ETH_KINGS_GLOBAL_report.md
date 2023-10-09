# KINGS GLOBAL Security Audit Report

# 1. Summary

[KINGS GLOBAL](https://etherscan.io/address/0xfda1f5278b9aa923b5e581565d599810c78c71f5#contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> KINGS GLOBAL is an Erc20 token use for Charity, Welfare, Shopping, Social, Media gallery for payment and royalty reward within the community and entire crypto space.

# 2. In scope

https://etherscan.io/address/0xfda1f5278b9aa923b5e581565d599810c78c71f5#contracts

# 3. Findings

In total, **5 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

No critical security issues were found.

## 3.1. ERC20 Compliance: false instead of throw

### Severity: medium

### Description

From ERC-20 specification:

The function SHOULD throw if the _from account balance does not have enough tokens to spend.

But in this implementation it just returns false. This can lead to serious consequences. Because checking the return value of this function is rare.

### Code snippet

```solidity

function transfer(address _to, uint256 _value) returns (bool success) {
    
        if (balances[msg.sender] >= _value && _value > 0) {
            balances[msg.sender] -= _value;
            balances[_to] += _value;
            Transfer(msg.sender, _to, _value);
            return true;
        } else { return false; }
    }

    function transferFrom(address _from, address _to, uint256 _value) returns (bool success) {
     
        if (balances[_from] >= _value && allowed[_from][msg.sender] >= _value && _value > 0) {
            balances[_to] += _value;
            balances[_from] -= _value;
            allowed[_from][msg.sender] -= _value;
            Transfer(_from, _to, _value);
            return true;
        } else { return false; }
    }


```

## 3.2. ERC20 Compliance — zero-value transfers rejecting

### Severity: low

### Description

EIP20 says that:  Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event.

    But in this contract, function transfer has a condition:

_value > 0

### Code snippet

```solidity

function transfer(address _to, uint256 _value) returns (bool success) {
    
        if (balances[msg.sender] >= _value && _value > 0) {
            balances[msg.sender] -= _value;
            balances[_to] += _value;
            Transfer(msg.sender, _to, _value);
            return true;
        } else { return false; }
    }

    function transferFrom(address _from, address _to, uint256 _value) returns (bool success) {
     
        if (balances[_from] >= _value && allowed[_from][msg.sender] >= _value && _value > 0) {
            balances[_to] += _value;
            balances[_from] -= _value;
            allowed[_from][msg.sender] -= _value;
            Transfer(_from, _to, _value);
            return true;
        } else { return false; }
    }


```

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

## 3.4. Checking input addresses

### Severity: low

### Description

Incoming addresses should be checked for an empty value(`0x0` address) to avoid loss of funds or blocking some functionality.

### Code snippet

* `transfer()`, line 47
* `transferFrom()`, line 57

## 3.5. ERC20 Compliance: event missing

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a `Transfer` event should be emitted.
An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

* `KINGSGLOBAL()`, line 107

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/8eb8f92679114ba92de78a1af5444de8

https://gist.github.com/yuriy77k/780e1e15da219c1414a72e04ec9d2d82

https://gist.github.com/yuriy77k/2399fc5377d29ab1cd59f1fe531b6c0e
