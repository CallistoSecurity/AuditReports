# Bitstars (BST) Security Audit Report

# 1. Summary

[Bitstars (BST)](https://github.com/bitstarsio/erc20-bitstars-token/blob/master/contracts/Bitstars(token)ERC20.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash: `747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d`

1. [Bitstars(token)ERC20.sol](https://github.com/bitstarsio/erc20-bitstars-token/blob/747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d/contracts/Bitstars(token)ERC20.sol)

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 1 owner privileges (ability of owner to manipulate contract, may be risky for investors).

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

### Severity: owner privileges

#### Description

The contract owner allow himself to pause functions of contract (`transfer`, `transferFrom`).

### Code snippet

https://github.com/bitstarsio/erc20-bitstars-token/blob/747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d/contracts/Bitstars(token)ERC20.sol#L707

https://github.com/bitstarsio/erc20-bitstars-token/blob/747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d/contracts/Bitstars(token)ERC20.sol#L702

## 3.3. Out of Gas

### Severity: medium

### Description

`releaseAll` function has a while-loop to unfreeze funds one by one. This can lead to "Out of Gas" error or to "Block Gas Limit". For example, for 25 freezed the cost of calling the function will be about 500000 gas. Block gas limit is about 8000000. `freezeTo` function is also uses while-loop and during the testing after more than 30 freezing the transaction was not executed.

### Code snippet

https://github.com/bitstarsio/erc20-bitstars-token/blob/747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d/contracts/Bitstars(token)ERC20.sol#L507-L511

https://github.com/bitstarsio/erc20-bitstars-token/blob/747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d/contracts/Bitstars(token)ERC20.sol#L467

https://github.com/bitstarsio/erc20-bitstars-token/blob/747eb0e7d48bd0dd7a0cb433eb9d91b0d69f735d/contracts/Bitstars(token)ERC20.sol#L537-L543

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/32673efa9ad95e62bd5050a5b75d55cd

https://gist.github.com/yuriy77k/8eee326f706683aaada2fff0b5621bd9

https://gist.github.com/yuriy77k/f8d1bdbf25d385406e6092b717d62d1f
