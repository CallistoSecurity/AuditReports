# 1. Summary

[Auditchain Token (v2)](https://github.com/Vestcomp/TGE) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

https://auditchain.com/

# 2. In scope

Commit hash: `1a6ccfb06b540d69101a82fef46b9170aa112f2d`.

1. [Locked.sol](https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/Locked.sol)
2. [MigrationAgent.sol](https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/MigrationAgent.sol)
3. [Migrations.sol](https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/Migrations.sol)
4. [Token.sol](https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/Token.sol)

# 3. Findings

In total, **2 issues** were reported including:

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Owners privileges

### Severity: low

### Description

The owners privileges:

* Ability to be migrated to new contract in the future (new contract may be not audited)

* Ability to lock and unlock ERC20 functions of Token based on user address

### Code snippet

https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/Token.sol#L79

https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/Token.sol#L104

## 3.2. Date related issues

### Severity: minor observation

### Description

It is about

> Issuing yearly 12,500,000 new tokens to governance contract

1. In this contract, the year is determined by dividing the number of seconds since the beginning of the Unix Epoch by the number of seconds in a leap year. In this case `06.02.2019 11:59pm` is 2018 year by `returnYear()` function. And `07.02.2019 12:00am` is 2019 year.

2. If the contract is deployed on February 6 the owner will receive 12'500'000 tokens. And the next day he can call `mint()` and get another 12'500'000 tokens because the contract will consider that the next year has come.

### Code snippet

https://github.com/Vestcomp/TGE/blob/1a6ccfb06b540d69101a82fef46b9170aa112f2d/contracts/Token.sol#L52

### Recommendation

Just save the timestamp of first payment and add to it year value in seconds to figure out if next payment date is come. And then update the date of the last payment.

# 4. Conclusion

The review of Auditchain Token smart contract did not show any critical issues and is safe for deploy. We pointed out some observations about the owner's rights and calculation of dates.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c73a1cdd30f45bd418325b1d08a3c79a

https://gist.github.com/yuriy77k/1cddf1ecdcde98f3a0bb5ba04d9053b2