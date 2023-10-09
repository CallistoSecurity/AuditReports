# ETHERC Security Audit Report

# 1. Summary

[ETHERC](https://github.com/ETHERCIO/smart_contract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ETHERCExchange.sol](https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCExchange.sol) github commit hash 8f3739db93588b5141b7eb0116ebaeb303263569.
- [ETHERCFeeModifiers.sol](https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCFeeModifiers.sol) github commit hash 8f3739db93588b5141b7eb0116ebaeb303263569.
- [ETHERCToken.sol](https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCToken.sol) github commit hash c20d7c768bd6f98cef2f3169a954de3622395a30.
- [Migrations.sol](https://github.com/ETHERCIO/smart_contract/blob/master/contracts/Migrations.sol) github commit hash 8f3739db93588b5141b7eb0116ebaeb303263569.
- [TokenStandard.sol](https://github.com/ETHERCIO/smart_contract/blob/master/contracts/TokenStandard.sol) github commit hash 8f3739db93588b5141b7eb0116ebaeb303263569.
- [TokenTemplate.sol](https://github.com/ETHERCIO/smart_contract/blob/master/contracts/test/TokenTemplate.sol) github commit hash 8f3739db93588b5141b7eb0116ebaeb303263569.

# 3. Findings

In total, **4 issues** were reported including:

- 1 high severity issues.

- 1 low severity issues.

- 1 notes.

- 1 owner privileges (ability of owner to manipulate contract, may be risky for investors).

## 3.1. Burn From

### Severity: high

### Description

The function `burnFrom` In `TokenTemplate` contract, allow an address to burn from another address that has approved token to it, however the burned value is not subtracted from the allowance once the function is executed, making the spender able to burn the total balance of the from address. 

The severity of this issue cannot be defined accurately since the usage of the contract containing the issue should be described by the development team, in all the cases the contract should be corrected since the repository is public.

### Code snippet

https://github.com/ETHERCIO/smart_contract/blob/master/contracts/test/TokenTemplate.sol#L85

## 3.2. Owner Privileges:

### Severity: owner privileges

### Description

1. In `ETHERCExchange.` contract the owner is allowed to change maker and taker fees without restrictions.
2. In `ETHERCToken` contract the owner is allowed to pause/unpause token transfers and approvals without restrictions.

### Code snippet

https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCExchange.sol#L312

https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCExchange.sol#L317

https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCToken.sol#L308#L329

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Code snippet

https://github.com/ETHERCIO/smart_contract/blob/master/contracts/ETHERCToken.sol

https://github.com/ETHERCIO/smart_contract/blob/master/contracts/test/TokenTemplate.sol

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.4. Deprecated method.

### Severity: note

### Description

The function () payable { revert(); } was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded.

### Code snippet

https://github.com/ETHERCIO/smart_contract/blob/f6835404b957ec7edc724107260e7ccfdfb6e743/contracts/ETHERCExchange.sol#L157-L159

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/0851796133f03a9d2d8230027fa85e25

https://gist.github.com/yuriy77k/9d3b71bf3e00f1175a566a0bb2aee2ac

https://gist.github.com/yuriy77k/b0043bd10f7b310f38824dc30e37f038
