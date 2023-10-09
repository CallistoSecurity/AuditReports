# SOY Farming Security Audit Report

# 1. Summary

[SOY Farming](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [b166fee968b6a10f30ab564c146260878b7b73b8](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/)

- SOYGlobalFarmSimplified.sol
- SOYLocalFarm.sol

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **6 notes** were reported, including:

- 2 notes.

- 4 owner privileges.


## 3.1. Reusing the same storage value - gas wasting

### Severity: note

### Description

When a contract read a value in a storage it costs about 500 gas. If in a function you are using the same value in a storage more than once will be gas-effective to assign that value to local variable and use the local variable wherever need.

In the contract `GlobalFarm` the storage variable `localFarmId[_localFarmAddress]` used many times in many functions:

1. In the function [removeLocalFarmByAddress()](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L194-L200);
2. In the function [changeMultiplier()](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L211-L213);
3. In the function [mintFarmingReward](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L228-L237)

### Recommendation

To reduce gas cost of using those functions should be used local variable `uint256 _localFarmId = localFarmId[_localFarmAddress]` instead of storage.

For example:
```Solidity
    function removeLocalFarmByAddress(address _localFarmAddress) external onlyOwner {
        require (farmExists(_localFarmAddress), "LocalFarm with this address does not exist");
        uint256 _localFarmId = localFarmId[_localFarmAddress]; 
        
        totalMultipliers = totalMultipliers - uint256(localFarms[_localFarmId].multiplier); // update totalMultipliers
        
        delete localFarms[_localFarmId]; // delete LocalFarm structure - automatically assign 0 to each element (reduce transaction gas cost).
        
        localFarmId[_localFarmAddress] = 0;
        
        emit RemoveLocalFarm(_localFarmAddress);
    }
```

## 3.2. Possible loosing accuracy in reward calculation

### Severity: low

### Description

In the function `mintFarmingReward()` to calculate [_reward](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L235) the nominator 1000 was used to get allocation in the `getAllocationX1000()` function. In case of growing numbers of local farms and `totalMultipliers` accordingly the accuracy may be lost due to small nominator value.

Also in this function the `next_payment()` used many times. To reduce gas usage use `uint256 _nextPayment = next_payment();` and use local variable `_nextPayment` wherever needed.

### Recommendation

Use this fixed function:
```Solidity
    function mintFarmingReward(address _localFarmAddress) external {
        require (farmExists(_localFarmAddress), "LocalFarm with this address does not exist");
        uint256 _localFarmId = localFarmId[_localFarmAddress]; 
        uint256 _nextPayment = next_payment();
        
        // Comparing against 0:00 UTC always
        // to enable withdrawals for full days only at any point within 24 hours of a day.
        if(localFarms[_localFarmId].lastPayment + paymentDelay > _nextPayment)
        {
            // Someone is requesting payment for a Local Farm that was paid recently.
            // Do nothing.
            return;
        }
        else
        {
            uint256 _reward = (_nextPayment - localFarms[_localFarmId].lastPayment) * getRewardPerSecond() * localFarms[_localFarmId].multiplier / totalMultipliers;
            localFarms[_localFarmId].lastPayment = _nextPayment;
            rewardsToken.mint(_localFarmAddress, _reward);
            ILocalFarm(_localFarmAddress).notifyRewardAmount(_reward);
        }
    }
```


## 3.3. Multiple cross-contract calling - gas wasting

### Severity: note

### Description

The cross-contracts calls cost additional 2000 gas, to reduce gas usage minimize number of cross-contracts calls.

The functions [getRewardPerSecond](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYLocalFarm.sol#L309-L312) and [getAllocationX1000](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYLocalFarm.sol#L314-L317) calls related functions in the `GlobalFarm` contract. But they are used only in pattern `uint256 _reward = multiplier * getRewardPerSecond() * getAllocationX1000() / 1000;` [here](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYLocalFarm.sol#L328) and [here](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYLocalFarm.sol#L354). 

### Recommendation

To reduce transaction gas cost create in the `GlobalFarm` contract function:
```Solidity
function getReward(uint256 interval) external view returns(uint256 reward) {
    reward = interval * getRewardPerSecond() * localFarms[localFarmId[msg.sender]].multiplier / totalMultipliers;
}
```

and call it instead of `multiplier * getRewardPerSecond() * getAllocationX1000() / 1000;` pattern.

## 3.4. The owner privileges of `LocalFarm` contract

### Severity: owner privileges

### Description

The `LocalFarm` contract owner has right to [withdraw](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYLocalFarm.sol#L389-L395) all rewards token of inactive farming contract even if users did not claimed their rewards.

### Recommendation

Check that all users withdraw their LP tokens adding this requirements:

```Solidity
    require(lpToken.balanceOf(address(this) == 0, "Not all users quit");
```

## 3.5. The owner privileges of `GlobalFarm` contract

### Severity: owner privileges

### Description

The owner of of `GlobalFarm` contract has right to:

1. [Add](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L147-L163)/[remove](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L190-L205) local farm contracts
2. [Change](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L207-L214) local farm contract multiplier (weight).
3. [Change](https://github.com/SoyFinance/smart-contracts/blob/b166fee968b6a10f30ab564c146260878b7b73b8/Farming/SOYGlobalFarmSimplified.sol#L216-L220) tokens per year.

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
