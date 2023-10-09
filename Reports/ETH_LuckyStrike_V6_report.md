# LuckyStrike v.6 Security Audit Report

# 1. Summary

[LuckyStrike v.6](https://lucky-strike.io/game/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Lucky Strike, based fully in Ethereum smart-contract, is bringing the core philosophy of blockchain to the gambling industry – enhancing it with an ICO model we’re calling ‘Bet & Own.’

https://lucky-strike.io/game/#/

# 2. In scope

1. [LuckyStrike](https://ropsten.etherscan.io/address/0x1a77110391c07d3d67c8c55c6114a858cb45bb26#contracts)
2. [LuckyStrikeTokens](https://ropsten.etherscan.io/address/0xc3e2b080aefc0d9d5b5e30614e892b5fd1276777#contracts)

# 3. Findings

In total, **5 issues** were reported including:

- 1 Critical severity issues.

- 1 medium severity issues.

- 1 low severity issues.

- 1 notes.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. An attacker can block the contract

### Severity: critical

### Description

From previous audit:

> In current version the draw takes place by queue and each bet is played out one by one. In case of victory, the winner is paid a reward by `transfer` function. The peculiarity of this function is that in the case of `throw` on the recipient's side the entire transaction will be rollbacked. `throw` can be done intentionally by an attacker, if the recipient is another smart contract. Thus, the attacker can block the entire contract, making it impossible to place bets and draws.

`isContract` function is not prevented this vulnerability because `extcodesize(address)` worked only for already deployed contracts. If an external function(`placeABet()`) is called from the `constructor` of an malicious contract then `extcodesize(_attacker)` is zero.

Simple example of an Attackers contract:

```Solidity
contract Attacker {
    LuckyStrike public ls = LuckyStrike(address(0xa819effd0a9c86619953a4b979079e9ba4dca3f2));
    bool public blockMode = true;

    constructor() public payable {
        ls.placeABet.value(msg.value)();
    }
    
    function turnBlockModeOn() public {
        blockMode = true;
    }
    
    function turnBlockModeOff() public {
        blockMode = false;
    }
    
    function () payable external {
        if(blockMode) {
            revert(); // LuckyStrike blocked;
        }
    }
}
```

#### Code snippet

* [LuckyStrike, lines 1762, 1995, 1923](https://ropsten.etherscan.io/address/0x1a77110391c07d3d67c8c55c6114a858cb45bb26#contracts)

#### Recommendation

Use `require(msg.sender == tx.origin)` to prevent smart contract calling.

## 3.2. Truncated Value (Invest & Play)

### Severity: medium

### Description

Following the answer to this issue, the tokens are said to be like a bonus for the players but the amount to be invested is subtracted from `msg.value` ("bet amount = msg.value - invested amount") when invest and play is called meaning that this issue is still applicable.

Please refer to the [previous audit issue description](https://gist.github.com/RideSolo/67652c80c78194296ae24affd8779db2#31-truncated-value-invest--play) to solve this error.

## 3.3. Sum Validation

### Severity: note

### Description

Inside `allocateSum` member of the game contract contain `sumValidationPassed` variable that is used to check if the allocated sum values are correct however no action is taken following the result of it.

### Code snippet

```
        bool sumValidationPassed = false;
        
        if (
            (jackpotsSumAllocation[1] +
            jackpotsSumAllocation[2] +
            jackpotsSumAllocation[3] +
            jackpotsSumAllocation[4] +
            incomeSum +
            refSum +
            payToWinner) == _sum) {
            sumValidationPassed = true;
        }
```

## 3.4. Owner Privileges

### Severity: owner privileges
 
### Description:

- `adjustAllocation` function allows the owner to reset the rates of the different jackpots and income rate as wished.
- 70M tokens are first distributed by the owner that represent 10500 ether, the token sale hardcap is 4500 ether,  meaning that the developers allow them self more than a third of the total income of the bet game, investors have to be aware of such usage.

### Code snippet

- [Line 1590-1628](https://ropsten.etherscan.io/address/0x1a77110391c07d3d67c8c55c6114a858cb45bb26#contracts)
- [Line 159-162](https://ropsten.etherscan.io/address/0xc3e2b080aefc0d9d5b5e30614e892b5fd1276777)


## 3.5. No event call

### Severity: low

### Description

According to ERC20 standard when coins are minted(or burned) a Transfer event should be emitted.
In function `mint(address to, uint256 value, uint256 _invested)` in line 339 in [token contract](https://ropsten.etherscan.io/address/0x28bab51604628323fc46459b2f90590597ba060d#code) there is no `Transfer` event call that funds transferred from zero address to `to` address.

```solidity
         balanceOf[to] = balanceOf[to].add(value);
```
Also `Transfer` event has not been called in function `init(address luckyStrikeContractAddress)` in line 142 in [token contract](https://ropsten.etherscan.io/address/0x28bab51604628323fc46459b2f90590597ba060d#code) after setting balances of addresses in lines 159-162.
```solidity
        balanceOf[0x7E6CdeE9104f0d93fdACd550304bF36542A95bfD] = 33040000;
        balanceOf[0x21F73Fc4557a396233C0786c7b4d0dDAc6237582] = 8260000;
        balanceOf[0x23a91B45A1Cc770E334D81B24352C1C06C4830F6] = 26600000;
        balanceOf[0x961f5a8B214beca13A0fdB0C1DD0F40Df52B8D55] = 2100000;
```

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c4ab2c36433dd229e223149b296ed89f

https://gist.github.com/yuriy77k/1d3b7b4f7a2f3be02f7068944281315c

https://gist.github.com/yuriy77k/1ca25cd18a61ab86dfcf4e0a76b03580
