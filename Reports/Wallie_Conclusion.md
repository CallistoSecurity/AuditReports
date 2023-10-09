# 1. Summary

[Wallie](https://etherscan.io/address/0xC0B52b76055C392D67392622AE7737cdb6D42133#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The audited contract based on the Ponzi scheme. New players will send the ether that will pay the profits and affiliate commissions of other users.

# 2. In scope

[wallie.sol](https://gist.github.com/yuriy77k/ed24d1dcdfceb7a41b0690667157ecbf)

# 3. Findings

In total, **1 issues** were reported including:

- 1 low severity issues.

No critical security issues were found.

## 3.1. Wrong stats

### Severity: low

### Description

Wrong calculating of stats [118-137](https://gist.github.com/yuriy77k/ed24d1dcdfceb7a41b0690667157ecbf#file-wallie-sol-L118-L137). For example: [`all_cash_back_payments`](https://gist.github.com/yuriy77k/ed24d1dcdfceb7a41b0690667157ecbf#file-wallie-sol-L136) may be increased till of triple value of current [`amount`](https://gist.github.com/yuriy77k/ed24d1dcdfceb7a41b0690667157ecbf#file-wallie-sol-L112).

# 4. Conclusion

Investing into financial schemes can represent a significant high risk when a Ponzi scheme is identified. No critical vulnerabilities were detected.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/853848e95b1c6b116383d0dab29209b9

https://gist.github.com/yuriy77k/ac941958ed0c48e91f6f5351daae8a5c

https://gist.github.com/yuriy77k/7453342a36ac13dadae32381aea0db9c
