# Humanity Security Audit Report

# 1. Summary

[Humanity](https://github.com/marbleprotocol/humanity) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

https://www.humanitydao.org/

# 2. In scope

Commit hash 9e66de601937cc1aae40ee95e52f2f44343fae24.

- [IGovernance.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/interface/IGovernance.sol).
- [IHumanity.sol.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/interface/IHumanity.sol).
- [IRegistry.sol.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/interface/IRegistry.sol).
- [IUniswapExchange.sol.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/interface/IUniswapExchange.sol).
- [MockERC20.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/mock/MockERC20.sol).
- [MockHumanityGovernance.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/mock/MockHumanityGovernance.sol).
- [MockHumanityRegistry.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/mock/MockHumanityRegistry.sol).
- [MockTarget.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/mock/MockTarget.sol).
- [MockUniswap.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/mock/MockUniswap.sol).
- [Faucet.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/Faucet.sol).
- [Governance.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/Governance.sol).
- [Humanity.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/Humanity.sol).
- [HumanityApplicant.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/HumanityApplicant.sol).
- [HumanityGovernance.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/HumanityGovernance.sol).
- [HumanityRegistry.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/HumanityRegistry.sol).
- [PayableHumanityApplicant.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/PayableHumanityApplicant.sol).
- [TwitterHumanityApplicant.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/TwitterHumanityApplicant.sol).
- [UniversalBasicIncome.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/UniversalBasicIncome.sol).
- [Void.sol](https://github.com/marbleprotocol/humanity/blob/master/contracts/Void.sol).

# 3. Findings

In total, **6 issues** were reported including:

- 1 medium severity issues.

- 4 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. Fallback

### Severity: medium

### Description

The fallback function defined in `PayableHumanityApplicant` for the contract to receive ether is not safe for users, any ether sent through the fallback function will be taken by the next user or attacker that calls `applyWithEtherFor` since uniswap function `ethToTokenSwapOutput` uses only `msg.value` and not the contract balance to make the external call. (please note that any remaining ether in the contract is sent back to the `msg.sender` including the eth received through the fallback function).

### Code snippet

https://github.com/marbleprotocol/humanity/blob/master/contracts/PayableHumanityApplicant.sol#L28#L41

## 3.2. Contract accept payment from anyone.

### Severity: low

### Description

An anybody, who send Ether to contract address may lose it because of no payment processing in contract code.

### Code snippet
https://github.com/marbleprotocol/humanity/blob/9e66de601937cc1aae40ee95e52f2f44343fae24/contracts/PayableHumanityApplicant.sol#L26

## 3.3. Proposal fee deposit

### Severity: low

### Description

To apply for a new proposal using `applyFor` function member of `HumanityApplicant` contract, a token fee should be transferred to the contract prior to the function call.

If the required fee to open a proposal is higher than the balance of contract the tokens are taken from the `msg.sender` wallet using `transferFrom` (assuming that the user preapproved the tokens transfer). Since the function `applyFor` uses the balance of the contract first then any one can exploit this logic by checking for direct proposal fees deposit using transfer to the contract and setting  a front running attack to pass his proposal without paying the fees. 

### Code snippet

https://github.com/marbleprotocol/humanity/blob/master/contracts/HumanityApplicant.sol#L29#L37

### Recommendation

- `transferFrom` should be used in both cases, meaning that no condition must be checked to allow the `transferFrom`.
- Add a function to withdraw any possible token that is deposited to the contract to send it back to the users.


## 3.4. Humanity checking

### Severity: low

### Description

Function [`setResult`](https://github.com/marbleprotocol/humanity/blob/master/contracts/mock/MockHumanityRegistry.sol#L13) could be called by anyone and anytime result could be changed to false.

## 3.5. Zero address checking

### Severity: low

### Description

There is no zero address checking in function [`applyWithEtherFor`](https://github.com/marbleprotocol/humanity/blob/master/contracts/PayableHumanityApplicant.sol#L28)

### Recommendation

Add zero address checking
```solidity
require(who != address(0));
```

## 3.6. Experimental Features

### Severity: notes

### Description

As raised by the compiler "Experimental features are turned on. Do not use experimental features on live deployments" the audited code uses ABIEncoderV2 that is in experimental phase and should not be deployed in a live network.

### Code snippet

pragma experimental ABIEncoderV2;

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/ad6c3f3af65d01757eea28354da70d39

https://gist.github.com/yuriy77k/e243dbb606ae384b2f668c7fc4875e75

https://gist.github.com/danbogd/f7462aa1d2073a4fa3dc560df09d201c
