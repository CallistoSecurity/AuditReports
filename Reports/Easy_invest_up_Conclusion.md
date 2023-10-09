# 1. Summary

[Easy invest up](https://etherscan.io/address/0xCd039b3A5c4afa255757beeC7eED4Da06094C374#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Audited smart contract is one kind of Ponzi scheme, where the first investors make a profit at the expense of subsequent ones. Ponzi scheme ("pyramid") where most investors lose their money.

# 2. In scope

- [easyinvestup.sol](https://gist.github.com/yuriy77k/71e34ff6452f78177a004c024e446b41

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Flow control transferred before state variables updated.

### Severity: minor observation

### Description

While 'address.transfer()' function gas usage is limited to around 2300, a good code practice is to update the state variables before transferring the control flow.

### Code snippet  

https://gist.github.com/yuriy77k/71e34ff6452f78177a004c024e446b41#file-easyinvestup-sol-L200

## 3.2. Ponzi Scheme.

### Severity: low

### Description

- The audited contract represent a pure ponzi scheme, future investors benefits will be paid from newly invested ether.
- The investors are requested to invest an amount higher than 1000000000 wei so they can get 5% promo  (which is kind of paying it from their own investment).
- The contract do not implment any other function to refill the contract with ether, which means that the last users of the pyramid will never receive their ether back.


# 4. Conclusion

The contract is safe for deployment, but there is high risk to lose money invested to Ponzi scheme.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/34a935c352c60980764180e0723b9334

https://gist.github.com/yuriy77k/8494bfb2952077e1a2cc42e3cb683d5d

https://gist.github.com/yuriy77k/aff7d0541ddcd6ac0211fabea844e38d
