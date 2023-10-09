# OMNIS Security Audit Report

# 1. Summary

[OMNIS](https://etherscan.io/address/0x82b9eb75d6e8e6b4a296cf8fde9a7c23ee4b89ec#contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> OMNIS is the ERC20 token that brings everyone closer to the cryptocurrencies' world with its services.

https://www.omnis-bit.com

# 2. In scope

https://etherscan.io/address/0x82b9eb75d6e8e6b4a296cf8fde9a7c23ee4b89ec#contracts

# 3. Findings

In total, **10 issues** were reported including:

- 1 high severity issues.

- 3 medium severity issues.

- 1 low severity issues.

- 3 notes.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).


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

## 3.2. Non-initialized return value

### Severity: note

### Description

Functions doesn't initialize return value. As result default value will be returned. This can break the logic of the Dapp.

### Code snippet

Lines 669, 678.

## 3.3. Any admin of 2nd level can relieve the owner's rights

### Severity: medium

### Description

The `setLevel` function is available to admins of 2nd level. But it does not check the level of the administrator being changed. Therefore, administrators have the option to reset owner rights to level 0. And there is no way to restore the rights, the contract will remain without the owner.

The contract can remain locked, for example, if the owner loses rights after calling the `ownerFreeze` function.

### Code snippet

`setLevel()`, line 151


## 3.4. Doubling the amount of stake

### Severity: high

### Description

The attacker can `approve` his tokens balance to himself and make `transferFrom` *from and to* himself. Thus, he will receive a double amount in his stake-account.
Look to the comments at this example:

```Solidity
// transferFrom("0x31337", "0x31337", 1000);
    uint256 _allowance = allowed[_from][msg.sender]; // 1000
    balances[_from] = balances[_from].sub(_value); // 1000 - 1000 = 0
    balances[_to] = balances[_to].add(_value); // 0 + 1000 = 1000
    allowed[_from][msg.sender] = _allowance.sub(_value); = 1000 - 1000 = 0
    emit Transfer(_from, _to, _value);

    //STAKING RELATED//////////////////////////////////////////////
    if (transferIns[_from].length > 0) delete transferIns[_from];
    uint64 _now = uint64(now);
    transferIns[_from].push(transferInStruct(uint128(balances[_from]), _now)); // transferIns["0x31337"] = [ {"amount" => 1000, "time" => 1560088849} ]
    transferIns[_to].push(transferInStruct(uint128(_value), _now)); // transferIns["0x31337"] = [ {"amount" => 1000, "time" => 1560088849},  {"amount" => 1000, "time" => 1560088849} ]
```

### Code snippet

`transferFrom()`, lines 420-421

## 3.5. Possibility to release more tokens than `maxTotalSupply`

### Severity: note

### Description

Condition `require(totalSupply < maxTotalSupply, 'Max supply reached')` occurs before new tokens are issued. In this case it is possible to release more tokens than `maxTotalSupply`

### Code snippet

lines 286 and 452


## 3.6. Wrong interest for the 5th year

### Severity: medium

### Description

Interest rate for the 5th year is `(11 * 1e1)`. It looks like a typo but could lead to serious consequences.

#### Code snippet

line 530


## 3.7. Owner Privileges

### Severity: owner privileges

### Description

1. The contract owner allowed to freeze functions of contract (`transfer`, `transferFrom`). Line 676
2. The owner can change the `escrowFeePercent` when he want and how he want, up to 100 %. Line 357.

## 3.8. Anyone can call `refund` function

### Severity: note

### Description

Even if the refund is approved (line 842) the provider has a choice to get refund(line 807) or to release the payment(line 761). But there is no restrictions for `refund()` function and anyone call this and make the choice for the provider.

### Code snippet

`refund()`, line 807

## 3.9. It is possible to block the staker reward

### Severity: medium

### Description

An attacker can make mass transfers to the victim in the amount of 1 "wei"(minimum token amount). Because of this, a large array of stake records will be created:
```Solidity
transferIns[_to].push(transferInStruct(uint128(_value), _now)); // large number of pushes.
```

And since the calculation of rewards occurs in a cycle, in the end, there not be enough gas to get a reward. And the reward of the victim will be unavailable.

This dangerous vulnerability can lead to large losses with large balances.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f25eeb000406c68f67bc577423a18a30

https://gist.github.com/yuriy77k/5751bcd95349737543f64df841d1c998

https://gist.github.com/yuriy77k/235be19c48cc9dde23d1baa6a435f5c1
