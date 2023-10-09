# Security Audit Report

# 1. Summary

[TokenStars Team Token](https://github.com/tokenstars/team-token) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Token Team of platform TokenStars

https://tokenstars.com/team

https://tokenstars.com/upload/files/team_whitepaper.pdf


# 2. In scope

Commit hash: `8efa52a4ac367ef23c961d648c3efc8a45b5739d`

1. [Migrations.sol](https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/Migrations.sol)
2. [StarTokenInterface.sol](https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/StarTokenInterface.sol)
3. [TeamToken.sol](https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamToken.sol)
4. [TeamTokenDistribution.sol](https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamTokenDistribution.sol)

# 3. Findings

In total, **4 issues** were reported including:

- 3 low severity issues.

- 1 minor observation.

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

## 3.2. No check for an empty address

### Severity: low

### Description

In the `bulkMint` function input addresses of investors is not checked for a null value and the funds can be transferred to a `0x0`-address.

### Code snippet

https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamTokenDistribution.sol#L50

## 3.3. Precision issue

### Severity: low

### Description

Solidity operates only with integers. Thus, variables multiplication should be done before the division in order to reduce the rounding errors.

### Code snippet

https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamToken.sol#L179-L180

## 3.4. Non-initialized return value

### Severity: minor observation

### Description

There is no return statement in this function that means that it always returns false by default. And this can break the logic of the DApp.

### Code snippet

https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamTokenDistribution.sol#L45

https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamTokenDistribution.sol#L58

https://github.com/tokenstars/team-token/blob/8efa52a4ac367ef23c961d648c3efc8a45b5739d/contracts/TeamTokenDistribution.sol#L66

### Recommendation

Add return statement with the right expression.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1ef45e9a2bc5e6da731d08f4b8b2677e

https://gist.github.com/yuriy77k/f60203d68faede0978c9bc5c66d07253

https://gist.github.com/yuriy77k/641f2198d845a3baf049631ba24d5416
