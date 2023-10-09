# Shield Warriors token Security Audit Report

# 1. Summary

[Shield Warriors token](https://github.com/shield2protocol/smart-contract/blob/main/BinanceSmartChain_WARRIOR.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Website: https://www.shieldprotocol.org
- Twitter: https://twitter.com/shield2protocol
- Telegram: https://t.me/shield2protocol
- Medium: https://shield2protocol.medium.com

# 2. In scope

Commit `d6cc465ce307d77c6c08502a2bb378d666519230`

- [BinanceSmartChain_WARRIOR.sol](https://github.com/shield2protocol/smart-contract/blob/d6cc465ce307d77c6c08502a2bb378d666519230/BinanceSmartChain_WARRIOR.sol)

# 3. Findings

In total, **1 note** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

- 1 notes.

- 0 owner privileges.

No critical security issues were found.

## 3.1. Best practice for ERC-20 and BEP-20 tokens

### Severity: note

### Description

ERC20 (BEP20) is a widely used standard across the Ethereum ecosystem. It is reasonable to assume that ERC20 tokens could be "accidentally" deposited to this contract even though it is not intentional.

Every user on the entire Ethereum ecosystem can send ERC20 tokens to this contract and he will have no ability to extract it back unless there is a special "ERC20-rescue" function implemented in your contract. It is advised to implement this function.

*Example: here is [BAT contract address](https://etherscan.io/address/0x0d8775f648430679a709e98d2b0cb6250d2887ef). As you can see the contract itself holds $497,000 worth of different ERC20 tokens - all these tokens are permanently "stuck" inside the contract and therefore uselessly lost.*

### Recommendation

A simple "ERC20-rescue" function can solve the problem.

```js
function rescueERC20(address _token, uint256 _amount) external onlyOwner {
    IBEP20(_token).transfer(owner(), _amount);
  }
```

# 4. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.


