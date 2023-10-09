# MChef Security Audit Report v.2

# 1. Summary

[MChef](https://github.com/GoSwapp/liquiditymining/blob/main/contract) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://goswapp.io/

# 2. In scope

Contract commit [c45a40dd4c470547679a8f051234e3a912245b2a](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract)

# 3. Findings

In total, **1 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

- 1 notes.

No critical security issues were found.

## 3.1. High Gas spending

### Severity: note

### Description

The function [massUpdatePools()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1336-L1342) updates every existing pool. It will use many gas if will be many pools. 
This function is calling from others functions: [add()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1213), [set()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1235) (it's possible not update pools from them), [updateRewardPerBlock()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1245), [setRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1260), [updateRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1268), so Gas usage will be high i those functions too.

However, using `massUpdatePools()` in the functions [setRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1260), [updateRewardsValue()](https://github.com/GoSwapp/liquiditymining/blob/c45a40dd4c470547679a8f051234e3a912245b2a/contract#L1268) is not required and may be removed from them.

# 4. Conclusion

The audited smart contract can be deployed. The issues pointed in the [previous report](https://gist.github.com/yuriy77k/60abfdc407c0fa036cab728699aef702) are fixed.

