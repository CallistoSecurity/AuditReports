# Pundi X Security Audit Report

# 1. Summary

[Pundi X (NPXS) Token](https://pundix.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

1. [NPXSToken.sol](https://etherscan.io/address/0xa15c7ebe1f07caf6bff097d8a589fb8ac49ae5b3#contracts)

# 3. Findings

In total, **9 issues** were reported including:

- 5 low severity issues.

- 1 notes.

- 3 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. Owner Privileges

#### Severity: owner privileges

#### Description

1. The owner receives funds immediately after the purchase of tokens. It should be available after the end of crowdsale. Otherwise, the owner has the opportunity to buy tokens for the same money. (line 149). `RefundVault` and `WithdrawVault` contracts is managed manually by the owner and are not integrated with the `Crowdsale` contract  which is not good for investors. (lines 200 and 446)

2. The contract owner allowed to pause transfer functions (`transfer`, `transferFrom`). (lines 541-545)

3. The owner can finish minting whenever he wants. Related with issue #4. (line 401)

## 3.3. ERC20 Compliance: event missing

### Severity: low

### Description

According to ERC20 standard when burning coins a transfer event should be emitted. In this case the `Burn` event is emitted, it is better to additionally use `emit Transfer(msg.sender, address(0), value);`

### Code snippet

* [burn(), line 363](https://etherscan.io/address/0xa15c7ebe1f07caf6bff097d8a589fb8ac49ae5b3#contracts)

## 3.4. `stopReceive` is not implemented for `transferFrom`

### Severity: low

### Description

`stopReceive` mechanism is used for `transfer` function but not implemented for `transferFrom` function. It is possibler to `approve` funds to yourself and send through `transferForm` without any restrictions.

### Code snippet

* [transfer(), line 611](https://etherscan.io/address/0xa15c7ebe1f07caf6bff097d8a589fb8ac49ae5b3#contracts)

## 3.5. ERC223 Compliance, transferAndCall issues

### Severity: note

### Description

1) Judging by the call of the `tokenFallback` function, this token should support the ERC223 standard. But in this case it should be called in the `transfer` function also. And should to check whether the recipient is a contract. And function `transfer(address, uint, bytes)` should be implemented. Please, read [this](https://github.com/ethereum/EIPs/issues/223)

2) Judding by `transferAndCall` function this token should support the ERC677 standard. But in this case it should call `contractFallback` not `tokenFallback` function. And should to check whether the recipient is a contract. Please, read [this](https://github.com/ethereum/EIPs/issues/677)

3) `transferAndCall` function not use `whenNotPaused` modifier and **tokens may be transferred** even if contract is paused.

### Code snippet

* [transferAndCall(), line 645](https://etherscan.io/address/0xa15c7ebe1f07caf6bff097d8a589fb8ac49ae5b3#contracts)

## 3.6. Zero address checking

### Severity: low

### Description

There are no zero address checking in functions `deposit` at line 212, `mint` at line 389, `deposit` at line 465

## 3.7. Allowance Approval

### Severity: low

### Description

Following ERC-20 final [description](https://eips.ethereum.org/EIPS/eip-20):

"NOTE: To prevent attack vectors like the one [described here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit) and discussed [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729), clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn't enforce it, to allow backwards compatibility with contracts deployed before.

Do not throw in case if the following condition is true require((value == 0) || (_allowances[msg.sender][spender] == 0)) and return false, users might not notice that the changes didn't occur, and external contract calls to this function will highlight many other issues.

### Code snippet
```solidity
function approve(address _spender, uint256 _value) returns (bool) {

    require((_value == 0) || (allowed[msg.sender][_spender] == 0));

    allowed[msg.sender][_spender] = _value;
    Approval(msg.sender, _spender, _value);
    return true;
  }
```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ede3912b0498e812eecd528acbe37137

https://gist.github.com/yuriy77k/72b57e6c662bf0413e4ad11190db1977

https://gist.github.com/yuriy77k/76d020a1607a9483b7dba267eb36b36d
