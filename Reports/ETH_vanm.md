# 1. Summary

[VANM token](https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> It's main purpose was/is...

> • the creation of the VANM token (ERC20)

> • handling our token sale in two phases including balance checks, overflow and whitelisting.

https://www.vanm.io/

# 2. In scope

Commit hash: `1c0995c827b200c76e90efe3a809096a71251581`

1. [VANMToken.sol](https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol)

# 3. Findings

In total, **6 issues** were reported including:

- 5 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1 Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. It is necessary to check the input address to the zero-address.

### Severity: low

### Description

In the `setPresaleAddress` and `setCrowdsaleAddress` function, the input address is not checked for a null value and the funds can be transferred to a `0x0`-address.

```solidity
    balances[_presaleAddress] = balances[_presaleAddress].add(presaleSupply);
```

### Code snippet

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L232

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L252

## 3.3. Missing event call.

### Severity: low

### Description

In the constructor is the event of the transfer of funds to address `platformAddress`, but there is no event on the transfer of funds to address `incentivisingAddress`.

```solidity
    balances[platformAddress] = platformSupply;
    emit Transfer(0x0, platformAddress, platformSupply);

    balances[incentivisingAddress] = incentivisingSupply;
```

### Code snippet

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L225

## 3.4. Invalid event argument.

### Severity: low

### Description

In functions `transferFromIncentivising`, `transferFromPresale` and `transferFromCrowdsale` the `Transfer` event is called but as argument **from** is used `0x0` instead address of corresponding contract. This can be misleading.

### Code snippet

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L321

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L332

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L343

## 3.5. More balance at `incentivising` address

### Severity: low

### Description

In `constructor` there is setting 20% of tokens to `incentivisingAddress`, but this address is equal to zero address, because it's not initialized. In functions `transferFromPresale` and `transferFromCrowdsale` there are no zero address checking and tokens could be sent to zero address by admins of presale and crowdsale. It means that balance of incentivising address will be greater than 20%.

### Code snippet

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L225

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L326

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L337

### Recommendation

Initialize `incentivisingAddress` before setting its balance.

## 3.6. Deprecated method.

### Severity: minor observation

### Description

The function () payable { revert(); } was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded. 

### Code snippet

https://github.com/VANMofficial/Token-Sale/blob/1c0995c827b200c76e90efe3a809096a71251581/VANMToken.sol#L377-L379

# 4. Conclusion

No major vulnerabilities were found but pointed issues need addressed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/473a271e22c53948c06fb525b4a63a4a

https://gist.github.com/yuriy77k/00ebeedf68be6dc0acd07e1052a50408

https://gist.github.com/yuriy77k/ce842ba28365844c6ba3ac4615f56b45

