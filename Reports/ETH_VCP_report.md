# VCP Token Security Audit Report

# 1. Summary

[VCP Token](https://etherscan.io/address/0x9b7922f5c51b43b59ea666ed77191c4cdbfca72f#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol      : VCP
	Name        : VCP Coin
	Total supply: 1,000,000
	Decimals    : 18 
	Standard    : ERC20

# 2. In scope

- [VCP token](https://etherscan.io/address/0x9b7922f5c51b43b59ea666ed77191c4cdbfca72f#code).

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.1. Transfer to `address(0)`

### Severity: low

### Description

Token transfers to `address(0)` are allowed by `transfer` and `transferFrom` functions. They are used as basic burn mechanism, however users might by mistake send tokens to 0x0 address and lose their tokens.

### Code snippet

```
    function totalSupply() public constant returns (uint) {
        return _totalSupply  - balances[address(0)];
    }
```
```
    function transfer(address to, uint tokens) public returns (bool success) {
        balances[msg.sender] = safeSub(balances[msg.sender], tokens);
        balances[to] = safeAdd(balances[to], tokens);
        emit Transfer(msg.sender, to, tokens);
        return true;
    }
```
```
    function transferFrom(address from, address to, uint tokens) public returns (bool success) {
        balances[from] = safeSub(balances[from], tokens);
        allowed[from][msg.sender] = safeSub(allowed[from][msg.sender], tokens);
        balances[to] = safeAdd(balances[to], tokens);
        emit Transfer(from, to, tokens);
        return true;
    }
```

### Recommendation

Do not allow transfers to 0x0 address and implement a burn function for better event handling and to avoiding token loss.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.


# 5. Revealing audit reports

https://gist.github.com/yuriy77k/2ccffd005f727f172e6b879ab9565f05

https://gist.github.com/yuriy77k/a944cbbffe7d1728eb0df95a56abe164

https://gist.github.com/yuriy77k/a2a7861fe80ec3998379aee697a876ae
