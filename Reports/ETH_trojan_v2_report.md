# 1. Summary

[Trojan tokens v2.](https://github.com/TROJANFOUNDATION/Private-sale-contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> This is the smart-contract that will manage the crowdsale of Trojan tokens.

# 2. In scope

Commit hash: `e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118`
 
1. [CustomAdmin.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/CustomAdmin.sol)
2. [CustomCappedCrowdsale.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/CustomCappedCrowdsale.sol)
3. [CustomPausable.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/CustomPausable.sol)
4. [Migrations.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/Migrations.sol)
5. [TrojanSale.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/TrojanSale.sol)
6. [WhiteList.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/WhiteList.sol)
7. [mocks/ERC20Mock.sol](https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/mocks/ERC20Mock.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

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

## 3.2. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to:

* change the buy and sell price of the tokens at any moment;
* change value of MaxCap and MinCap;
* the pause functions of contract.

This contract is managed manually by the owner which is not good for investors.

### Code snippet

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/TrojanSale.sol#L55

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/TrojanSale.sol#L65

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/TrojanSale.sol#L76

https://github.com/TROJANFOUNDATION/Private-sale-contracts/blob/e0c7e5dd5a939ef15d28e04dd04e8b4b20b46118/contracts/CustomPausable.sol#L35

# 4. Conclusion

There is no serious vulnerabilities were found here. The audited contract can be deployed

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/62a9c91d1851f20da6dbb8ea0507add2

https://gist.github.com/yuriy77k/c719459b3a90a0c1ee6b87567f814515

https://gist.github.com/yuriy77k/3dccb40d7fd839c9d41c9e2f90ce04cb
