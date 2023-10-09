# Twogap Token Security Audit Report

# 1. Summary

[Twogap Token](https://etherscan.io/address/0xf96aa656ec0e0ac163590db372b430cf3c0d61ca#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol      : TGT
	Name        : Twogap Token
	Total supply: 210,000,000,000
	Decimals    : 18 
	Standard    : ERC20

# 2. In scope

- [Twogap Token](https://etherscan.io/address/0xf96aa656ec0e0ac163590db372b430cf3c0d61ca#code).

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. Bounty Lock

### Severity: low

### Description

If an address receives bounties twice or more the bounties value of the address is not accumulated, meaning that the vesting logic implemented in transfer function will not lock the bounties allowed before the last call to `bountyWithdraw`.

### Code snippet

```
    function bountyWithdraw(address to, uint256 value) public onlyOwner returns (bool) {
        withdraw(to, value);
        bounties[to] = value;
        return true;
    }
```

```
    function _transfer(address from, address to, uint256 value) internal {
        require(to != address(0));
        if (block.timestamp < bountyLockTime3Month) {
            require(_balances[from].sub(value) >= bounties[from]);
        } else if(block.timestamp < bountyLockTime6Month) {
            require(_balances[from].sub(value) >= (bounties[from] * 9 / 10));
        } else if(block.timestamp < bountyLockTime12Month) {
            require(_balances[from].sub(value) >= (bounties[from] * 6 / 10));
        }
        _balances[from] = _balances[from].sub(value);
        _balances[to] = _balances[to].add(value);
        emit Transfer(from, to, value);
    }
```

### Recommendation

change `bounties[to] = value` to `bounties[to] = bounties[to].add(value)`

## 3.2. Decrease Allowance

### Severity: low

### Description

`decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

```
    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
        require(spender != address(0));

        _allowed[msg.sender][spender] = _allowed[msg.sender][spender].sub(subtractedValue);
        emit Approval(msg.sender, spender, _allowed[msg.sender][spender]);
        return true;
    }
```

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add the following code to the `function _transfer(address from, address to, uint256 value)` function:

```
require( _to != address(this) );
```

# 4. Conclusion

The audited contract is safe, however issue 3.1 should be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ed783fa4761a26d4e917eb5eb6c500ad

https://gist.github.com/yuriy77k/c5aea1e544c1cc2557f391cbe17b154c

https://gist.github.com/yuriy77k/0cfd9435161debfc66bcca612b83f0f5
