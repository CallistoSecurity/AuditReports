# TRONDapphub Dice Security Audit Report

# 1. Summary

[TRONDapphub Dice](https://trondapphub.io/wp/Whitepaper%20TRONDapphub.pdf) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- The audited code was requested to be private.

# 3. Findings

In total, **20 issues** were reported including:

- 2 Critical severity issues.

- 4 high severity issues.

- 3 medium severity issues.

- 6 low severity issues.

- 5 owner privileges (ability of owner to manipulate contract, may be risky for investors).

## 3.1. Dice Bet Creation

### Severity: Critical

### Description

If we understand well `Dice` contract is supposed to be dice bet game, `createBet` is non restricted public function that any one can call to make a bet, however the function is not payable and it still transfers the `_value` to `diceAdmin`, if the process is automated then any one can exploit this function to win without betting any ether since the ether will be taken from the contract balance and `payoutTRX` will be called by the game bot to pay them the value won.

Developers should explain the logic behind this contract.

### Code snippet

```
    function createBet(address player, address _player, uint256 _value, bytes32[] data) public {
        require( player != address(0) );
        require( player != diceAdmin );
        transferTo(diceAdmin, _value);
        emit CreateBet(_player, _value, data);
    }
```

## 3.2. Token Distribution

### Severity: High

### Description

`distributeToken` member `TDHManager` contract is from where the owner will distribute tokens using, since `getCurrentWagerTrx` will reset the value of `cumulative` to 0 99 times if `cumulative` is higher or equal to `TO_BE_MINED` and following the function logic.

However a requirement present in `getCurrentWagerTrx` `require( level < STEP && stage < STEP )` is in opposite to `if ( stage > STEP )` presented in the same function, the result will be that the function will throw after ~9M distributed in approximation (approximately because `cumulative` is reset after that is higher or equal to `TO_BE_MINED` since the value distributed is added after calling  `getCurrentWagerTrx` and `cumulative` is not checked if it is higher than `TO_BE_MINED` in `distributeToken`).

The consequence is if all supply of tokens sent to `TDHManger` or to a contract that inherit from it, most of the tokens will be stuck after ~9M token distributed.

Owners should also explain why 99M to be distributed since it can be either a mistake or intentionally done, however if it is a mistake it can be fixed by changing the conditions.

### Code snippet

```
    function getCurrentWagerTrx() private returns(uint256) {
        require( level < STEP && stage < STEP );
        if ( cumulative >= TO_BE_MINED ) {
            wagerTrx = wagerTrx.add( DELTA * 10 ** DECIMALS );
            cumulative = 0;
            if ( stage > STEP ) {
                level++;
                stage = 1;
            }
            else{
                stage++;
            }
            emit LevelUp(level, stage);
        }
        return wagerTrx;
    }
```
### Recommendation

- Remove `stage < STEP` from the requirement.

## 3.3. Block Gas Limit

### Severity: medium

### Description

When distributing dividends using `dividends` function member of `TokenTDH` if `token_hoder_list` is long enough, which can happen since token deposit using `receiveApproval` will extend its size, `dividends` function will throw either for an out of gas or a block gas limit reached, making dividends distribution impossible and all ether sent to the contract stuck.

### Code snippet

```
    function dividends( uint256 _currentProfit ) public onlyOwner {
        
        for(uint i = 0; i < token_hoder_list.length; i++) {
            token_hoder_list[i].transfer(token_holders[token_hoder_list[i]].div(onlineFrozenTokens)*_currentProfit * (10 * DECIMALS));
        }
        emit Dividends(true);
        unfreezable = false;
    }
```

## 3.4. Multiple Dividend to same Address

### Severity: high

### Description

If a user deposit multiple times through `receiveApproval` its address will be added multiple times to `token_hoder_list` making him receives dividends multiple times when `dividends` is called.
consequences are that if the contract do not contain enough ETH the function will throw, the call to `dividends` will spend more ether than expected. 
 
### Code snippet

```
    function receiveApproval(address _from, uint256 _value, address _token, bytes data) public {
        require( _from != tdhManager );
        
        token_holders[_from] = token_holders[_from].add(_value);
        onlineFrozenTokens = onlineFrozenTokens.add(_value);
        token_hoder_list.push(_from);
        
        ITRC20(_token).transferFrom(_from, tdhManager, _value);
        emit FreezeTDH(_token, _from, _value);
    }
```

## 3.5. Dice Payout

### Severity: high

### Description

`payoutTRX` member of `Dice` contract is restricted to game addresses only through `onlyGame` that it inherit from `TDHManager` that support all the dividends computation and token distributions, if another game is added to the games collection as described in the whitepaper his address wil be added using `addGame` which will allow it to call `payoutTRX` and transfer any amount present in the contract to any address.

Dice should not inherit from `TDHManager` since `GameManager` contract contain a `createBet` function that calls game contract externally, the whole inheritance should be explained in more details for a better audit.
 
### Code snippet

```
    function payoutTRX(address player, uint256 value) public onlyGame {
        transferTo(player, value);
        emit DicePayout(player, value);
    }
```

```
contract GameManager is GameRole, IGameManager {

    // -------------------- checked -------------------//
    function createBet(address game, address player, bytes32[] data) public payable {
        require(games[game]);
        IGame(game).createBet(msg.sender, player, msg.value, data);
    }
```

## 3.6. Token transfers

### Severity: medium

### Description

In `TokenTDH` contract, the private `_transfer` function is used by both `transfer` and `transferFrom`, where a wrong extra requirement is added `require(_allowed[from][to] > value);` making most transfer impossible for both `transfer` and `transferFrom` function, except if the `from` address has pre allowed token to `to` address which doesn't make sense.

Allowance should only be checked when `transferFrom` is called, as it is already done since the following line is already present in `transferFrom` function:

`_allowed[from][msg.sender] = _allowed[from][msg.sender].sub(value);`

### Code snippet

```
    function _transfer(address from, address to, uint value) private {
        require(to != address(0));
        require(to != address(this));
        require(_allowed[from][to] > value);  // ----- <<< this line should be removed. 
        _balances[from] = _balances[from].sub(value);
        _balances[to] = _balances[to].add(value);
        emit Transfer(from, to, value);
    }
```

## 3.7. Owner Privileges

### Severity: owner privileges

### Description

1. Once tokens sent through `receiveApproval` function member of `TDHManager` to be locked, only the owner is allowed to send them back using `unfreezeToken`.
2. Only the owner can distribute tokens and no sort of ICO is implemented and following the contract logic most of the total supply will be distributed using `distributeToken`, no sort of fundamental base can support the token price.
3. Token allocated through `distributeToken` in `TDHManager` are only withdrawable using `withdrawTDH` that is only accessible by the owner, nothing guarantee to the beneficiary that he can get his tokens one distributed to him.
4. `Dice` game bet is computed offchain which do not guarantee the non-manipulations of the game by the admin.

## 3.8. Dice Admin

### Severity: low

### Description

In `Dice` contract the `diceAdmin` is set to `msg.sender` in the constructor, meaning that `owner` is also equal to `diceAdmin` since `Dice` inherit from `TDHManager` that inherits from `Ownable` at some point.
In the inheritance of `Dice` `GameManager` is also present and it is where `transferTo` is implemented and as we can see transfers aren't allowed to owner, meaning that owner of the contract should be changed to allow `createBet` function to call `transferTo` otherwise the function will throw since it is sending ether to diceAdmin.

### Code snippet

```
    function transferTo(address player, uint amount) public onlyGame {
        require(player != address(0));
        require(player != owner);
        player.transfer(amount);
    }
```

## 3.9. Fallback Function

### Severity: low

### Description

### Code snippet

The fallback function implemented in `TDHManager` contract allow any address to deposit ether to the contract either by mistake or intentionally by the owners to distribute the dividends later on.

### Code snippet

```
    function () external payable {
    }
``` 

### Recommendation

Allow only known addresses that are going to deposit.


## 3.10. Token Freezing

### Severity: low

### Description

The token freezing mechanism implemented in `TDHManager` doesn't guarantee anything since both `setFreezable` and `unfreezeToken` are both only allowed to be accessed by the owner only, meaning that if the owner set `unfreezable` to true he can also change it to false and `unfreezeToken` is also accessible by him.

### Code snippet

```
    function setFreezable(bool _status) public onlyOwner {
        unfreezable = _status;
    }

    function unfreezeToken(address _token, address _to, uint256 _value) public onlyOwner {
        require(unfreezable, 'stil_not_allowed');
        require( _to != address(0) && _to != tdhManager );
        require( token_holders[_to] >= _value );
        ITRC20(_token).transferFrom(tdhManager, _to, _value);
        token_holders[_to] = token_holders[_to].sub(_value);
        onlineFrozenTokens = onlineFrozenTokens.sub(_value);
        emit UnfreezeTDH(_token, _to, _value);
    }
```

### Recommendation

`unfreezeToken` should be accessible by all token holders where the `_to` address will be changed to `msg.sender`.

## 3.11. Dividends

### Severity: low

### Description

All described issue related to dividends put aside `DECIMALS` is used in dividends calculation when we clearly see that a Token unit is divided by another token unit then multiplied by `_currentProfit` that should be in ether then why is it multiplied again by (10 * DECIMALS). Developers should explain this issue.
 
### Code snippet

```
    function dividends( uint256 _currentProfit ) public onlyOwner {
        
        for(uint i = 0; i < token_hoder_list.length; i++) {
            token_hoder_list[i].transfer(token_holders[token_hoder_list[i]].div(onlineFrozenTokens)*_currentProfit * (10 * DECIMALS));
        }
        emit Dividends(true);
        unfreezable = false;
    }
```

## 3.12. Decrease Allowance

### Severity: low

### Description

`decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

```
    function decreaseAllowance(address spender, uint subtractedValue) external returns (bool) {
        require(spender != address(0));

        _allowed[msg.sender][spender] = _allowed[msg.sender][spender].sub(subtractedValue);
        emit Approval(msg.sender, spender, _allowed[msg.sender][spender]);
        return true;
    }
```


## 3.13. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );
```

## 3.14. Error in calculation of referrers fee

### Severity: high

### Code snippet

`TDHManager.sol`, line 64.

### Description

Let's look at `distributeToken` function. Its purpose is to distribute tokens to player in accordance with the `TRX` amount paid. Also it pays a fee to referrer.

The formula of referrer reward is:
```Solidity
_wageredTrx.div(1000).mul(2) * 10 ** DECIMALS
```
`_wageredTrx` is amount of `TRX`s paid and it's also with decimals. It's wrong to multiply this to `10 ** DECIMALS`. According to the formula it was assumed that the fee to the referrer will be 0,2%. In fact, it equal to `200'000%`.

This can lead to the loss of a large amount of money.

### Recommendation

Remove `* 10 ** DECIMALS` in the formula.

## 3.15. Unable to call `withdrawTDH` function

### Severity: medium

### Code snippet

* `TDHManager.sol`, line 68.
* `GameManager.sol`, line 21.

### Description

The `withdrawTDH` function has `onlyOwner` modifier, but it calls `transferTokenTo` function with `onlyGame` modifier. The first should called by owner and the second by game contract. Calling this function will be unable by the intended way.

### Recommendation

Change permissions for `transferTokenTo` function.

## 3.16. Anyone can get an undeserved reward

### Severity: critical

### Code snippet

* `TDHManager.sol`, line 72.

### Description

Anyone can call the `receiveApproval` method with an attacker token contract address as argument. In this method, the address is not verified and can be anything. An attacker can approve any number of tokens and receive dividends or get unfreezed by owner TDH tokens.

#### Recommendation

Check the address of the token for compliance with the address of the TDH token.

## 3.17. Games privileges

### Severity: owner privileges

### Code snippet

* `GameManager.sol`, line 15.
* `GameManager.sol`, line 21.

### Description

Game contracts can be third-party, but at the same time they have the opportunity to dispose of any TRX or TDH tokens of the `GameManager` contract. Even if the games are only of own production they should not be able to dispose of all the funds for security reasons.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/fbbf1786a21f9e940ada52cfabb1407f

https://gist.github.com/yuriy77k/7a889f8b9f8d679e58ff31472916ae72

https://gist.github.com/yuriy77k/8ed456bb9bc0f22fbb0e0832277fcc71