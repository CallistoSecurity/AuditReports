# 1. Summary

[Metadollar DOLF](https://etherscan.io/address/0x65731AC534BC0D3fdF3F4BDd2B09cF05044920Bc#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

[Metadollar_DOLF](https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502)

# 3. Findings

In total, **3 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Wrong fees calculation.

### Severity: medium 

### Description

In the contracts documentation stated that "Buy and sell fee is increased to 50%", but the division by the "dividendFee_ = 50;" will actually result in 2% share.

### Code snippet

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L88

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L235

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L279

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L444

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L463

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L477

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L494

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L509

## 3.2. Known Issues of ERC20 Standard

### Severity: low

### Description

ERC20 Tokens have some well-known issues.  This is just a reminder for the contract developers.

Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.

## 3.3. Buy function always returns 0.

### Severity: minor observation

### Description

The buy function doesn't have a return statement and always returns 0.

### Code snippet

https://gist.github.com/yuriy77k/f3b1bd615f8c5633cb9399f8ae854502#file-metadollar_dolf-sol-L138-L144

# 4. Conclusion

The contract contains some issues that should be addressed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/1d4b81050831c26957018b473e7ef124

https://gist.github.com/yuriy77k/d06fe8cceb070925e00dae46b664524c

https://gist.github.com/yuriy77k/943a709adde4f1387ac8ed2df6cdc5d8
