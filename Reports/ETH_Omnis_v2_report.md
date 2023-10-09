# Omnis v2 Security Audit Report

# 1. Summary

[Omnis v2](https://drive.google.com/file/d/1OMN_WcIWpCvmtkOEcaDYhawwDTm1Qm1y/view) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> OMNIS is the ERC20 token that brings everyone closer to the cryptocurrencies' world with its services.

https://www.omnis-bit.com

# 2. In scope

[OMNIS-testChanges.sol](https://drive.google.com/file/d/1OMN_WcIWpCvmtkOEcaDYhawwDTm1Qm1y/view)

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

## 3.2. Lost of all staked balance

### Severity: medium

### Description

If some address will get lot of transfers each of them will be added to his staked balance, but if he will `transfer`(line 384) or `transferFrom` (line 414) some amount of tokens he will lose all his staked balance. And even after he will receive this transfered value again, he will get only staked amount calculated using this amount. Look comments:

```solidity
if (staked[_from] != 0) staked[_from] = 0;  <- here he will lose all his staked amount
uint64 _now = uint64(now);
lastTransfer[_from] = transferInStruct(uint128(balances[_from]), _now);  <- here will be stored only last value, so in case he will transfer 1 wei, he will get almost nothing then

if (_from != _to) { //Prevent double stake

     if (uint(lastTransfer[_to].time) != 0) {
           uint nCoinSeconds = now.sub(uint(lastTransfer[_to].time));
           if (nCoinSeconds > stakeMaxAge) nCoinSeconds = stakeMaxAge;
           staked[_to] = staked[_to].add(uint(lastTransfer[_to].amount).mul(nCoinSeconds.div(1 days)));  <- in case someone will sent to us some value, there will be called this line and we will get 1 wei multiplied to the time, so we will lost all the staked before amount.
     }

     lastTransfer[_to] = transferInStruct(uint128(_value), _now);
}
```
Also same issue in function `claimAirdrop` in line 655.

## 3.3. Owner Privileges

### Severity: owner privileges

### Description

The contract owner allowed to freeze transfer functions for a specific user. (`transfer`, `transferFrom`).

### Code snippet

Line 703

# 4. Conclusion

The audited smart contract has issue with staking rewards. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5e22bcbcd2d56db93c459fda35ad942b

https://gist.github.com/yuriy77k/7208e54cb1f04f4b36ebe827d7c59bc9

https://gist.github.com/yuriy77k/8f6ccab50225067394ac32e06a86ed15
