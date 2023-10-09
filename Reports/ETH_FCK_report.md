# FCK Security Audit Report

# 1. Summary

[FCK](https://github.com/FCKOfficial/FCK-contracts/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash: `a7fd48b135db2f4828cbc8e5e694b4a9627cd323`

1. [fck.com Sol](https://github.com/FCKOfficial/FCK-contracts/blob/a7fd48b135db2f4828cbc8e5e694b4a9627cd323/dice/fck.com%20Sol)

# 3. Findings

In total, **4 issues** were reported including:

- 2 low severity issues.

- 1 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Fallback Function

### Severity: low

### Description

The fallback function allows anyone to deposit ether to the contract, only addresses intended to deposit tokens should be allowed to deposit.

### Code snippet

https://github.com/RideSolo/FCK-contracts/blob/master/dice/fck.com%20Sol#L139

## 3.2. Kill Function

### Severity: notes

### Description

Once `kill` function called, all payables function should be blocked including the fallback function.

### Code snippet

https://github.com/RideSolo/FCK-contracts/blob/master/dice/fck.com%20Sol#L191

## 3.3. The croupier delay

### Severity:  owner privileges

### Description

The bet will not be played if the croupier does not call `settleBet` function within an hour(250 blocks) of betting. The croupier is a server-side mechanism. And if there are problems with the server - the work of the dealer will stop. Players can lose their bets.

Also, this restriction can be used by the contract owner maliciously, for example, when the user can win the jackpot or makes a very large bet and it can become a winning one.

### Code snippet

* [BET_EXPIRATION_BLOCKS](https://github.com/FCKOfficial/FCK-contracts/blob/a7fd48b135db2f4828cbc8e5e694b4a9627cd323/dice/fck.com%20Sol#L344)

## 3.4. Zero address checking required

### Severity: low

### Description

In `constructor`, `setCroupier`, `setSecretSigner` and `sendFunds` there are zero address checking required.

### Code snippet

https://github.com/FCKOfficial/FCK-contracts/blob/master/dice/fck.com%20Sol#L113

https://github.com/FCKOfficial/FCK-contracts/blob/master/dice/fck.com%20Sol#L165

https://github.com/FCKOfficial/FCK-contracts/blob/master/dice/fck.com%20Sol#L160

https://github.com/FCKOfficial/FCK-contracts/blob/master/dice/fck.com%20Sol#L469

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/316d0183bf8686bb63e56a95da31f1ef

https://gist.github.com/yuriy77k/6ae298c8327d9d3cb284df055956f7b7

https://gist.github.com/yuriy77k/c43197dab70c1856d949cda36398bb8a
