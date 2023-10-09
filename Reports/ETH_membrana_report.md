# 1. Summary

[Membrana](https://drive.google.com/file/d/1FjNbE21tNWmrTgfZ60FP3UeGPYhzs2u0/view?usp=sharing) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [Deploy.sol](https://github.com/yuriy77k/Membrana-ICO/blob/master/Deploy.sol) github commit hash 37cd100748e1ef5bf9593cc9e6c20c5c358a1b54.
- [Presale.sol](https://github.com/yuriy77k/Membrana-ICO/blob/master/Presale.sol) github commit hash 37cd100748e1ef5bf9593cc9e6c20c5c358a1b54.
- [Token.sol](https://github.com/yuriy77k/Membrana-ICO/blob/master/Token.sol) github commit hash 37cd100748e1ef5bf9593cc9e6c20c5c358a1b54.
- [Treasure.sol](https://github.com/yuriy77k/Membrana-ICO/blob/master/Treasure.sol) github commit hash 37cd100748e1ef5bf9593cc9e6c20c5c358a1b54.

# 3. Findings

In total, **7 issues** were reported including:

- 2 high severity issues.

- 2 medium severity issues.

- 3 low severity issues.

## 3.1. Party Adding

### Severity: medium

### Description

The function `addParty` member of `Treasure` contract is public, anyone can introduce an address an be part of the voting system (multiple addresses can be added to control majority vote), however `finalize` function can be called to stop adding addresses but it doesn't prevent them from adding address when the contract is not finalized. Contract developers should add a security mechanism to avoid this.

### Code snippet

https://github.com/yuriy77k/Membrana-ICO/blob/master/Treasure.sol#L43

https://github.com/yuriy77k/Membrana-ICO/blob/master/Treasure.sol#L115

## 3.2. The owner can purchase tokens for any address at any price.

### Severity: low

### Description

The function `transfer` allows for owner to mint any value of tokens to any address. It can cause the following problems:
* owner can mint any amount of tokens to himself;
* it's possible to make a mistake and transfer the wrong amount to the buyer (there is no relation between the investor's balance and the number of tokens purchased).

### Code snippet

https://github.com/yuriy77k/Membrana-ICO/blob/91cd70c7df4c7808c5ef23cee9a9416cda793347/Presale.sol#L72

## 3.3. Misuse of Safemath Library

### Severity: high

### Description

Safe math library function do not assign a value to the uint but only return a value, the value returned should be assigned to the variable if the developers want to save its value.
As in the below Code snippets the number of up/down votes is not saved therefore the vote mechanism will never allow transfer process to execute, blocking any fund in the contracts.

### Code snippet

https://github.com/yuriy77k/Membrana-ICO/blob/master/Treasure.sol#L75

https://github.com/yuriy77k/Membrana-ICO/blob/master/Treasure.sol#L92

### Recommendation

Assign the variable like below:

```    votes_[_to] = votes_[_to].add(1);```
and
```    votes_[_to] = votes_[_to].sub(1);```

## 3.4. Release Date

### Severity: high

### Description

The `release` function member of `Presale` contract should allow any user to release token transfer lock that is set in the token contract, but in the function logic it is the opposite the function should be called before the the release time end (`releaseTime_ > block.timestamp`). The result of such issue is the unlimited block of transfer on the token contract if the function isn't called before the release time.

### Code snippet

https://github.com/yuriy77k/Membrana-ICO/blob/master/Presale.sol#L125#L132

### Recommendation

Change code like below.

```
require(block.timestamp > releaseTime_);

```

## 3.5 Token Address

### Severity: medium

### Description

The `setToken` function member of `Presale` is public, any attacker can change the token address if he execute the function first (racing condition).

### Code snippet

https://github.com/yuriy77k/Membrana-ICO/blob/master/Presale.sol#L117#L122

## 3.6 Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.7 Incorrect cleanup

### Severity: low

### Description

The function `processTransfer` member of `Treasure` cleanup voting data before transferring. But it uses comparison instead assign operator.

### Code snippet

https://github.com/yuriy77k/Membrana-ICO/blob/master/Treasure.sol#L109

### Recommendation

Change code like below.

```
votesStatus_[_to][participants_[i]] = false;

```

# 4. Conclusion

The audited contracts are not safe and cannot be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/addb6a5f129b4e1611951d73a92c4d8b

https://gist.github.com/yuriy77k/878d9d31a3f0fb1683b33917ef9be39c

https://gist.github.com/yuriy77k/f7acd25f9aa448d1e78d3469e96b098b
