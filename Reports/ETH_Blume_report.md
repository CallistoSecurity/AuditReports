# Blume Token Security Audit Report

# 1. Summary

[Blume Token](https://www.blumetoken.com) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol       : BLM
	Name         : Blume Token
	Capped supply: 100,000,000
	Decimals     : 8 
	Standard     : ERC20

# 2. In scope

- [Blume Token](https://etherscan.io/address/0x2722690a68f97e3361b958eb0b1dedf39190f6ad#code).

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

## 3.2. Burn Mechanism

### Severity: low

### Description

Transfers to address 0 is used as a basic burn mechanism, however transfer to address zero can also be a result of a mistake by a user or a dapp, devs should take this issue into consideration

### Code snippet

```
    function transfer(address to, uint tokens) public returns (bool success) {
        balances[msg.sender] = safeSub(balances[msg.sender], tokens);
        balances[to] = safeAdd(balances[to], tokens);
        Transfer(msg.sender, to, tokens);
        return true;
    }
```

```
    function transferFrom(address from, address to, uint tokens) public returns (bool success) {
        balances[from] = safeSub(balances[from], tokens);
        allowed[from][msg.sender] = safeSub(allowed[from][msg.sender], tokens);
        balances[to] = safeAdd(balances[to], tokens);
        Transfer(from, to, tokens);
        return true;
    }
```

```
    function totalSupply() public constant returns (uint) {
        return _totalSupply  - balances[address(0)];
    }
```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/323b5d7a78524afb5b06f0a688b1faf4

https://gist.github.com/yuriy77k/3b1899e479d14e1271a49deb68940a0e

https://gist.github.com/yuriy77k/e22a93e1c5929c25cf8714f1a8894925
