# Burn token Security Audit Report

# 1. Summary

[Burn token](https://theburntoken.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Burn token is a deflationary token that burns 1% of every transaction.

# 2. In scope

https://etherscan.io/address/0x4f7c5bd3f7d62a9c984e265d73a86f5515f3e92b#contracts

# 3. Findings

In total, **4 issues** were reported including:

- 3 low severity issues.

- 1 notes.

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
## 3.2. Should compared the length of the input arrays

### Severity: low

### Description

Input arrays of `multiTransfer` may have different length by accidentally. This can lead to incorrect sending of funds to many recipients.

### Code snippet

* lines 129.

### Recommendation

Use `require(receivers.length == amounts.length)`.

## 3.3. At least 1 token is burned for each transaction

### Severity: note

### Description

The `ceil` function rounds numbers up and at least 1 token is burned for each transaction. If the user sends 1 token, the recipient will not receive anything.

Given that the token does not contain decimal digits and the Total Supply is only `10'000'000` tokens this can be considered a serious problem.

### Code snippet

* lines 106.

## 3.4. Decrease Allowance

### Severity: low

### Description

The function `decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

Line 169.

        function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
        require(spender != address(0));
        _allowed[msg.sender][spender] = (_allowed[msg.sender][spender].sub(subtractedValue));
        emit Approval(msg.sender, spender, _allowed[msg.sender][spender]);
        return true;
        }


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ab14a83c1ed2465b9923362318af26f6

https://gist.github.com/yuriy77k/b865d5812342347dc1bf251d6ef6fa2a

https://gist.github.com/yuriy77k/7776d4ccd634a809fdeda42e579a247f
