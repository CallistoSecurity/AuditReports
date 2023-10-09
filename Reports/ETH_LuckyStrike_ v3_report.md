# Lucky Strike v3 Security Audit Report

# 1. Summary

[Lucky Strike v3](https://ropsten.etherscan.io/address/0x78c32ffb7d209457a75e6c25854f19de58d64a4b#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

1. [LuckyStrike](https://ropsten.etherscan.io/address/0x78c32ffb7d209457a75e6c25854f19de58d64a4b#code)

2. [LuckyStrikeTokens](https://ropsten.etherscan.io/address/0xfd9f46d87625f1f8ee06fdb7f5e93c745005aae2#code)

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issues.

- 1 notes.

- 1 owner privileges (ability of owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. It is possible to use Ahead of The Curve strategy

### Severity: medium

### Description

Formula of the random calculation is:

```solidity
    bytes32 seed = keccak256(
        block.blockhash(lastInstantGameBlockNumber[player])
    );

    uint256 randomNumber = uint256(seed) % ticketsInTheInstantGame;
```

The result of the player's fight with the King of the hill is known immediately after the player has placed a bet. But the result will be applied only after calling the `play` method. This way different players can use information about bets(and block hashes) for their own profit.

- For example, the King of the hill can choose to fight with the enemy(among the few players who made bets) which will lose(the king knows the result in advance) and call `play(looser_address)` method. Thus, he can choose the order of battles, first collecting winnings, and then losing. 

- Another case if attacker is player. Knowing that he will win the King of the mountain(and he can find out), attacker initiates a fight with King ahead of other players. Further acting in accordance with paragraph 1. Or if attacker knows he's going to lose to the King, that he can wait for someone to fight with King and when the new King comes up the hill, fight him.

### Code snippet

[LuckyStrike, line 1936](https://ropsten.etherscan.io/address/0x78c32ffb7d209457a75e6c25854f19de58d64a4b#code)

### Recommendation

There should not be a gap between reliable information that someone won between the fact of this. A good solution would be to play all the instant games in the first block after the any bet. Even if there were several bets in one block, they all need to be played at the first next appeal to the contract.

## 3.2. Possibility of minting more than hardCap

### Severity: note

### Description

Function `mint` allows owner to mint more tokens than `hardCap`.

### Code snippet

[LuckyStrikeTokens, line 345](https://ropsten.etherscan.io/address/0xfd9f46d87625f1f8ee06fdb7f5e93c745005aae2#code)

### Recommendation

You should check `(invested + _invested) > hardCap` before minting and if it's true, mint only `hardCap - invested` number of tokens and return remainder to investor.

## 3.3. Owner Privileges

### Severity: owner privileges

### Description

`adjustAllocation` function allows the owner to reset the rates of the different jackpots and income rate.

### Code snippet

[LuckyStrike, line 1575](https://ropsten.etherscan.io/address/0x78c32ffb7d209457a75e6c25854f19de58d64a4b#code)

# 4. Conclusion

The audited smart contract must not be deployed. Medium severity issue must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/499ce0873c5827863c85ec75aded02cc

https://gist.github.com/yuriy77k/7272da582aff4271a908ca106ef9086d

https://gist.github.com/yuriy77k/e4b4e4f96d65e67683fcf76ba1967ff6
