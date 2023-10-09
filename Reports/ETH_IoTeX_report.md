# IoTeX Security Audit Report

# 1. Summary

[IoTeX](https://member.iotex.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 13b1b1053529effce653deabfec427b70800eb42.

- [Pausable.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/lifecycle/Pausable.sol).
- [SafeMath.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/math/SafeMath.sol).
- [Ownable.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/ownership/Ownable.sol).
- [BasicToken.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/token/BasicToken.sol).
- [ERC20.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/token/ERC20.sol).
- [ERC20Basic.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/token/ERC20Basic.sol).
- [StandardToken.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/token/StandardToken.sol).
- [IoTeXNetwork.sol](https://github.com/iotexproject/IOTX-token-contracts/blob/master/contracts/IoTeXNetwork.sol).

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

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

## 3.2. Owner Privileges

### Severity: owner previliges

### Description
Contract owner allow himself to:

to pause functions of contract (transfer, transferFrom, approve, increaseApproval, decreaseApproval) [here](https://github.com/iotexproject/IOTX-token-contracts/blob/13b1b1053529effce653deabfec427b70800eb42/contracts/lifecycle/Pausable.sol#L21).

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/45a3751e1d135df8f6db33ee8df7ae9d

https://gist.github.com/yuriy77k/b350f165d69377de14d160988ba78196

https://gist.github.com/yuriy77k/b9d7f27bfc2ff25385c44431a14ea135
