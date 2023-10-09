# Tronempire Security Audit Report

# 1. Summary

[Tronempire](https://troneye.com/reveal?address=TGik1iQSWYR1zGVsMgeBY95oUoPmNQjwr5) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> This is a game on a smart contract. The user for the in-game currency tickets buys several types of transport and receives a profit for it.

https://tronempire.net

# 2. In scope

1. [Tronempire.sol](https://troneye.com/reveal?address=TGik1iQSWYR1zGVsMgeBY95oUoPmNQjwr5)

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issue.

- 1 note.

- 1 minor observation.

No critical security issues were found.

## 3.1. Possible loss of a large number of transport when joining

### Severity: medium

### Code snippet

[function join(), line 409](https://troneye.com/reveal?address=TGik1iQSWYR1zGVsMgeBY95oUoPmNQjwr5)

### Description

When the transport is joined, the values are summed. `tto.count` and `tfrom.count` are `uint16` with a maximum value of 65535. But when summed, they can exceed this value. There is a check at line 403:
```Solidity
require(tto.count + tfrom.count <= 65535, "Too large count");
```
But it can also overflow.

Example:
```Solidity
// tto.count += tfrom.count;
tto.count = 30000 + 35550; // this equals 14
```

It is real case because 65535 Steam Engines (transport type #2) cost about $ 180,000. Or if someone today buys Steam Engines for $ 25,000, he will receive such a quantity of transport in about a year without additional investments.

### Recommendation

Use condition `require(tto.count + tfrom.count > tto.count, "Too large count");`

## 3.2. Overflow is theoretically possible

### Severity: note

### Code snippet

[function buy(), line 363](https://troneye.com/reveal?address=TGik1iQSWYR1zGVsMgeBY95oUoPmNQjwr5)

### Description

When calculating the cost may overflow if some parameters(like `tt.price` or maximum value of `_count`) in the code will be updated.

### Recommendation

This should be taken into account, for example, when updating the transport parameters.

## 3.3. The removed transport remains in storage

### Severity: minor observation

### Code snippet

[function removeWithLength(), line 447](https://troneye.com/reveal?address=TGik1iQSWYR1zGVsMgeBY95oUoPmNQjwr5)

### Description

In fact, when using `remove()` or `removeWithLength()` functions the elements of the transport array is not deleted from the storage. Just changing the variable `length` of `m.units` structure. This means that if we directly call to the element `m.units.items[i]` with the index of the removed item, we will be able to get the removed transport data, or change them.

In the code of the contract, there was not found the possibility of direct access by index, but it's better to clear data from storage.

# 4. Conclusion

There are some vulnerabilities were found that should be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/4a6836cb71f924056a9b4727e02d78d8

https://gist.github.com/yuriy77k/c47b96bef121308cc905727a6cd6fded

https://gist.github.com/yuriy77k/ed9195212c45d480fbed8d66f2c1f72c
