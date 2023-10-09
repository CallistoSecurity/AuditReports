# Bills of exchange factory Security Audit Report

# 1. Summary

[Bills of exchange factory](https://ropsten.etherscan.io/address/0x74eB4DBD3124D41B6775701FD1821571EAd5cf9A#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> 'Bills Of Exchange Factory' is a smart contracts based service that allows user to draw electronic bills or exchange.

https://cryptonomica.net/bills-of-exchange/

# 2. In scope

1. [BillsOfExchangeFactory.sol](https://ropsten.etherscan.io/address/0x74eB4DBD3124D41B6775701FD1821571EAd5cf9A#code)

# 3. Findings

In total, **9 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

- 1 notes.

- 4 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.

## 3.1. Token Uses No Decimals

### Severity: note

### Description

While the specification defined the number of token decimals to be 18, no decimals were found to be used. This can cause problems when interacting with other smart contracts as tokens with 0 decimals can cause rounding errors. For example, many exchanges charge a small fee based on the tokens exchanged. As such, using no decimals will either make it impossible to list the token on these exchanges or it will result in having expensive fees compared to other tokens.

### Code snippet

Line 153.

## 3.2. ERC223 Compliance

### Severity: medium

### Description

The reviewed token contract is not ERC223 fully compliant.

1. The function transfer(address _to, uint _value, bytes _data) call tokenFallback external function on the receiver contract without adding the value to balances[_to]. The original implementation adds the token value to the balance before making the external call [here](https://github.com/Dexaran/ERC223-token-standard/blob/master/token/ERC223/ERC223_token.sol#L63#L68)

2. ERC223 does not implement an approve/transferFrom mechanism.


## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

## 3.4. No checking for zero address

### Severity: low

### Description

In this functions there are no checking for zero address.
-  `initToken` at line 187,
- `changeCryptonomicaVerificationContractAddress` at line 430, 
- `signDisputeResolutionAgreementFor` at line 737, 
- `initBillsOfExchange` at line 786, 
- `setLegal` at line 851, 
- `createBillsOfExchange` at line 981.

## 3.5. Missing event call.

### Severity: low

### Description

According to ERC20 standard, when initializing a token contract if any token value is set to any given address a transfer event should be emitted.
An event isn't emitted when assigning the initial supply to the msg.sender.

### Code snippet

Line 200.

## 3.6. Owner Privileges

### Severity: owner previliges

### Description
Contract owner allow himself to:

1. Owner can upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure. (line 430)

2. fix or not fix withdraw address depends from owner.(line 541) 

3. change price (line 614)

4. Any admin can remove the contract creator from admin list. (line 487) Together with the ability to change the withdrawal address by admin, this can be quite dangerous.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/26e2ee2e96ecb93091c5efbc2bddc7a4

https://gist.github.com/yuriy77k/adbf6e55c290b1382bf9c9dfea2c9ad2

https://gist.github.com/yuriy77k/135a6003cd890ee02a3edd90e566388a

https://gist.github.com/yuriy77k/fa0bcf86093ada4e7ba06e9bedb1bd81
