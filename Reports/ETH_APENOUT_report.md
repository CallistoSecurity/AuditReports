# APENOUT Security Audit Report

# 1. Summary

[APENOUT](https://github.com/SNOSDEV/APENOUT/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Users pay 10% fee on buying token from AMM and 30% fee on selling token to AMM.

# 2. In scope

Commit [7d8adf0cd217767ea5695dc5fddca03865472f99](https://github.com/SNOSDEV/APENOUT/blob/7d8adf0cd217767ea5695dc5fddca03865472f99/contracts/)

- APENOUT.sol
- Context.sol
- DividendPayingToken.sol
- DividendPayingTokenInterface.sol
- DividendPayingTokenOptionalInterface.sol
- ERC20.sol
- Greeter.sol
- IERC20.sol
- IERC20Metadata.sol
- IterableMapping.sol
- IUniswapV2Factory.sol
- IUniswapV2Pair.sol
- IUniswapV2Router.sol
- Ownable.sol
- SafeMath.sol
- SafeMathInt.sol
- SafeMathUint.sol


# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **6 notes** were reported, including:

- 0 notes.

- 6 owner privileges.


## 3.1. Owner privileges

### Severity: owner privileges

### Description

The owner of the contract has right to:

1. Change trading start time, if trading was not started yet.
2. Update dividend tracking contract.
3. Update Uniswap router address.
4. Exclude addresses from fees.
5. Allow pre-trade to some addresses.
6. Set AMM pool addresses.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.


# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
