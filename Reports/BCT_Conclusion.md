# 1. Summary

[BCT](http://etherhub.io/addr/0x1be6d61b1103d91f7f86d47e6ca0429259a15ff0#tab_addr_3) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [BCT.sol](https://gist.github.com/yuriy77k/341c72bb372a30a98df9ebd7797ecac1).

# 3. Findings

In total, **1 issues** were reported including:

- 1 low severity issues.

No critical security issues were found.

## 3.1. No zero address checking.

### Severity: low

### Description

There are possibility of accidentally sending tokens to zero address in [`transfer`](https://gist.github.com/yuriy77k/341c72bb372a30a98df9ebd7797ecac1#file-bct-sol-L239-L254) functions.

# 4. Conclusion

No critical vulnerabilities were detected.This contract can be considered safe to be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1732c02ee4d146142a670a4438c65a9d

https://gist.github.com/yuriy77k/5490d082d862bed8021595112786fcde

https://gist.github.com/yuriy77k/4312bfddd72e018a011a0c10023694a2
