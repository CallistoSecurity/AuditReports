# [opnplatform](https://github.com/OPNAG) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited smart contracts do not contain any high severity issues, however the above described issues have to be taken into consideration to avoid any possible error or misunderstanding with the investors.

# 2. Medium severity issues:

## 2.1. Insufficient Balance

### Description

Before adding tokens to user balance using `addTokens` function member of `TokenTimeLock` contract, a condition should be added where the token balance of the contract has to be sufficient to pay the users.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/TimeLock.sol#L143#L150


## 2.2 All transfer function should check is the recipient address empty.

### Code snippet

* [Escrow.sol#L161](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L161)

* [Escrow.sol#L175](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L175)

* [Escrow.sol#L182](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L182)

### Description

It is possible to accidentally send funds to `0x0` address if `getAllETH`(or another) function will called without the argument.

### Recommendation

Use validation `require(_to != address(0));`


# 3. Low severity issues:

## 3.1 It is required to limit the maximum of `_releaseTime` argument.

### Code snippet

* [TimeLock.sol#L143-L150](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/TimeLock.sol#L143-L150)

### Description

For various reasons (accident, vulnerability on the caller smart contract), `_releaseTime` variable may have a large value which can lead to overflow and that is more critical to **blocking tokens for many many years**. We can not rely on the correct input data and should check it in this contract.

### Recommendation

Limit the maximum value of `_releaseTime` variable. Let it be a real value based on company goals. May be next few years.

### Recommendation

Transfer tokens directly from `addTokens` method, like in [comment](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/TimeLock.sol#L146). Or use another reliable solution.


## 3.2. Tokens Amount Computation

### Severity: low

### Description

To compute `tokens` that a user bought, the `_weiAmount` in `_getTokenAmount` is first divided by `tokenPriceInWei` then multiplied by `10**decimals` (`1 ether` in the contract). 

If the devs intention was to set a minimum buy threshold of `tokenPriceInWei` or a multiple of it, this operation can be considered as correct.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L366#L398

## 3.3. Oraclize Balance

### Severity: low

### Description

Sufficient balance has to be available to execute oraclize queries,  if the contract owner call `withdrawBalance()` and empty the contract balance, oraclize callback won't execute the required query to update the exchange rate using `updatePrice`. 
This issue does not represent a risk since `updatePrice` can be called manually.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L249#L258

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L484#L486

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L489#L492

### Recommendation

- `updatePrice` is payable, `msg.value` should be added to `oraclizeBalance`, same as in `addBalanceForOraclize`.
- When `withdrawBalance()` is called the remaining contract balance should be equal to `oraclizeBalance`.
- At each call of `updatePrice`, the query price has to be deducted from `oraclizeBalance`. 

## 3.4. Escrow Race Condition

### Severity: low

### Description

If a user sends ether to the escrow contract using multiple transactions and a manager call `getETH` between two ethers transfer, `startBalance` will be set to the previous contract balance value making the withdrawal stages applicable only for that value.
to withdraw the ether left in the contract either the owner has to call `transferETH` or the manager has to wait until `stopDay` is reached.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L147#L149

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L161#L172

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L182#L185

## 3.5. Pausable Token Contract

### Severity: low

### Description

`pause` function member of `Pausable` contract can still be called even after ICO end (following the contracts logic the pause mechanism is used to prevent token trading while the ICO is ongoing), therefore pausing the token transfer.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Token.sol#L141#L144

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Token.sol#L119

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L465

### Recommendation

The devs should consider to implement a mechanism where this procedure is automated to protecet the token contract from any possible issue like hack of owner private key.

## 3.6. Multiple Declaration 

### Severity: low

### Description

Multiple declaration of `delOwner` member of `Escrow`. replace the function name with `delManager`.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L89#L93

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Escrow.sol#L102#L106


## 3.7. Known Issues of ERC20 Standard

### Severity: low

### Description

ERC20 Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

- Approve + transferFrom mechanism allows double Withdrawal attack (use `increaseApproval` or `decreaseApproval` to change the allowance value). 
- Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.

## 3.8. There is no way to change oraclize query url and result properties.

### Code snippet

* [Crowdsale.sol#L255](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L255)

```solidity
bytes32 queryId = oraclize_query(14400, "URL", "json(https://api.kraken.com/0/public/Ticker?pair=ETHUSD).result.XETHZUSD.c.0");
```

### Description

The API can temporarily not work or it can change, or the support of old versions of the API can be closed. In this case, the work of the contract will be broken. And there is no way to configure the receiving of data from another API.

### Recommendation

Make URL configurable by the contract owner.


# 4. Minor observations:

## 4.1. ICO Address 

### Description

`ICOAddress` is not set in the contract constructor of `Ownable` contract. however, the address can be set using `setICOAddress`.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Token.sol#L55

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Token.sol#L67#L69

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Token.sol#L104#L107

## 4.2. Oraclize Call in Constructor

### Severity: minor

### Description

The `Crowdsale` contract constructor should be payable in order to successfully execute `updatePrice` inside the constructor and update the exchange rate.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L222

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L250

## 4.3. `addTokens` method always returns `false`

### Code snippet
* [TimeLock.sol#L143](https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/TimeLock.sol#L143)

```solidity
function addTokens(address _owner, uint256 _value, uint256 _releaseTime) onlyOwner external returns (bool) {
    require(_owner != address(0));
    //SEND tokens to contract address!!!
    //token.safeTransferFrom(msg.sender, this, _value);

    balances[_owner] = balances[_owner].add(_value);
    releaseTime[_owner] = now + _releaseTime * 1 days;
}
```

### Description

There is no `return` statement in this function that means that it always returns `false` by default. And this can break the logic of the DApp.

### Recommendation

Add `return` statement with the right expression.

## 4.4. Crowdsale Stages

### Description

Stages of the `Crowdsale` contract are set after deploying the contract,  additional stages can be added while the ICO has started extending the ICO phase. investors have to be informed.

### Code snippet

https://github.com/OPNAG/contracts/blob/51d4bffb4c109e2a6a1c11a383e57724b1625d7e/Crowdsale.sol#L261#L271


# Revealing audit reports:

https://gist.github.com/yuriy77k/1c28df69a51a9772bb5341761407649d

https://gist.github.com/yuriy77k/6832913d6e8563ea7c2a689f14095bf0

https://gist.github.com/yuriy77k/ebe316622a50b285ebeb703257ed3a1d

