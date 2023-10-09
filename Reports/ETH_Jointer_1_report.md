# Jointer token Security Audit Report

# 1. Summary

[Jointer token](https://github.com/mak2296/JntrToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Сommit hash 35c2f9ba264ac722e8c57966ca820e8dc6b93fb0.

- [ERC20.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/ERC20.sol).
- [MultiOwnable.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/MultiOwnable.sol).
- [Ownable.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Ownable.sol).
- [SafeMath.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/SafeMath.sol).
- [StandardToken.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/StandardToken.sol).
- [Token.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol).
- [TokenUtil.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/TokenUtil.sol).
- [WhiteList.sol](https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol).

# 3. Findings

In total, **14 issues** were reported including:

- 1 critical severity issues.

- 2 medium severity issues.

- 3 low severity issues.

- 1 notes.

- 7 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1 Force Swap

### severity: Critical

### Description

When owners enforce user token swap using [`forceSwapWallet`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L169), `token.swapForToken` is called using `balances[_address]` parameter but user balance was previously burned through and set to zero `_burn(_address,balances[_address])` meaning that his balance is equal to zero, the swap will occur with zero value, user balance should be saved first in a memory variable then burned and the the newly created variable assigned for swap.

## 3.2 Wallet White Listing

### severity: Medium

### Description

Following the document provided, as example "If Investors purchase through third party exchanges like, national stock exchanges, tZero, and other exchanges should be able to check and update the whitelist through a secure API"; however, any address added to the whitlist will be able to add other addresses following the logic implemented in [`addMoreWallets`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L59)

```
    function addMoreWallets(address _which) public returns (bool){
        require(address_belongs[_which] == address(0));
        address sender = msg.sender;
        address primaryAddress = address_belongs[sender];
        require(is_whiteListed[primaryAddress]);
        address_belongs[_which] = primaryAddress;
        emit WalletAdded(primaryAddress,_which);
        return true;
    }
```

`address_belongs[_which]` is always set to `primaryAddress`, meaning that the `require(is_whiteListed[primaryAddress])` will never throw, since `address_belongs[_which]`  is always set to the first address that was whitelisted by the owner using [`addNewWallet`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L50). The result of such implementation is that any address will be able to whitelist other addresses recursively.

The contract function description state that "once user whitelisted it can add more address itself", but even addresses not listed in `is_whiteListed` mapping will be able to add other addresses, since whitelisting allow to manage KYC user onchain, this issue can have a bad legal impact on the project.

Developers should re-think the function logic.

## 3.3 Send Restriction

### severity: medium

### Description

[`setCansent`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L103) function member of `WhiteList` is setting `recive_block` instead of `sent_block`, where [`canSentToken`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L112) is checking `sent_block`.

## 3.4. Owner privileges

### Severity: owner privileges

### Description

The owner has a large number of privileges. This can be considered as an medium severity.

1. Owner can change hold duration both to increase and to decrease.
2. Owner can change token maturity days. This may affect the ability to transfer tokens. 
3. Owner can block user sending and receiving by `setCansent` and `setCanRecive` functions.
4. Owner can set any price of tokens at any time.
5. Can mint any value of tokens to any address by `assignToken` function.
6. Upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.
7. `forceSwapWallet` allow the owner to force user address balance swap.

The owner can manipulate these parameters to his advantage.

### Code snippet

1. https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/TokenUtil.sol#L80
2. https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/TokenUtil.sol#L111
3. https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L103
4. https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/TokenUtil.sol#L59
5. https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L154
6. https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L75-L82
7. https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L169

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.6. Transferring to swappable token

### Severity: low

### Description

When using `transfer` function and in case the recipient is a swappable token, then the exchange occurs. But when using `transferFrom` with swappable token as recipient the exchange is no occurs but the tokens will be sent. On the part of the user it is logical to assume that these two functions work the same in this case. This may result in the loss of the sender's funds.

### Code snippet

* https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L115-L122

## 3.7. No checking for zero address

### Severity: low

### Description

Incoming addresses should be checked for an empty value(0x0 address) to avoid loss of funds or blocking some functionality.

### Code snippet
https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L76

https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/Token.sol#L80

https://github.com/mak2296/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/MultiOwnable.sol#L57

## 3.8 Doc Parsing

### severity: note

### Description

Doc parsing errors are raised for [`addMoreWallets`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L59) and [`setCansent`](https://github.com/ridesoloAudit/JntrToken/blob/35c2f9ba264ac722e8c57966ca820e8dc6b93fb0/WhiteList.sol#L103) since the documentation to be generated contains invalid parameters name `_address` and `_recive` that should be replaced with `_which` and `sent`.


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/gorbunovperm/543d1ea731858947c265091a1a62932d

https://gist.github.com/danbogd/064f6a441f498b28717a98fd85b0ff8b

https://gist.github.com/MrCrambo/d2d8af2b70c9f97d7084cd49482a3903

https://gist.github.com/RideSolo/29b0bc7bdf12f9512c314d7ca320daa8

# 6. Notes.

## 6.1. Note about [danbogd](https://gist.github.com/danbogd/064f6a441f498b28717a98fd85b0ff8b) report.

### There is no way to remove user and wallets.

The owner can block sending and receiving token for any address, so removing address is just new future and developer may decide is it necessary. 
Is not security issue.