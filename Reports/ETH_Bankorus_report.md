# Bankorus Token Security Audit Report

# 1. Summary

[Bankorus Token](https://ropsten.etherscan.io/address/0x39d6095f20e30d7280069d4913f69bccf121692b#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

	Symbol      : BKTS
	Name        : Bankorus Token
	Total supply: Mintable
	Decimals    : 18 
	Standard    : ERC20

# 2. In scope

- [Bankorus Token](https://ropsten.etherscan.io/address/0x39d6095f20e30d7280069d4913f69bccf121692b#code).

# 3. Findings

In total, **4 issues** were reported including:

- 2 low severity issues.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. Decrease Allowance

### Severity: low

### Description

`decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

```
    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
        _approve(msg.sender, spender, _allowed[msg.sender][spender].sub(subtractedValue));
        return true;
    }
```

## 3.3. High Owner Privileges

### Severity: owner privileges

### Description

1. Pause/Unpause token transfers and approvals.
2. Unlimited token minting.  

### Code snippet

```
    modifier whenNotPaused() {
        require(!_paused);
        _;
    }
```

```
    function mint(address to, uint256 value) public onlyMinter returns (bool) {
        _mint(to, value);
        return true;
    }
```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/9b9ce332054f0feedaddefa6544ad645

https://gist.github.com/yuriy77k/4e51e3d0a5818659eeacb75e2833f1be

https://gist.github.com/yuriy77k/2b0e0ebfd78ece5e912c3108c8517f1f
