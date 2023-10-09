# 1. Summary

[MUST Protocol](https://github.com/MUSTprotocol/smart-contracts) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Burner.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/Burner.sol) github commit hash c37a04227ec01d78d8719e0319b815b03b6ae07b.
- [MustToken.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/MustToken.sol) github commit hash a8dd497893fa4b0afcf4a3a8e8adf79aba8afd26.
- [TokenBucket.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/TokenBucket.sol) github commit hash d3300541e8d5b88259567aac8d5c8e66ae5fe69c.
- [ERC223ReceiverMock.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mock/ERC223ReceiverMock.sol) github commit hash d4ffe0348b3b01d93d816d879a1f7a05bf3f6d6a.
- [ERC223TokenBurnerMock.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mock/ERC223TokenBurnerMock.sol) github commit hash d4ffe0348b3b01d93d816d879a1f7a05bf3f6d6a.
- [RBACMock.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mock/RBACMock.sol) github commit hash 587390ca503ece05dda224e81a82aef30fed5f7b.
- [ERC223Mixin.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mixins/ERC223Mixin.sol) github commit hash 5a589cc528d9ee4348714d831577e7fc9f9ae444.
- [ERC223ReceiverMixin.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mixins/ERC223ReceiverMixin.sol) github commit hash d4ffe0348b3b01d93d816d879a1f7a05bf3f6d6a.
- [RBACERC223TokenFinalization.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mixins/RBACERC223TokenFinalization.sol) github commit hash 587390ca503ece05dda224e81a82aef30fed5f7b.
- [RBACMintableTokenMixin.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mixins/RBACMintableTokenMixin.sol) github commit hash a8dd497893fa4b0afcf4a3a8e8adf79aba8afd26.
- [RBACMixin.sol](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mixins/RBACMixin.sol) github commit hash 20845c987018dd2bfaf91a32918cbc139b51655b.

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerability of ERC-20 token

### Severity: low

### Code snippet

* [ERC20.sol, line 79](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/9b3710465583284b8c4c5d2245749246bb2e0094/contracts/token/ERC20/ERC20.sol#L79)

### Description

It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

## 3.2 Last version of OpenZeppelin `MintableToken.sol` contract doesn't have `finishMinting()` function.

### Severity: low

### Code snippet

* [MustToken.sol, Line 49](https://github.com/MUSTprotocol/smart-contracts/blob/7bbd6418fe18da3bee2c169adf52edc9e93a608d/contracts/MustToken.sol#L49)

### Description

Mintable token of OpenZeppelin solution in up to date version doesn't have `finishMinting()` function. The reason is described [here](https://github.com/OpenZeppelin/openzeppelin-solidity/issues/1348).

### Recommendation

Use the latest version of [this token](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC20/ERC20Mintable.sol) and change the mechanism of stop the minting if this is required. Or use older version of this token, but `MintableToken.sol` not audited, because we do not know the version you were considering at the time of this contract development.

## 3.3. Zero address transfer and mint. 

### Severity: low

### Description

In functions [`transferFrom`](https://github.com/MUSTprotocol/smart-contracts/blob/7bbd6418fe18da3bee2c169adf52edc9e93a608d/contracts/mixins/ERC223Mixin.sol#L11), [`transfer`](https://github.com/MUSTprotocol/smart-contracts/blob/7bbd6418fe18da3bee2c169adf52edc9e93a608d/contracts/mixins/ERC223Mixin.sol#L49), [`mint`](https://github.com/MUSTprotocol/smart-contracts/blob/master/contracts/mixins/RBACMintableTokenMixin.sol#L23) and [`upgrade`](https://github.com/MUSTprotocol/smart-contracts/blob/7bbd6418fe18da3bee2c169adf52edc9e93a608d/contracts/Migrations.sol#L23) there are no zero address checking.

### Recommendation

Add zero address checking for protecting users from token lose.
```solidity
require(_to != address(0));
```

# 4. Conclusion

This contract can be considered safe.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/a5db6053bc68a720884babf134a72f46

https://gist.github.com/yuriy77k/d7a1ab5aabe6a77257ba35e296529b2c

https://gist.github.com/yuriy77k/82f32673089e9916509d3e29ddbbd4ad


