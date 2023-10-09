# Jointer token v2 Security Audit Report

# 1. Summary

[Jointer token v2](https://github.com/mak2296/JntrToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16.

- [Constant.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/Constant.sol).
- [ERC20.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/ERC20.sol).
- [Ownable.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/Ownable.sol).
- [SafeMath.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/SafeMath.sol).
- [StandardToken.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/StandardToken.sol).
- [Token.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/Token.sol).
- [TokenUtil.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol).
- [WhiteList.sol](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/WhiteList.sol).

# 3. Findings

In total, **12 issues** were reported including:

- 2 medium severity issues.

- 4 low severity issues.

- 1 notes.

- 5 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. The function `forceSwap` burns all tokens

### Severity: medium

### Description

When [force swap](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/Token.sol#L148) is used the owner can choose any amount to be sent to the user address without any relation with the user balance while **burning all the user balance**, but the user can swap only the part of his tokens.

## 3.2. ERC-20 Approval Compliance

### Severity: medium

### Description

The implemented [approve](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/StandardToken.sol#L86) function is not ERC20 compliant any dApp, exchange or dex that were deployed previously or in the future to handle ERC20 [approval](https://eips.ethereum.org/EIPS/eip-20#approve) will not work with this implementation of ERC20 token.

Even the [approval event](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/ERC20.sol#L21) is not [compliant](https://eips.ethereum.org/EIPS/eip-20#approval) since it send 4 parameters, meaning that a dApp will not detect the approval event correctly.

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.4. Token Maturity

### Severity: low

### Description

Following the whitepaper definition "The token JNTR/ETN has a 10 year maturity date", however the maturity date in the contract is set to [one year](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L17)(a setter exist and can be used to update the date but the contract should reflect the whitepaper, in case if the owner forget to call that function after a year the transfers will stop).

## 3.5. Receive and Send Lock

### Severity: low

### Description

When owner or system address [allow or block](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/WhiteList.sol#L104) a whitelisted address from [receiving](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/WhiteList.sol#L126) or [sending](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/WhiteList.sol#L117) tokens, they have to block or allow all the addresses that were added through [addMoreWallets](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/WhiteList.sol#L81) by a whitelisted address.

Most tokens implement the locking mechanism to block a single address in case of stolen fund, however here a single address cannot be locked.

## 3.6. Zero address checking

### Severity: low

### Description

There are no zero address checking in functions [`setByPassedAddress`](https://github.com/mak2296/JntrToken/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/master/TokenUtil.sol#L74), [`setWhiteListAddress`](https://github.com/mak2296/JntrToken/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/master/TokenUtil.sol#L80) and [setTokenHolderWallet](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L99).

## 3.7. Security Check

### Severity: note

### Description

[Security check](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L23) is always set to true and can be removed from [checkBeforeTransfer](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/Token.sol#L51). If the intention of the developers was to set failsafe switch in case of issues with authorization logic, they should implement a setter function for that variable.

## 3.8. Owner Privileges

### severity: owner privileges

### Description

1. No cap is set for the system when [minting](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/Token.sol#L115). If the system addresses mint more than expected, the users can risk big losses.
2. Owner can change tokens [hold back days](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L87) without permission from the users.
3. Owner can change [token per eth](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L68) price at any moment, please also note that the owner can change the [token price](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L61) but the storage variable is not further used in the contract.
4. Following the whitepaper definition "The token JNTR/ETN has a 10 year maturity date", however the maturity date in the contract is set to [one year](https://github.com/ridesoloAudit/JntrToken-1/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L17). Using [setTokenMaturityDays](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/TokenUtil.sol#L93) the owner can update the date to any but the contract should reflect the whitepaper.
5. Owner can block user sending and receiving by [setReciveAndSend](https://github.com/mak2296/JntrToken/blob/75a7b807a9d7d3f4d8bb4ef66080812fd27b5e16/WhiteList.sol#L104) function.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/RideSolo/b0597545817b3c94955b669cf17e59f3

https://gist.github.com/gorbunovperm/6bb08307cd2cf0f7fc34dee0b6b2f286

https://gist.github.com/MrCrambo/06c9af30a97c9837ffaa0093c2dde71f

https://gist.github.com/danbogd/5e90bf095bdfef0e669b612e8544d38c
