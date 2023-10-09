# TRONDapphub  security Audit Report

# 1. Summary

[TRONDapphub](https://github.com/trondapphub/contracts/blob/3cf4e716c3e8cf2e4339657b1fbca51d9c19bc96/TDHtoken.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

TRONDapphub aims to host 100+ games in 1 Platform by 2020.
Currently, we have 60 games available to deploy by Q4 2019.
* TRONdapphub.io aims to provide both Mobile and Web Gaming experience to its
Users.
* TRONdapphub.io mission is to be the Biggest Dapp by Volume and Usage standpoint on Tron Ecosystem by hosting most Popular Traditional PVP Games like Bowling,
Billiards, Football, and Casino Games like Dice, Texas Holdem, Black Jack, Baccarat, High
Low, Let it Ride, Casino War, Slots, Keno.
* Our goal as a team is to create the fairest provability across all our games.

https://trondapphub.io/

# 2. In scope

Commit hash: `3cf4e716c3e8cf2e4339657b1fbca51d9c19bc96`

1. [TDHtoken.sol](https://github.com/trondapphub/contracts/blob/3cf4e716c3e8cf2e4339657b1fbca51d9c19bc96/TDHtoken.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issue.

- 1 note.

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

## 3.2. Extra checking

### Severity: note

### Description

There is extra checking in function `transferFrom` for zero address, because it will be checking in `_transfer` function.

### Code snippet

https://github.com/trondapphub/contracts/blob/3cf4e716c3e8cf2e4339657b1fbca51d9c19bc96/TDHtoken.sol#L106

https://github.com/trondapphub/contracts/blob/3cf4e716c3e8cf2e4339657b1fbca51d9c19bc96/TDHtoken.sol#L117

# 4. Conclusion

The audited smart contract can be deployed. Only one low severity issue was found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ce2cca3ff243fb379be63ef9cb1f3f2c

https://gist.github.com/yuriy77k/5dbc7165fc737c95517f6807cc2763e4

https://gist.github.com/yuriy77k/7e1874755c0fbc7a2214e5a19d22471e
