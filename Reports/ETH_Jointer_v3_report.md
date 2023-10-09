# Jointer Token v3 Security Audit Report

# 1. Summary

[Jointer Token v3](https://github.com/mak2296/JntrToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 1c121288e910fe2bb52b99d5e5fd201512ba47f6.

- [Constant.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/Constant.sol).
- [ERC20.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/ERC20.sol).
- [EtnToken.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnToken.sol).
- [EtnTokenUtils.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnTokenUtils.sol).
- [Ownable.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/Ownable.sol).
- [SafeMath.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/SafeMath.sol).
- [StandardToken.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StandardToken.sol).
- [StockToken.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockToken.sol).
- [StockTokenUtils.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockTokenUtils.sol).
- [Token.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/Token.sol).
- [TokenUtils.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/TokenUtils.sol).
- [WhiteList.sol](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol).


# 3. Findings

In total, **16 issues** were reported including:

- 1 high severity issues.

- 5 medium severity issues.

- 3 low severity issues.

- 8 owner privileges.


## 3.1. Wallet Removal (Wrong Restrictions)

### Severity: High

### Description

A whitelisted address can remove the sub wallet of other whitelisted addresses using [removeWallet](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L126). 

Instead of:
```
address primaryAddress = address_belongs[sender];
```
The implementation should be: 
```
address primaryAddress = address_belongs[_which];
```
so the `primaryAddress` of `_which` will be matching `msg.sender` address.

## 3.2. Accidentally wallet removing is possible

### Severity: medium

### Description

In `removeWallet` function, the loop searches for the address in the array of wallets corresponding to the function argument. If found, all elements are shifted to the left, replacing the wallet. This case is correct. But if the argument is the address of nonexistent wallet after the loop the last element will be deleted anyway: `details.wallets.length--;`.

### Code Snippet

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L141

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L160

## 3.3. Wallet Removal (GAS ISSUES)

### Severity: medium

### Description

`removeWallet` functions implemented [here](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L126) and [here](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L148) contains a for loop that iterates over all added wallets of the same whitelisted user that added them in order to delete a single address. Two main logical issues can be described that will cause a possible block gas limit or an out of gas error:
- The address that will be deleted can be just found using a mapping that points to its index instead of iterating through all the wallets.
- When the address is found there is no need to shift all the addresses from that point to the end, instead only the last address can be moved to the deleted address index.

## 3.4. Token Swap

### Severity: medium

### Description

When swapping tokens no security check is done since the internal `_transfer` function is used, meaning that a blocked user will be able to swap tokens, if each token contracts uses a different whitelisting contract a blocked user might be able to transfer or do other actions in the newly swapped token contract.

### Code snippet

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockToken.sol#L80

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockToken.sol#L66

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/Token.sol#L89

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/Token.sol#L75

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnToken.sol#L81

https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnToken.sol#L67

## 3.5. ERC-20 Zero Value Transfers

### Severity: medium

### Description

Following [ERC20](https://eips.ethereum.org/EIPS/eip-20) standard "Transfers of 0 values MUST be treated as normal transfers and fire the Transfer event" however in [transfer](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StandardToken.sol#L79) function member of `StandardToken` contract, a modifier is used to prevent zero value transfers, this can cause compatibility issues with possible DApps that uses `Transfer` event to handle users data.

## 3.6. ERC-20 Approval Compliance

### Severity: medium

### Description

Following [ERC20](https://eips.ethereum.org/EIPS/eip-20) standard "To prevent attack vectors like the one described [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit) and discussed [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729), clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn’t enforce it, to allow backward compatibility with contracts deployed before"

The implemented [approve](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StandardToken.sol#L89) function is enforcing the value to be the first set to zero which will cause high incompatibility issues with many DApps.

## 3.7. Receive and Send Lock

### Severity: low

### Description

[Receive and send](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L176) actions are not allowed or disallowed by address but by cluster meaning that the whitelisted address that added them have to block all the added addresses by it in order to block a single address.

## 3.8. Zero address check

### Severity: low

### Description

There are no zero address check in functions [setByPassedAddress](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L66).

## 3.9. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.10. Owner Privileges

### Severity: owner privileges

### Description

- 1- No cap is set for the owner when [minting](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StandardToken.sol#L134).
- 2- Owner can change token price:
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockTokenUtils.sol#L43
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/TokenUtils.sol#L42
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnTokenUtils.sol#L42
- 3- Owner can change token holdback days:
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockTokenUtils.sol#L76
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/TokenUtils.sol#L75
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnTokenUtils.sol#L75
- 4- Owner can change token maturity days:
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockTokenUtils.sol#L81
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/TokenUtils.sol#L80
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnTokenUtils.sol#L80
- 5- Owner or a whitelisted user can remove any added address through [addMoreWallets](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L107) using [removeWallet](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L126) or [removeWallet](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L148),resulting in users fund freezing or loss.
- 6- Owner can force swap users assets without their permission [here](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/EtnToken.sol#L94) and [here](https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/StockToken.sol#L93).
- 7- Owner can block user sending and receiving by `setReciveAndSend` function:
    - https://github.com/mak2296/JntrToken/blob/1c121288e910fe2bb52b99d5e5fd201512ba47f6/WhiteList.sol#L176
- 8- Owner can change main token address and stock token address.
    - https://github.com/mak2296/JntrToken/blob/master/EtnTokenUtils.sol#L48
    - https://github.com/mak2296/JntrToken/blob/master/EtnTokenUtils.sol#L53

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/628fa94fc4e0089820053574b7e0a5de

https://gist.github.com/gorbunovperm/c8bc1d83cc47424752ce6773ae77d50b

https://gist.github.com/MrCrambo/982d9ce8c1ad81c163a5e77542850860

https://gist.github.com/danbogd/4be6ef496f55b60b30898594c0d04fbc
