# iOWN Token Security Audit Report

# 1. Summary

[iOWN Token](https://github.com/iOWNToken/iOWNToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> iOWN Token is an ERC20 token for iOWN project based on Openzeppelin.
It is intended work as a standard ERC-20 Utility token, to be traded on exchanges and used for payments on iOWN Platform when it is released.

> The contract has minor functionalities added to satisfy requirements: like releasing token as ODR (on demand release balance: ODR will be released later on).

https://www.iowngroup.com/

# 2. In scope

Commit hash: `c559f9ee36f1da2b9fd520a0200ee43b95ac848c`

1. [IownToken.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/IownToken.sol)
2. [CappedBurnableToken.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/CappedBurnableToken.sol)
3. [Migrations.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/Migrations.sol)
4. [TokenTreasury.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/TokenTreasury.sol)
5. [TransfererRole.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/TransfererRole.sol)
6. [UpgradeAgent.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/UpgradeAgent.sol)
7. [UpgradeableToken.sol](https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/UpgradeableToken.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 1 low severity issues.

- 2 notes.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

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

## 3.2. Owner's Privileges

### Severity: owner privileges

### Description

1. The contract owner allowed to pause and freeze functions of contract (`transfer`, `transferFrom`).
2. The owners can upgrade contract and implement any logic in the new contract.

### Code snippet

https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/UpgradeableToken.sol#L12

https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC20/ERC20Pausable.sol#L11

https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC20/ERC20Pausable.sol#L15

https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/UpgradeableToken.sol#L49


## 3.3. Not possible to use the contract upgrade

### Severity: notes

### Description

`_upgradeReady` is false by default and there is no way to change this variable. In this case, it is not possible to use the contract upgrade.

### Code snippet

https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/UpgradeableToken.sol#L33

## 3.4. Two identical checks

### Severity: notes

### Description

There are two same checks
```solidity
    require(tokenAddress != address(0), "Invalid token owner address provided");
    require(tokenAddress != address(0), "Invalid token address provided");
```

But no check of `owner` address.

### Code snippet

https://github.com/iOWNToken/iOWNToken/blob/c559f9ee36f1da2b9fd520a0200ee43b95ac848c/contracts/TokenTreasury.sol#L22-L23

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.


# 5. Revealing audit reports

https://gist.github.com/yuriy77k/20d2b459f6dbe4d6dcd43ae90d1079ec

https://gist.github.com/yuriy77k/5f53d294b69490a3691d644006f15180

https://gist.github.com/yuriy77k/90b9e897b308dd62beb4395c94fd7a0f
