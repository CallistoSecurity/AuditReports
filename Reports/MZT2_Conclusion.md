# 1. Summary

[MZT2](https://gist.github.com/Mizhentaotuo/f16aeb6fc4f697e64a589a0cbeda5e58) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

# 3. Findings

In total, **5 issues** were reported including:

- 2 high severity issues.

- 2 medium severity issues.

- 1 low severity issues.

## 3.1. Incorrect definition of block id.

### Severity: medium

### Code snippet

* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L1039
* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L1077

### Description

Definition of block number looks like:
```solidity
uint256 blockID_ = xTo_ +  (totalRow_ *  yTo_);
```
But the y-coordinate must be multiplied by the number of board columns, not by the number of rows. Thus we get the number of cells above the current row. In this case `totalRow_` and `totalColumn_` are the same, but this can be changed and then the calculation will be incorrect.

Also `uint256 blockID_` can be overflowed by big input values of coordinates.

### Recommendation

The formula should be like this:
```solidity
uint256 blockID_ = xTo_ +  (totalColumn_ *  yTo_);
```

## 3.2. It is possible to place a piece to any cell even in preparation period.

### Severity: high

### Code snippet

* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L743

### Description

There is no check of maximum x and y coordinate in the `teamRegion` method. It is possible to put in the input value more than 300.

For example, if I am a member of the team 2, then during the preparatory period I can put pieces only on upper right corner of board. But I can put `(320, 10)` coordinates as parameters, then I get `blockID_ = 320 + (300 * 10) = 3320`. The 3320 is id of cell with coordinates `(20,11)` that in the upper left quarter that belonging to team 1.

### Recommendation

Check input variables for maximum values.


## 3.3. It's easy to guess the winner team.

### Severity: high

### Code snippet

  * https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L414

### Description

Everyone sees the situation on the board and Everyone sees the situation on the board and can make a bet at the moment when the winner becomes obvious. The `guessTeam` function is available for call on any moment. The game will not be interesting in such conditions.

## 3.4. A player can be a member of all teams in one round.

### Severity: medium

### Code snippet

  * https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L379

### Description

A player may join the several teams or join one team several times. There is no check for membership:
```solidity
playerJoinRound_[_customerAddress].push(roundID_);
```
And then player will get dividends for each join to the winner team:
```solidity
for (uint256 i = 0; i < playerJoinRound_[_customerAddress].length; i++) {
    uint256 round_ = playerJoinRound_[_customerAddress][i];
    uint8 teamID_ = playerTeam[_customerAddress][round_];
    totalProfit_ = SafeMath.add(totalProfit_, winningPerShareTeam_[round_][teamID_]);
}
```

## 3.5. Actions are possible even if the game is not active.

### Severity: low

### Code snippet

* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L364

* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L397

* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L420

* https://gist.github.com/gorbunovperm/e195e811d0af7844c4a8d6db0fd8f647#file-mzt2-L449

### Description

Function `gameActiveCheck` is called in many other function. It sets the correct value of the variable `gameActive_`. But `gameActive_` used for conditions only in `movePiece` and `placePiece` functions. In other functions, calling of `gameActiveCheck` is useless.

It's just a reminder that `gameActiveCheck` is only sets the variable `gameActive_` without any `revert`. And if you want to restrict any operations in prep period you should add additional conditions.


# 4. Conclusion

The contract has high severity issues and is not safe to deploy.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/9f734c4d56b20830e7d7bd90067de467

https://gist.github.com/yuriy77k/d04a245ea04cbd31e8bc6f843888dcd6

https://gist.github.com/yuriy77k/a1d7e6ec9a6fa683206899e5a3e0a7a9
