# [Cryptonity Smart Contract](https://github.com/Katsiaryna-Maslava/cryptonity) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

This ICO is not safe to be deployed, the developers should update the code following the above highlighted security issues.

# 2. High severity issues:

## 2.1. Crowdsale Finalization Failing 

### Description

At the end of the execution of the reimplemented `finalization` function member of `CryptonityCrowdsale` contract, the token address used cannot accept `ownershipTransfert` since the `msg.sender` has to be the owner address. The token contract isn't created inside `CryptonityCrowdsale` contract, therefore the call to `finalize` function will throw and the ICO will fail.

### Code snippet

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityCrowdsale.sol#L838

Same issue here due to duplicated contract:

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/CryptonityCrowdsale.sol#L220

### Recommendation

Remove the the implicated code line.


# 3. Medium severity issues:

## 3.1. Investor Reward Computation 

### Description

The Token reward value when users decide to invest is dependent on ETH/USD rate exchange (look at `FiatContract`), the rate is set by the team and can be subject to manipulation (this is just a possibility where users reward can be manipulated). The users will have to trust the team to input the right rate, this can be a decisive point for both users safe investments and the project fund raising where it can have a positive or a negative impact for both sides (dev/investors).

### Code snippet

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/FiatContract.sol#L87#L91

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/CryptonityCrowdsale.sol#L115#L118

Same issue here due to duplicated contract:

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityCrowdsale.sol#L744#L747

### Recommendation

A fully automated crowdsale phase, without external interaction can help investors to trust the ICO.


## 3.2. Goal Reached Condition 

### Description

The value returned by `goalreached` function depend on an external factors manually set by the team (ETH/USD rate), following the high volatility of the crypto markets the results of such function can be highly random. The possible errors are the following:

- If ETH/USD rate changes and the team update the rate after the finalization of the Crowdsale phase, `withdrawTokens()`call may fail since it requires `goalReached` to return `true`.
- The opposite is also true if the crowdsale goal isn't reached and finilized, if the rate change later the `claimRefund()` (member of OpenZepplin contracts framework) call may fail since it requires `goalReached` to return `false`.

### Code snippet

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityCrowdsale.sol#L777#L779

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityCrowdsale.sol#L816#L821

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityCrowdsale.sol#L562#L567

Same issue here due to duplicated contract:

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/CryptonityCrowdsale.sol#L148#L150

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/CryptonityCrowdsale.sol#L198#L203


## 3.3. Possible setup to zero address. 

### Description

Tokens could be sent to zero address and owner can lose his access because of setting sender and creator address to `zero address`. Functions [`changeCreator`](https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/FiatContract.sol#L101) and [`changeSender`](https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/FiatContract.sol#L107).

### Recommendation

Need to check if address whe want to set is not zero address.


# 4. Low severity issues:

## 4.1. Incorrect Solidity version.

### Description

`FlatCryptonityCrowdsale` and `FlatCryptonityToken` contracts have "pragma solidity ^0.4.13;" but "constructor" keyword is available from 0.4.23 version. 

### Code snippet

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityCrowdsale.sol#L1

https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/flatContracts/FlatCryptonityToken.sol#L1

### Recommendation

Replace  ```pragma solidity ^0.4.13;``` to ```pragma solidity ^0.4.23;```


# 5. Minor observation:

## 5.1. Possible wrong logic.

### Description

Function [changeSender(address _sender)](https://github.com/Katsiaryna-Maslava/cryptonity/blob/bc1b8e23c9881722d594beb82ac80c5ee8319218/contracts/crowdsale/FiatContract.sol#L107-L109) ```require(msg.sender==creator)```, but change ```sender``` address. Maybe there was ```require(msg.sender == sender)```?



# Revealing audit reports:

https://gist.github.com/yuriy77k/f24cade1d02e51deaffde6192dbc03fd

https://gist.github.com/yuriy77k/8ba6b05a5426d19eb1b389cd01eabe6b

https://gist.github.com/yuriy77k/8ba5efceb8e7214055be437373124220

