# Security Audit Report

# 1. Summary

[Zilliqa Token](https://etherscan.io/address/0x05f4a42e251f2d52b8ed15e9fedaacfcef1fad27#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token desription:

	Symbol      : ZIL
	Name        : Zilliqa
	Total supply: To be set in the construction phase
	Decimals    : 12
	Standard    : ERC20

# 2. In scope

- [ZilliqaToken.sol](https://gist.github.com/yuriy77k/cd476ddc9a1c1685dc39e399fcd08b3e).

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

## 3.2. Transfer Admin to zero address.

### Severity: low

### Description

Possibility of setting zero address as `newAdnin` at `changeAdmin` function.

### Code snippet

- [lines 344-348](https://gist.github.com/yuriy77k/cd476ddc9a1c1685dc39e399fcd08b3e#file-zilliqatoken-sol-L344)

### Recommendation

Need to check if newOwner address is not zero address.

```solidity
require( newAdmin != address(0) );

```

## 3.3. Owner Privileges

### Severity: low

### Description

The contract owner allow himself:
- to pause functions of contract (transfer, transferFrom, approve, increaseApproval, decreaseApproval), besides burn function; 
- Evacuate Tokens at any time.

The contract is managed manually by the owner which is not good for investors.

### Code snippet

- [lines 236-245](https://gist.github.com/yuriy77k/cd476ddc9a1c1685dc39e399fcd08b3e#file-zilliqatoken-sol-L236)

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/8a4e69ad1ae46a818982b0dbc5627a1a

https://gist.github.com/yuriy77k/4b4dbfbd3ce5ba84eb0b7364dd608c74

https://gist.github.com/yuriy77k/7facb589523c03d64f09be1ba8b11614
