# MChef Security Audit Report

# 1. Summary

[MChef](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://goswapp.io/

# 2. In scope

Contract commit [578705b62cbe1b48d9f4a029eac518d850259d04](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract)

# 3. Findings

In total, **5 issues** were reported including:

- 0 high severity issues.

- 1 medium severity issues.

- 2 low severity issues.

- 2 notes.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(address recipient, ...)` function:

```
require( recipient != address(this) );

```

## 3.2. Unused parameter `months`

### Severity: note

### Description

In the function [setRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1257) there is parameter `months` but it's not using in the function logic.


## 3.3. The function `bonusPendingToken()` may return wrong result

### Severity: low

### Description

If function [bonusPendingToken()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1319-L1333) calling not by `_user` it will return wrong result. Because it use [msg.sender](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1324) to get `UserInfo`.

### Recommendation

At [line 1324](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1324) use this code:
```
UserInfo storage user = userInfo[_pid][_user];
```

## 3.4. High Gas spending

### Severity: note

### Description

The function [massUpdatePools()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1336-L1341) updates every existing pool. It will use many gas if will be many pools. 
This function is calling from others functions: [add()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1212), [set()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1234) (it's possible not update pools from them), [updateRewardPerBlock()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1244), [setRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1259), [updateRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1267), so Gas usage will be high i those functions too.

However, using `massUpdatePools()` in the functions [setRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1259), [updateRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1267) is not required and may be removed from them.

## 3.5. `rewardSender` may block users' deposits and withdrawals.

### Severity: medium

### Description

The function [safeRewardTokenTransfer()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1437-L1440) transfer tokens from `rewardSender` address to user. But in case there is not enough tokens or `rewardSender` did not approve transfers the transaction will be reverted.
This function calls from [deposit()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1379) and [withdraw()](https://github.com/GoSwapp/liquiditymining/blob/578705b62cbe1b48d9f4a029eac518d850259d04/contract#L1406) functions. Therefore it will be reverted too in that cases.

### Recommendation

Use `safeRewardTokenTransfer()` like this:
```solidity
    function safeRewardTokenTransfer(address _to, uint256 _amount) internal {
        if (IERC20(token).balanceOf(rewardSender) >= _amount && IERC20(token).allowance(rewardSender, address(this)) >= _amount)
            IERC20(token).transferFrom(rewardSender, _to, _amount);
    }
```

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

