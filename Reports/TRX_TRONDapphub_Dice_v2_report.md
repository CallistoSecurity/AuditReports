# TRONDapphub Dice v2 Security Audit Report

# 1. Summary

[TRONDapphub Dice v2](https://trondapphub.io/wp/Whitepaper%20TRONDapphub.pdf) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

TRONDapphub aims to host 100+ games in 1 Platform by 2020.
Currently, we have 60 games available to deploy by Q4 2019.
* TRONdapphub.io aims to provide both Mobile and Web Gaming experience to its
Users.
* TRONdapphub.io mission is to be the Biggest Dapp by Volume and Usage standpoint on Tron Ecosystem by hosting most Popular Traditional PVP Games like Bowling,
Billiards, Football, and Casino Games like Dice, Texas Holdem, Black Jack, Baccarat, High
Low, Let it Ride, Casino War, Slots, Keno.
* Our goal as a team is to create the fairest provability across all our games.

 https://trondapphub.io/#/dice

# 2. In scope

Private files from `TDH_0503.zip`:

1. `TDHManager.sol`
2. `Dice.sol`
3. `GameRole.sol`
4. `IApproveAndCallFallback.sol`
5. `ITRC20.sol`
6. `Ownable.sol`
7. `SafeMath.sol`
8. `TokenTDH.sol`

# 3. Findings

In total, **15 issues** were reported including:

- 1 critical severity issues.

- 4 high severity issues.

- 3 medium severity issues.

- 4 low severity issues.

- 1 notes.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Transfer From

### Severity: medium

### Description

The allowed amount to be transferred is subtracted from `_allowed[from][to]` when it should be `_allowed[from][msg.sender]`.

The developers should note that this issue is a priority to be fixed since it is not TRC20 compatible, once fixed, other issues will appear such as "3.7. Distributed Token Withdrawal".

### Code snippet

`TokenTDH.sol` line 121:

```
    function transferFrom(address from, address to, uint value) external returns (bool) {
        
        _allowed[from][to] = _allowed[from][to].sub(value);
        _transfer(from, to, value);
        emit Approval(from, msg.sender, _allowed[from][msg.sender]);
        return true;
    }
```

## 3.2. Decrease Allowance

### Severity: low

### Description

`decreaseAllowance` throw in case if the value to be subtracted is higher than the amount that is allowed, if the address owner wants to change the value allowed by reducing it and the spender withdraw a part of it before, the function might throw and give more chances for the spender to take the rest of the allowed value.

The value should be set to zero if the value to be subtracted is higher than the allowance.

### Code snippet

`TokenTDH.sol` line 137:

```
    function decreaseAllowance(address spender, uint subtractedValue) external returns (bool) {
        require(spender != address(0));

        _allowed[msg.sender][spender] = _allowed[msg.sender][spender].sub(subtractedValue);
        emit Approval(msg.sender, spender, _allowed[msg.sender][spender]);
        return true;
    }
```

### Recommendation

Set the allowance to zero if the value to be subtracted is higher than the allowance.

## 3.3. Token Distribution

### Severity: high

### Description

`getCurrentWagerTrx` will reset the value of `cumulative` to 0 90 times if `cumulative` is higher or equal to `TO_BE_MINED`, because `level` and `stage` state variable are both set to be equal to 1 inside the constructor but the conditions that allow the iteration are not the same since `level` is compared to be less than `STEP` and `stage` is compared to be lower than `STEP` meaning that `cumulative` will be reset 9x10 times and not 10x10 times, . 

The result will be that the function will throw after ~90M distributed in approximation (approximatly because `cumulative` is reset after that is higher or equal to `TO_BE_MINED` since the value distributed is added after calling  `getCurrentWagerTrx` and `cumulative` is not checked if it is higher than `TO_BE_MINED` in `distributeToken`).

The consequence is if all the token supply is sent to `TDHManger` or to a contract that inherit from it, most of the tokens will be stuck after ~90M token distributed.

### Code snippet

`TDHManager.sol` line 46:

``` 
    constructor(address tdh_token_) public {
        require(msg.sender != address(0));
        level = stage = 1;
```

`TDHManager.sol` lines 129-142:

```
    function getCurrentWagerTrx() private returns(uint) {
        require( level < STEP );
        if ( cumulative >= TO_BE_MINED ) {
            wagerTrx = wagerTrx.add( DELTA );
            cumulative = 0;
            stage++;
            if ( stage > STEP ) {
                level++;
                stage = 1;
            }
            emit LevelUp(level, stage);
        }
        return wagerTrx;
    }
```

## 3.4. Fallback Function

### Severity: low

### Description

### Code snippet

The fallback function implemented in `TDHManager` contract allow any address to deposit ether to the contract either by mistake or intentionally by the owners to distribute the dividends later on.

### Code snippet

`TDHManager.sol` lines 61-63:

```
    function () external payable {
    }
```
 
## 3.5. Compliance Errors

### Severity: medium

### Description

`ApproveAndCall/receiveApproval` functions member of `TokenTDH` have different parameters than the original ERC827 implementation where they were presented first, causing incompatibility with any contract that uses this mechanism.

- `to` address parameters is added to both `ApproveAndCall` and `receiveApproval`, when this parameter cannot be set by the address that approve the transfer, since once approved the contract address to be called should handle to tokens.
- `approveAndCall` member of `TokenTDH` approves tokens to `to` address instead of the `spender` address.

### Code snippet

`IApproveAndCallFallback.sol`

```
interface IApproveAndCallFallback {
    function receiveApproval(address _from, address _to, uint _value, address _token, bytes _data) external;
}
```

`TokenTDH.sol` lines 158-163:

```
    function approveAndCall(address spender, address to, uint value, bytes data) external returns (bool) {
        _allowed[msg.sender][to] = value;
        emit Approval(msg.sender, to, value);
        IApproveAndCallFallback(spender).receiveApproval(msg.sender, to, value, address(this), data);
        return true;
    }
```

## 3.6. Receive Approval

### Severity: Critical

### Description

`receiveApproval` function member of `TDHManager` does send the tokens once approved to the destination address `to` that was set by the user who first called `approveAndCall` instead of sending them to the contract address, meaning that the user will get to even take his token back since he gets to choose the destination address but also he will be accounted as token holder. this operation can be done an infinity of times making his `token_holders[_from]` balance high enough to take most of the dividends.

### Code snippet

`TDHManager.sol` line 109:

```
    function receiveApproval(address _from, address _to, uint _value, address _token, bytes data) external fromTDH {
        require( _from != owner );
        token_holders[_from] = token_holders[_from].add(_value);
        onlineFrozenTokens = onlineFrozenTokens.add(_value);
        // token_hoder_list.push(_from);
        ITRC20(_token).transferFrom(_from, _to, _value);
        emit FreezeTDH(_token, _from, _value);
    }
```

## 3.7. Distributed Token Withdrawal

### Severity: note (high)

### Description

This issue is described supposing that the previously described  "Transfer From" issue was fixed.

`withdrawTDH` is a public function that allows the user to withdraw the token distributed to him by the owner using `distributeToken`, however the user get the choose the `from`, meaning that the user that calls this function will be able to transfer any tokens that was approved to the contract address from a specific address.

### Code snippet

`TDHManager.sol` line 97-103:

```
    function withdrawTDH(address token, address from, address to) public {
        require(to != address(0) && to != address(this) );
        // transferTokenTo(token, to, mint_tokens[to]);
        ITRC20(token).transferFrom(from, to, mint_tokens[to]);
        mint_tokens[to] = 0;
        emit WithdrawTDH(to, mint_tokens[to]);
    }
```

### Recommendation

- Remove the call to approval in `distributeToken`.
- Directly transfer the distributed tokens when `distributeToken` is called and remove `withdrawTDH`.

## 3.8. Token Unlocking

### Severity: High

### Description

`unfreezeToken` function member of `TDHManager` is public, Any user can call `unfreezeToken` to unfreeze the balance of another user with a large amount of frozen tokens or multiple address to be able to get a higher dividends later on when the dividends are distributed by the owner .

### Code snippet

`TDHManager.sol` lines 112-120:

```
    function unfreezeToken(address _token, address _to, uint _value) public {
        require(unfreezable, 'stil_not_allowed');
        require( _to != address(0) && _to != owner );
        require( token_holders[_to] >= _value );
        
        ITRC20(_token).transferFrom(owner, _to, _value);
        token_holders[_to] = token_holders[_to].sub(_value);
        
        onlineFrozenTokens = onlineFrozenTokens.sub(_value);
        emit UnfreezeTDH(_token, _to, _value);
    }
```

### Recommendation

`to` address should be removed from the function parameters and set inside to be equal to msg.sender.

## 3.9. Dividends Distribution

### Severity: owner privileges

### Description

Multiple issues from gas consumption to owner privileges touch this function:

- Owner get to input the "_currentProfit" (investors should trust the owner to input the right parameter).
- Only Owner is allowed to call "dividends".
- Owner should call every address one by one to transfer the dividends to all the investors.

### Code snippet

`TDHManager.sol` lines 121-128:

```
    function dividends( address token_holder, uint _currentProfit ) public onlyOwner {
        token_holder.transfer(token_holders[token_holder].div(onlineFrozenTokens).mul(_currentProfit));
        // for(uint i = 0; i < token_hoder_list.length; i++) {
        //     token_hoder_list[i].transfer(token_holders[token_hoder_list[i]].div(onlineFrozenTokens)*_currentProfit);
        // }
        emit Dividends(true);
        unfreezable = false;
    }
```

### Recommendation

Such mechanism like dividends distribution should be completely automated to get more investors an take advantage of the trustless nature of the blockchain.
Developers should respect the withdrawal-pattern as presented [here](https://solidity.readthedocs.io/en/v0.4.24/common-patterns.html).

## 3.10. Dice Bet results

### Severity: owner privileges

### Description

- Please note that, the bet results are processed offchain which put the player in a bad position where the result can be manipulated.

## 3.11. Dice Bet 

### Severity: medium

### Description

`Dice` contract allow users to create a bet using `createBet` with a certain bet amount, the amount of the bet will be sent to the contract owner. Once the result of the bet computed the amount won by the player will be transferred using `transferTo` member of `Dice` contract as described in the report provided by the dev team, however the contract do not contain any ether since no function is implemented to refill the contract balance, meaning that the described logic cannot be used.
 

### Code snippet

`Dice.sol`

```
contract Dice is Ownable {
    
    event DicePayout(address indexed player, uint256 value);
    event CreateBet(address indexed player, uint256 value, bytes32[] data);
    
    constructor() public {
        require(msg.sender != address(0));
    }
    
    function createBet(bytes32[] data) public payable returns(bool) {
        require( msg.sender != address(0) );
        require( msg.sender != owner );
        owner.transfer(msg.value);
        return true;
    }
    function transferTo(address player, uint amount) public onlyOwner {
        require(player != address(0) && player != address(this));
        player.transfer(amount);
        emit DicePayout(player, amount);
    }

    function transferTokenTo(address token, address player, uint amount) public onlyOwner {
        require(player != address(0) && player != address(this));
        ITRC20(token).transfer(to, amount);
    }
}
```

## 3.12. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

## 3.13. Zero-address of token contract

### Severity: low

### Description 

`tdh_token_` variable as argument of the constructor should be checked for empty value. 

### Code snippet

`TDHManager.sol` line 51:

## 3.14. Double withdrawal (`transferFrom`) attack

### Severity: high

### Description

1. The first paragraph described general vulnerabilities of ERC-20 tokens that may be possible when using a token contract(`TokenTDH`) by a third party. But `TDHManager` contract contains a specific example of a token contract. In `distributeToken` function, the function `approve` of the token is called. The attacker can promote his transaction higher and pick up both approved amounts (`mint_tokens[player]` and `mint_tokens[player].add(minable_token)`).

2. And an easier way to conduct an attack is to withdraw tokens directly by calling the `transferFrom` method of token contract. In this way the `mint_tokens`  variable will not be reset and the next time the function `distributeToken` is called, approved amount will be increased, even if these funds have already been withdrawn.

### Code snippet

`TDHManager.sol`, line 90

```
    function distributeToken( address token, address player, uint _wageredTrx ) public onlyOwner {
        uint wagerTrx_ = getCurrentWagerTrx();
        uint minable_token = _wageredTrx.div( wagerTrx_ );
        cumulative = cumulative.add( minable_token );
        mint_tokens[player] = mint_tokens[player].add(minable_token);
        ITRC20(token).approve(player, mint_tokens[player]);
        emit DistributeToken(player, minable_token);
    }
```

### Recommendation

1. Use `increaseAllowance` instead `approve`.

2. Do not use `withdrawTDH` function and `mint_tokens` variable. There's no apparent reason to do that.

## 3.15. Attacker can reset funds of anyone

### Severity: high

### Description

An attacker can call `withdrawTDH` function with address of fake token(deployed by the attacker) and with `to`-address of attacked account. `mint_tokens` of attacked address will be reset.

### Code snippet

`TDHManager.sol` line 101:

```
    function withdrawTDH(address token, address from, address to) public {
        require(to != address(0) && to != address(this) );
        // transferTokenTo(token, to, mint_tokens[to]);
        ITRC20(token).transferFrom(from, to, mint_tokens[to]);
        mint_tokens[to] = 0;
        emit WithdrawTDH(to, mint_tokens[to]);
    }
```

### Recommendation

Do not use `withdrawTDH` function and `mint_tokens` variable. There's no apparent reason to do that.


# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/09f225c9159310682a192e78b73a4d22

https://gist.github.com/yuriy77k/6873e0533cedfc5e58c91a4e81aebbe1

https://gist.github.com/yuriy77k/324b2f848587b492ee69e23f80492c75
