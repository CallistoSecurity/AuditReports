# Bomb3d Security Audit Report

# 1. Summary

[Bomb3d](https://bomb3d.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> BOMB3D enables BOMB holders to wrap their token to BOMB3D. The wrapped token has a standard ERC20 implementation plus approveandcall, so it enables BOMB holders to exchange their token without burn and also to exchange fractions (as BOMB has 0 decimals, BOMB3D has 18).

# 2. In scope

https://etherscan.io/address/0x6305e1cc6fb7802f01792360af2397b326662c58/#contracts

# 3. Findings

In total, **5 issues** were reported including:

- 4 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. The length of the input arrays should be compared

### Severity: low

### Description

Input arrays of functions may have different length by accidentally. This can lead to incorrect sending of funds to many recipients.

### Code snippet

* line 167

### Recommendation

Use something like `require(receivers.length == values.length)`.

## 3.3. The owner can generate bonuses

### Severity: owner privileges

### Description

The owner can generate bonuses(`50000`) for himself and get reward for it. Given the total supply of the BOMB token(1 million), this is a significant amount. This can be dangerous for investors.

### Code snippet

* line 402

## 3.4. Decrease Allowance

### Severity: low

### Description
decreaseAllowance throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

Line 215.

        function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) 
        {
        require(spender != address(0));
        _allowed[msg.sender][spender] = (_allowed[msg.sender][spender].sub(subtractedValue));
        emit Approval(msg.sender, spender, _allowed[msg.sender][spender]);
        return true;
        }

## 3.5. Zero address checking

### Severity: low

### Description

In function `unwrapTo` there is no zero address checking.

### Code snippet

* line 311

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/936515fc4752d2a577d8cd6ad7afa614

https://gist.github.com/yuriy77k/c29d8bb1c83ace363d0cf5fdd3d71575

https://gist.github.com/yuriy77k/ff642216f8fc75c234fedf58517bd35b
