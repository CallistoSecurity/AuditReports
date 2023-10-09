# Security Audit Report

# 1. Summary

[JoyArt](https://etherscan.io/address/0x96313f2C374F901E3831ea6DE67b1165c4f39A54#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash: `c2d356979c3802bc7f25e731d8c4ca208feb51cc`

1. [JoyArt.sol](https://github.com/JohnOrionYoung/johnorionyoung/blob/c2d356979c3802bc7f25e731d8c4ca208feb51cc/Contracts/JoyArt.sol)

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-721 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Owner's Privileges

### Severity: low

### Description

The contract owner allow himself to pause functions of contract (`purchase`, `transfer`, `transferFrom`, `approve`, `takeOwnership`).

### Code snippet

https://github.com/JohnOrionYoung/johnorionyoung/blob/c2d356979c3802bc7f25e731d8c4ca208feb51cc/Contracts/JoyArt.sol#L52-L71

## 3.3. View Functions Gas Limit

### Severity: low

### Description

Even if a view function does not need ether for execution when it is called externaly, developers should think about implementing loops inside such functions since a gas limit can still be applied by the node that execute the function to avoid overloading the server that is running the node.

The UI rely on function such as `getAllTokens` and `tokensOf` to get information about tokens and token holders, if the total supply become high enough the function will probably throw if the gas limit for view function is set too low for a particular node.

If the gas limit for view function called externaly is set to high the function call will probably work, but in this case if the node inteface is public then a possible denial of service attack can be possible. 

### Code snippet

https://github.com/RideSolo/johnorionyoung/blob/master/Contracts/JoyArt.sol#L207

https://github.com/RideSolo/johnorionyoung/blob/master/Contracts/JoyArt.sol#L226

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b5fca088728055aa4683f5d46d25adc1

https://gist.github.com/yuriy77k/bdd6b5719b89c5da98646384f9d1ec61

https://gist.github.com/yuriy77k/4b966c14c250999441b728918d1530e5

