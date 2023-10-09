# 1. Summary

[BitUnits Crowdsale](http://etherhub.io/addr/0xd1c10d433c888e6d1841ff924d0ce45157f0d5cd#tab_addr_3) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

[BitUnits.sol](https://gist.github.com/yuriy77k/d0d28a553000ddc1a64f63b0fb4d4b05)

the same as:
http://etherhub.io/addr/0xd1c10d433c888e6d1841ff924d0ce45157f0d5cd#tab_addr_3

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 2 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Crowdsale Price

### Severity: medium

### Description

Taking into account the decimals and the [rate](https://gist.github.com/yuriy77k/d0d28a553000ddc1a64f63b0fb4d4b05#file-bitunits-sol-L189) set by the developers, if any token value is allocated for the crowdsale the investors will receive for every 1 ETC = 10.000.000 token which is the total supply of UNITS token.

### Recommendation

Developers should set a different rate otherwise the maximum collected fund will be 1 ETC.

### Code snippet

https://gist.github.com/yuriy77k/d0d28a553000ddc1a64f63b0fb4d4b05#file-bitunits-sol-L189

## 3.2. Sale Availability

### Severity: low

### Description

In order for the investors to buy tokens the address 0x0 should be set with a certain amount of tokens to be sold, however 0x0 address is set with zero tokens.

The crowdsale won't work.

### Code snippet

https://gist.github.com/yuriy77k/d0d28a553000ddc1a64f63b0fb4d4b05#file-bitunits-sol-L187#L189

## 3.3. Token Transfer to Address 0x0.

### Severity: low

### Description

Accidentally transferred to zero address tokens may be sold.

## 3.4. Extra checking.

### Severity: minor observation

### Description

Extra checking in 90 and 100 lines. SafeMath library checks it anyway.

### Code snippet

https://gist.github.com/yuriy77k/d0d28a553000ddc1a64f63b0fb4d4b05#file-bitunits-sol-L90

https://gist.github.com/yuriy77k/d0d28a553000ddc1a64f63b0fb4d4b05#file-bitunits-sol-L100

### Recommendation

Those lines may be deleted.

# 4. Conclusion

The contract developers should consider the issues described above. the contract cannot be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f96117ef641b601010ffc9c9b5c2a0b9

https://gist.github.com/yuriy77k/598a97f409c1a890b26571e416ebff7f

https://gist.github.com/yuriy77k/52081bed4c5b8cdf745ec9e3ebd444f2
