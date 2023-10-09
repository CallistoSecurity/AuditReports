# 1. Summary

[Tubiex](https://github.com/tubiex/smart-contracts/tree/f162c021c0d3d4c72c2144c80968d5190086300a/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

_ERC-20 implementation, Presale token management (creating records of presales and allowing users to claim those records at a rate of 1% per day), Crowdsale contract - EOS model with 2 additions: 1) a hidden hard cap, and 2) dynamic daily reserve price (with the ability to rebase the ETH price daily to have an accurate reserve._

https://tubiex.com/

# 2. In scope

Commit hash: f162c021c0d3d4c72c2144c80968d5190086300a.

- [Migrations.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/Migrations.sol). 
- [IPresale.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/presale/IPresale.sol). 
- [Presale.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/presale/Presale.sol). 
- [Math.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/math/Math.sol). 
- [SafeMath.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/math/SafeMath.sol). 
- [ICrowdsale.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/crowdsale/ICrowdsale.sol). 
- [TBNCrowdsale.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/crowdsale/TBNCrowdsale.sol). 
- [Bounty.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/bounty/Bounty.sol).
- [IBounty.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/bounty/IBounty.sol). 
- [Airdrop.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/airdrop/Airdrop.sol). 
- [IAirdrop.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/airdrop/IAirdrop.sol). 
- [Roles.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/access/Roles.sol). 
- [FundkeeperRole.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/access/roles/FundkeeperRole.sol). 
- [ManagerRole.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/access/roles/ManagerRole.sol). 
- [MinterRole.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/access/roles/MinterRole.sol). 
- [RecoverRole.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/access/roles/RecoverRole.sol).
- [WhitelisterRole.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/access/roles/WhitelisterRole.sol). 
- [ERC20.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/ERC20/ERC20.sol). 
- [ERC20Detailed.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/ERC20/ERC20Detailed.sol). 
- [IERC20.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/ERC20/IERC20.sol). 
- [TBNERC20.sol](https://github.com/tubiex/smart-contracts/blob/master/contracts/ERC20/TBNERC20.sol). 

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. The owner can mint any value of tokens he wants.

### Severity: medium

### Description

The owner (Minter) or a hacker (if the Minter's private key will be compromised) can mint any value of tokens he wants. The token can become worthless very quickly. This is dangerous for investors.

### Code snippet

https://github.com/tubiex/smart-contracts/blob/4d955765e0041d54176b8a337481bd7f76720167/contracts/ERC20/TBNERC20.sol#L45-L55

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```
## 3.3. Deprecated method.

### Severity: minor observation

### Description

The `function () payable { revert(); }` was a pattern used to prevent implicit acceptance of ether in Solidity versions older than 0.4.0, but today this is unneeded.

### Code snippet

https://github.com/tubiex/smart-contracts/blob/4d955765e0041d54176b8a337481bd7f76720167/contracts/ERC20/TBNERC20.sol#L28

https://github.com/tubiex/smart-contracts/blob/4d955765e0041d54176b8a337481bd7f76720167/contracts/bounty/Bounty.sol#L84

https://github.com/tubiex/smart-contracts/blob/4d955765e0041d54176b8a337481bd7f76720167/contracts/airdrop/Airdrop.sol#L84

https://github.com/tubiex/smart-contracts/blob/4d955765e0041d54176b8a337481bd7f76720167/contracts/presale/Presale.sol#L84

# 4. Conclusion

The review did not show any critical issues, but right of owner to mint any amount of token at any time is dangerous for investors.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/6ac37ccca58ad78ce81a717bfbdc002a

https://gist.github.com/yuriy77k/ec5f39e05fdbe62a345da9f435d9f51f

https://gist.github.com/yuriy77k/eaf23b4651e55a02e4298c7c7adb6e64
