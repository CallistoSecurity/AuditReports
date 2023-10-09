# Ethmoji Security Audit Report

# 1. Summary

[Ethmoji](https://github.com/ProjectOpenSea/ethmoji-contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash: `f69f8f7ca5e2596ede6c4d86b3413fa62160a62f`

1. [Ethmoji.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Ethmoji.sol)
2. [Avatar.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Avatar.sol)
3. [Composable.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Composable.sol)
4. [EthmojiProxy.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/EthmojiProxy.sol)
5. [Migrations.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Migrations.sol)
6. [OwnableProxy.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/OwnableProxy.sol)
7. [Proxy.sol](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Proxy.sol)


# 3. Findings

In total, **7 issues** were reported including:

- 1 low severity issues.

- 2 notes.

- 4 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Contract EthmojiProxy is vulnerable to function selector clashing 

### Severity: note

### Description

This implements upgradable contracts based on forwarding messages to an implementation contract using DELEGATECALL.This forwarding logic is implemented in the Proxy’s fallback function, which is only called if the current message's data doesn't start with one of the Proxy's functions' selectors. This means that if the selector of one of the implementation's functions clashes with one of the Proxy's, the latter will take precedence and the message will not be forwarded. This behavior can possibly break contracts.


## 3.2. Owner Privileges

### Severity: owner previliges

### Description
Contract owner allow himself to:


1. The owner can set any value of composition fee he wants. [Code snippet](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Composable.sol#L364)
2. The owner can mint any amount of tokens with any parameters. [Code snippet](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Composable.sol#L68)
3. Pause/unpause compose function [here](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Ethmoji.sol#L33).
4. Owner can upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure [here](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/EthmojiProxy.sol#L43).

## 3.3. Zero address checking

### Severity: low

### Description

There are no zero address checking in initialize function. 

### Code snippet

https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Ethmoji.sol#L21  

## 3.4. Emoticons may not be unique

### Severity: note

### Description

One of the features of the project is the uniqueness of emoticons. As the FAQ page says:

> Is my Ethmoji unique?
> Yes! ... This means that anyone who comes along after you will not be able to create the same creation.

The uniqueness is checked by the `keccak256()` function by putting an array of layers in it and the order of the layers is important. If we change the mouth and nose places in this array — the `_isUnique` function will say that it is a different emoticon, but visually they will be exactly the same.

### Code snippet

* [_isUnique()](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Composable.sol#L345)
* [the condition for the emoticon creation](https://github.com/ProjectOpenSea/ethmoji-contracts/blob/f69f8f7ca5e2596ede6c4d86b3413fa62160a62f/contracts/Composable.sol#L123)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/8722a665f56bc944b85327578e0bc166

https://gist.github.com/yuriy77k/e83a84370e1956f280aae3c38c153283

https://gist.github.com/yuriy77k/b72d1c2af734ac98ad9cfe064ed7bbcb
