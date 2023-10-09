# 1. Summary

[Bulleon Tokens](https://github.com/Bulleon/contract/tree/v2/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Standard ERC20 token functions. Token burn. Whitelisting transfer while ICO is on specific address. Unpausing the transfer function by anyone who have token in their wallet. Setting crowdsale address.

# 2. In scope

Commit hash: `5e9adec0afdd879afcc109dd9e48f07d2244e673`
 
1. [BulleonCrowdsale.sol](https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonCrowdsale.sol)
2. [BulleonToken.sol](https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonToken.sol)
3. [Migrations.sol](https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/Migrations.sol)
4. [Multitransfer.sol](https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/Multitransfer.sol)

# 3. Findings

In total, **8 issues** were reported including:

- 5 low severity issues.

- 3 minor observation.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.2. Missing event call.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.
An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonToken.sol#L34-L42

## 3.3. Incorrect `endDate`.

### Severity: minor observation

### Description

Now it is equal to December 31, 2018. This means that the crowdsale is not active (`isActive() == false`) and sales are prohibited.

### Code snippet

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonCrowdsale.sol#L21

## 3.4. Multitransfer contract zero address checking

### Severity: low

### Description

It is necessary to check token address for zero-value. Otherwise it is possible to lose tokens.

### Code snippet

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/Multitransfer.sol#L19

## 3.5. Wrong bonus checking

### Severity: minor observation

### Description

In function `setBonus` there is possibility for owner to set high bonus amount. At line 242 bonus coefficient multiplied by 100 and it's equal to 10000% (in line 58 there written that value 1000 = 100%).

### Code snippet

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonCrowdsale.sol#L240

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonCrowdsale.sol#L58

### Recommendation

Change the checking to like below

```solidity
require(bonusAmount < BONUS_COEFF && bonusAmount >= 0);
```

## 3.6. The relocating is not secure process for investors.

### Severity: low

### Description

1. The owners can implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure. 

2. It is necessary to check the input address to the zero-address.

### Code snippet

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonToken.sol#L47-L51

## 3.7. Anyone can call Unpase function 

### Severity: minor observation

### Description

In audit request said that unpausing the transfer function by anyone who have TOKEN IN THEIR WALLET, but `unpause()` don't check sender's token balance.

### Code snippet

https://github.com/Bulleon/contract/blob/5e9adec0afdd879afcc109dd9e48f07d2244e673/contracts/BulleonToken.sol#L73

## 3.8. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to:

* Change bonus amount;

* Missing burn function after crowdsale;

* Withdraw funds at any time during a crowdsale.

The contract is managed manually by the owner which is not good for investors.

# 4. Conclusion

The review did not show any critical issues, but some low severity issues were found. The crowdsale

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5d82197f87686517d46195e63f02975f

https://gist.github.com/yuriy77k/8e9d19f1fabf9b64cd4ab115004f241e

https://gist.github.com/yuriy77k/0fe35fdea7bb4ae366368bf4ebd0c00a
