# SafePost Security Audit Report

# 1. Summary

[SafePost](https://www.safepost.com) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [safepost_solidity.js](https://www.dropbox.com/sh/63x0i354l1qm0bc/AAA8bXICnnMnd8t53TETJt52a?dl=0&preview=safepost_solidity.js).
- [spaccount_v.3_solidity.js](https://www.dropbox.com/sh/63x0i354l1qm0bc/AAA8bXICnnMnd8t53TETJt52a?dl=0&preview=spaccount_v.3_solidity.js).

# 3. Findings

In total, **5 issues** were reported including:

- 5 low severity issues.

No critical security issues were found.

## 3.1. Payable Fallback 

### Severity: low

### Description

`SPAccount` contract contain a fallback function that allows ether deposit to the contract, but no other function inside the contract is implemented to withdraw ether from it.

### Code snippet

spaccount_v.3_solidity.js Line 19.

```
    function () public payable {}
```

## 3.2. Not Emitted Transfer Event

### Severity: low

### Description

[ERC20 standard](https://eips.ethereum.org/EIPS/eip-20): "A token contract which creates new tokens SHOULD trigger a Transfer event with the _from address set to 0x0 when tokens are created". the same can be deducted when burning tokens.

This issue is applicable for `SafePost` contract constructor, where the initial supply is assigned but no `Transfer` event is emitted.

In `burn` and `burnFrom` a transfer event should also be emitted from the `msg.sender` to `address(0)` with a value equal to the burned tokens.
 
### Code snippet

safepost_solidity.js Line 34.

```
    function SafePost(
        uint256 initialSupply,
        string tokenName,
        string tokenSymbol
    ) public {
        totalSupply = initialSupply * 10 ** uint256(decimals);  // Update total supply with the decimal amount
        balanceOf[msg.sender] = totalSupply;                // Give the creator all initial tokens
        name = tokenName;                                   // Set the name for display purposes
        symbol = tokenSymbol;                               // Set the symbol for display purposes
    }
```

## 3.3. Transfer Returns Value

### Severity: low

### Description

Following [ERC20 standard](https://eips.ethereum.org/EIPS/eip-20) transfer function is defined as follow: `transfer(address _to, uint256 _value) public returns (bool success)`, when the implemented transfer function does not return any boolean.

### Code snippet

safepost_solidity.js Line 60.

```
    function transfer(address _to, uint256 _value) public {
        _transfer(msg.sender, _to, _value);
    }
```


## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into the function `transfer(address _to, ... )` and `transferFrom` the following code, to avoid transfers to the contract address:

```
   require( _to != address(this) );
```

## 3.5. Zero address checking

### Severity: low

### Description

There are no zero address checking in functions `transfer`, `transferFrom`, `sendToAddress` and `withdrawToAddress`.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/46cf54b64d7bbb67963db8c936c7afb8

https://gist.github.com/yuriy77k/c414ea2abe86024ec0af5bafdb063481

https://gist.github.com/yuriy77k/8f0d15550323ff3b4e7581ac11735f58
