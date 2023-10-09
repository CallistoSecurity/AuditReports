# xEuro Token v2. Security Audit Report

# 1. Summary

[xEuro Token v2.](https://etherscan.io/address/0xe577e0b200d00ebdecbfc1cd3f7e8e04c70476be#code) contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

[xEuro](https://etherscan.io/address/0xe577e0b200d00ebdecbfc1cd3f7e8e04c70476be#code)

# 3. Findings

In total, **6 issues** were reported including:

- 2 low severity issues.

- 1 notes.

- 3 owner privileges (an ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Missing Events 

### Severity: low

### Description

Transfer event should be emitted when `mintTokens` is used, the event can show a transfer event from `address(0)` to the contract address for this case.
When tokens are burned using `burnTokens` a `Transfer` event should be emitted from the contract address to `address(0)`. 
This is required by dapps that track users balances.  

### Code Snippet

[Line: 587](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L587)

[Line: 722](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L722)

## 3.2. Zero address

### Severity: low

### Description

In function `changeCryptonomicaVerificationContractAddress` there is possibility of passing zero address into function.

### Code snippet

[Line: 348](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L348)

### Recommendation

Add zero address checking.
```solidity
require(_newAddress != address(0));
```

## 3.3. Token Uses No Decimals

### Severity: note

### Description

While the specification defined the number of token decimals to be 18, no decimals were found to be used. This can cause problems when interacting with other smart contracts as tokens with 0 decimals can cause rounding errors. For example, many exchanges charge a small fee based on the tokens exchanged. As such, using no decimals will either make it impossible to list the token on these exchanges or it will result in having expensive fees compared to other tokens.

### Code snippet

[Line: 127](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L127)

## 3.4 Administrators Addresses Management

### Severity: owner privileges

### Description

Any admin address added through `addAdmin` will be able to remove all other admins addresses, `addAdmin` and `removeAdmin` function execution should be allowed for execution to a different group than admin group.
 
### Code snippet

[Line: 365](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L365)

[Line: 391]()https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L391)

## 3.5. Tokens Buy Back

### Severity: owner privileges

### Description

Token that are sent back to the contract address are logged and an event is emitted to the UI to process the exchange from tokens to fiat offchain, if the payment does not succeed the tokens should be at least sent back to the user however no function is intended to reimburse the users (fiat payment may not succeed for different reasons), only the success case is treated since the tokens can be burned later on using `burnTokens` with `fiatOutPaymentId` that should represent the payment made offchain to the user.

### Code snippet

[Line: 262](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L262)

## 3.6. Token Minting and Transfer

### Severity: owner privileges

### Description

To mint token using `mintTokens` function a `fiatInPaymentId` should be added as input parameters meaning that an external fiat payment was received by the team and the equivalent tokens should be given to a specific address that belongs to the person that made the fiat payment.

Only specific addresses allowed through `canMint` can mint tokens, and only addresses allowed through `canTransferFromContract` can call `transferFrom` to transfer the minted tokens to their beneficiary by setting the from address to the contract address and the `_to` address to the destination address.

- an address that is in both `canMint` and `canTransferFromContract` will be able to mint and transfer tokens to the receiver.
- an address that is only in `canMint` will only be able to mint tokens (please note that the minted tokens are address to the contract address balance at first.)
- an address that is in `canTransferFromContract` will be able to transfer any tokens that are allocated to the contract address to any address even if the minted tokens where minted to be transferred to a specific address, if the developers have taken this into account they should bear all the responsibility if any added address take all the contract address balance, if this issue was not intended then it should be fixed asap.
- Token minting does not guarantee any real investment since the payment are made offchain, meaning that is an address receive enough privileges it will be able to mint without real payment.
- Token minting is not capped, this present more risks to investors especialy knowing that minting `fiatInPaymentId` that is supposed to be made offchain is not guaranteed by anyway.

### Code snippet

[Line: 589](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L589)

[Line: 602](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L602)

[Line: 256](https://gist.github.com/RideSolo/cd69639424c60572fe5e0f3cb3596418#file-xeuro-sol-L256)

### Recommendation

As soon as `mintTokens` is used the tokens should be assigned to the person that made the fiat payment offline and not to the contract address, this two steps mechanism make every one at risk.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ce9e496aa70d315b58cf6d8baf68b959

https://gist.github.com/yuriy77k/bf72550d006394a82649da8509d77dfe

https://gist.github.com/yuriy77k/34a16d2e48bcba58c8a9ef3aefade126
