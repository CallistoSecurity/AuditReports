# 1. Summary

[Auditchain Token](https://github.com/Vestcomp/TGE) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

https://auditchain.com/

# 2. In scope

Github commit hash 6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46

1. [Locked.sol](https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Locked.sol)
2. [MigrationAgent.sol](https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/MigrationAgent.sol)
3. [Migrations.sol](https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Migrations.sol)
4. [Token.sol](https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Token.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 5 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

## 3.2. Missing event call.

### Severity: low

### Description

In the constructor is no events on the transfer of funds.

### Code snippet

https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Token.sol#L42-L45

## 3.3. Date related issues

### Severity: low

### Description

It is about
> Issuing yearly 12,500,000 new tokens to governance contract

1. In this contract, the year is determined by dividing the number of seconds since the beginning of the Unix Epoch by the number of seconds in a leap year. In this case `06.02.2019 11:59pm` is 2018 year by `returnYear()` function. And `07.02.2019 12:00am` is 2019 year.

2. If the contract is deployed on February 6 the owner will receive 12'500'000 tokens. And the next day he can call `mint()` and get another 12'500'000 tokens because the contract will consider that the next year has come.

### Code snippet

https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Token.sol#L52

### Recommendation

Just save the timestamp of first payment and add to it year value in seconds to figure out if next payment date is come. And then update the date of the last payment.

## 3.4. There is a possibility of losing funds during the migration process

### Severity: low

### Description

In the process of migration, all funds of the sender are burned, but only those that he specified as an argument are sent to new contract. The user may not have actual information of the his balance and accidentally burn part of his funds.

### Code snippet

https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Token.sol#L86

### Recommendation

Do not use value from argument of `migrate` but set `_value = balanceOf(msg.sender);`.

### 3.5. Owners privileges

### Severity: low

### Description

The owners privileges:

* Ability to be migrated to new contract in the future (new contract may be not audited)

* Ability to lock and unlock ERC20 functions of Token based on user address

### Code snippet

https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Token.sol#L80

https://github.com/Vestcomp/TGE/blob/6ebb925a6f8bf9a744b2c6eedaf71bacf24ddf46/contracts/Token.sol#L103


# 4. Conclusion

The review of Auditchain Token smart contract did not show any critical issues, but some low severity issues were found.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/3148010d397a86f57800b9d98ef83cee

https://gist.github.com/yuriy77k/5a8b0bf7838635ccb090d664fd283eb9

https://gist.github.com/yuriy77k/c60dc5f1439c1cbebb20793db52339db
