# [PeopleWave](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/master/contracts/PeopleWaveTokenFlat.sol) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited token smart contract does not contain any critical issue and can be deployed safely, but pay attention to the token symbol using in contract and website.

# 2. High severity issues:

No security issue.

# 3. Medium severity issues:

No security issue.

# 4. Low severity issues:

## 4.1. Known Issues of ERC20 Standard

### Description

PeopleWave token is ERC20 compatible, ERC20 Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

- Approve + transferFrom mechanism allows double Withdrawal attack.
- Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.


# 5. Minor observation:

## 5.1. Wrong symbol. 

### Description

Token symbol in contract `PPL`, but it has another symbol in [website](https://peoplewaveico.io) of the project. It should be `PWV`.

### Recommendation

Fix it before deploying.

## 5.2. No need of require. 

### Severity: minor

### Description

SafeMath.sub() will automatically throw, if someone will try send more, than he has. In [`transfer`](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/c7d8bb199a940d297ee5576522e2ef5a3b07e6e0/contracts/PeopleWaveTokenFlat.sol#L94) and [`transferFrom`](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/master/contracts/PeopleWaveTokenFlat.sol#L156) functions no need to check it with require.

### Recommendation

In lines [96](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/c7d8bb199a940d297ee5576522e2ef5a3b07e6e0/contracts/PeopleWaveTokenFlat.sol#L96), [165](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/master/contracts/PeopleWaveTokenFlat.sol#L165), [166](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/master/contracts/PeopleWaveTokenFlat.sol#L166) no need of require.

## 5.3. Representation note

### Severity: minor

### Description

['initialSupply'](https://github.com/PeoplewavePteLtd/Peoplewave-Token/blob/master/contracts/PeopleWaveTokenFlat.sol#L263) assigns a long number.

### Recommendation

Better to change the representation of this number. And write it as below:

```solidity
uint public constant initialSupply = 1200000000 * (10 ** uint256(decimals));
```

# Revealing audit reports:

https://gist.github.com/yuriy77k/927d31cb5c2c5d2e8bb5f5141bd50bda

https://gist.github.com/yuriy77k/8da30d72bfcb6343366a26b82d595309

https://gist.github.com/yuriy77k/d34a5f7401da469d44e606a432374daa

