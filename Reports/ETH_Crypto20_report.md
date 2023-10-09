# Security Audit Report

# 1. Summary

[CRYPTO20 (C20)](https://etherscan.io/address/0x26e75307fc0c021472feb8f727839531f112f317#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol

# 3. Findings

In total, **12 issues** were reported including:

- 4 low severity issues.

- 1 minor observation.

- 7 owner privileges (ability of owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Withdrawal

### Severity: low

### Description

In order to make a withdrawal, the user has first to request the withdrawal using `requestWithdrawal` then call `withdraw` to get the equivalent value in ether.
However between making the request and the withdraw the price could be changed by the team for many reason like a drop in token price, the equivalent value is computed in the withdraw function instead of the request, making the user unable to know the value to be withdrawn.
- There is no possible way for a user to cancel a submitted withdraw request, except if there is not enough of ether in the contract then his balance will be automatically replenished once withdraw is called.
- The waiting time for a user once a withdraw request is submitted to call withdraw cannot be defined if the amount to be withdrawn is higher then the contract balance. The user will have to wait until a manager replenish the contract balance.

### Code snippet

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L225

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L236

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L253

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L261

## 3.3. Transfer Event

### Severity: low

### Description

Transfer events are not triggered in `allocateTokens` `allocatePresaleTokens`. This issue may cause compatibility issues with blockchain explorers.

### Code snippet

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L166

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L179

## 3.4. Poor workaround of Short Address Attack

### Severity: low

### Description

`onlyPayloadSize` modifier is workaround to avoid the Short Address Attack. But it doesn't work properly.

1. This method failed to execute when it was called from Parity multisignature wallet. The EVM pads call from this multisignature wallet, making the total 96 bytes instead of the expected 68.

2. If `transfer` and `transferFrom` are used by a subcontract function with fewer arguments, the onlyPayloadSize check will fail. It is not possible to adapt the workaround to prevent this issue.

More details [here](https://blog.coinfabrik.com/smart-contract-short-address-attack-mitigation-failure/).

### Code snippet

https://github.com/cryptotwenty/smartcontracts/blob/7b6559f646581a6b76e2384589796e421d0ef126/SafeMath.sol#L29-L32

### Recommendation 

Remove this workaround.

## 3.5. Owners Allocations

### Severity: note

### Description

Following the comments the owner should allow him self 13% of the total sold tokens. however the code shows 14.94%.

### Code snippet

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L168#L169

## 3.6. Owner Privileges

The contract owners allow themselves to:

### Severity: owner privileges

### Description

The contract owners allow themselves to:

1. `require_waited` modifier is set to limit the calls to `updatePrice` but `waitTime` can be changed at any moment using `changeWaitTime`, making this mechanism useless to prevent frequent price update by the team.
2. `updatePriceDenominator` update the denominator unlike `updatePrice` that update the numerator, the `waitTime` mechanism is applied only to `updatePrice` when it should be applied for both.
3. change `vestingContract` address at any time after setting it for first time.
4. halt/unhalt token sell (not applicable now since the ICO ended).
5. Manually enable trading even after the `fundingEndBlock` (not applicable now since the trading was enabled).
6. The owner has the right to change the end date of the crowdsale (not applicable now since the ICO ended).
7. The owner can withdraw funds even before the end of the crowdsale. It should be available after the end of crowdsale (not applicable now since the ICO ended).

### Code snippet

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L301

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L98

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L124

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L132

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L156

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L317#L322

https://github.com/cryptotwenty/smartcontracts/blob/5bd6f1ec2cc1e15191d20f168bcaf0e48fea6775/C20.sol#L324

https://github.com/cryptotwenty/smartcontracts/blob/7b6559f646581a6b76e2384589796e421d0ef126/C20.sol#L314

https://github.com/cryptotwenty/smartcontracts/blob/7b6559f646581a6b76e2384589796e421d0ef126/C20.sol#L287


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/45a30008d6f249a98344baf304e8aecc

https://gist.github.com/yuriy77k/866b772042b5689330cc5c72f28d8422

https://gist.github.com/yuriy77k/aa08e12770f0d4651cd41e95b624cec4
