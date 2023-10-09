# Security Audit Report

# 1. Summary

[FFM Token](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> There are two contracts - one for the token and one for the crowdsale. More info is provided in the readme on github.

https://files.fm/

# 2. In scope

Commit hash: `48889776bcaedc341a6d4fc8a8f01360995ac4a5`.

1. [ECRecovery.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/ECRecovery.sol)
2. [ERC777TokenScheduledTimelock.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/ERC777TokenScheduledTimelock.sol)
3. [FilesFMToken.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/FilesFMToken.sol)
4. [FilesFMTokenController.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/FilesFMTokenController.sol)
5. [Migrations.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/Migrations.sol)
6. [TokenRecoverable.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/TokenRecoverable.sol)
7. [eip820/contracts/ERC820Implementer.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/eip820/contracts/ERC820Implementer.sol)
8. [erc777/contracts/ERC20Token.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/erc777/contracts/ERC20Token.sol)
9. [erc777/contracts/ERC777BaseToken.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/erc777/contracts/ERC777BaseToken.sol)
10. [erc777/contracts/ERC777ERC20BaseToken.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/erc777/contracts/ERC777ERC20BaseToken.sol)
11. [erc777/contracts/ERC777Token.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/erc777/contracts/ERC777Token.sol)
12. [erc777/contracts/ERC777TokensRecipient.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/erc777/contracts/ERC777TokensRecipient.sol)
13. [erc777/contracts/ERC777TokensSender.sol](https://github.com/files-fm/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/erc777/contracts/ERC777TokensSender.sol)

# 3. Findings

In total, **3 issues** were reported including:

- 3 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Known vulnerabilities of ERC-777 token

### Severity: low

### Description

1. It relies on central contract registry which is a security concern as well. A central registry can make developer's life easier but it also acts as a central point of failure exactly as it was with Parity Multisig. All the Parity Multisigs relied on a central code library. It happened that there was a bug in the library and it was exploited. As a result, all the Parity Multisigs crashed. [source](https://www.cointelligence.com/content/comparison-erc20-erc223-new-ethereum-erc777-token-standard/)

2. In addition, ERC777 defines a new set of functions. This is an attempt to allow token developers to make their tokens compatible with both ERC20 and ERC777 standards simultaneously for the sake of adoption. This means that a developer can inherit a bug of ERC20 in ERC777. [source](https://www.cointelligence.com/content/comparison-erc20-erc223-new-ethereum-erc777-token-standard/)

## 3.3. Out of gas.

### Severity: low

### Description

For every call fallback function member of  ERC777TokenScheduledTimelock contract release function is called to check if the beneficiary locked tokens is listed in Timelock array, the gas consumption can be more expensive each time that a new data is appended to the array, until reaching an "Out of Gas" error or a "Block Gas Limit" in the worst scenario.
Knowing that fallback is one of the most called function in a token contract such implementation can make the function usage very expensive, and that there is no implemented function to reduce the array size.

### Code snippet

https://github.com/danbogd/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/ERC777TokenScheduledTimelock.sol#L33

https://github.com/danbogd/FFM_Token_Crowdsale_SmartContract/blob/48889776bcaedc341a6d4fc8a8f01360995ac4a5/contracts/ERC777TokenScheduledTimelock.sol#L40-L64

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/16898eaf849875edf2752d49ae32ac6b

https://gist.github.com/yuriy77k/facf2160c0664fe4eb041fa7dbcad360

https://gist.github.com/yuriy77k/ea1b764a4e67b3754c84cd47a6d74025
