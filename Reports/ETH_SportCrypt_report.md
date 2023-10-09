# SportCrypt Security Audit Report

# 1. Summary

[SportCrypt](https://sportcrypt.com/whitepaper.pdf) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [SportCrypt.sol](https://github.com/sportcrypt/sportcrypt-contract/blob/master/SportCrypt.sol) github gist 66d81c570d5d464253924521a97d1ae7fbf1fb68.

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. It is necessary to check the input address to the zero-address.

### Severity: low

### Description

In the changeOwner and addAdmin functions, the input address is not checked for a null value.

### Code snippet

https://github.com/sportcrypt/sportcrypt-contract/blob/e002324d630108f1be38d5b2f1685426fe0ab67f/SportCrypt.sol#L16
https://github.com/sportcrypt/sportcrypt-contract/blob/e002324d630108f1be38d5b2f1685426fe0ab67f/SportCrypt.sol#L20

## 3.2. Match ID

### Severity: low

### Description

Matches IDs are created offlines to avoid extra interactions with the contract and gas consumption, as described in the whitepaper:

"4.1  Match Creation: When new matches are offered for trading, they are added to our backend system which forwards them to all connected clients via websocket. The exchange doesn’t need to perform any on-chain actions to create a new match. This is a major scalability advantage of SportCrypt since it reduces the operational costs of the exchange and allows us to experiment with many simultaneous matches without worrying about ethereum gas overhead or waiting for blocks to be mined."


If a match id that is created by a third person (that is not related with the team ) is not picked by UI and finalized , this issues can represent a risk since someone can lure a user to use a match id that he created manipulating parameters like `recoveryTimestamp` and `finalPrice`.

A match can be finalized by an admin or after `[recoveryTimestamp`](https://github.com/RideSolo/sportcrypt-contract/blob/master/SportCrypt.sol#L251) has passed, if `recoveryTimestamp` is set low enough the `finalPrice` will be taken from the `matchid` and not set by the admin as it can be seen [`here`](https://github.com/RideSolo/sportcrypt-contract/blob/master/SportCrypt.sol#L244).

Many situation can lead to an exploit of this issue, like creating a fake website but using the real contract address to show good activity of the contract transactions.

The described issue is not a direct exploit but require some social engineering to lure victims, developers can give more explanation to clear out this issue.

### Code snippet

https://github.com/sportcrypt/sportcrypt-contract/blob/master/SportCrypt.sol#L254

https://github.com/sportcrypt/sportcrypt-contract/blob/master/SportCrypt.sol#L251

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/6d0c21d54f9f03d36d8f897001af62ba

https://gist.github.com/yuriy77k/9f16e02d2b54697f7a2f0051a9bceaf3

https://gist.github.com/yuriy77k/538e1f24c7367dacfa75d9a43d4ba0d1
