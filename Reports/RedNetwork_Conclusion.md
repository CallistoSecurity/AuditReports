# [RedNetwork Token](https://etherscan.io/address/0x7ca654da4501fcbcdf4941eced804f34b2e533dd#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited ERC20 compliant token contract can be considered safe to be deployed.

# 2. Low severity issues:

## 2.1. Known Issues of ERC20 Standard

### Description

ERC20 compliant Tokens have some well-known issues (listed bellow), This is just a reminder for the contract developers.

- Approve + transferFrom mechanism allows double Withdrawal attack. 
- Lack of transaction handling.

The above mentioned issues are well documented, a basic search can help to get more information.


# Revealing audit reports:

https://gist.github.com/yuriy77k/2344e54c767f308dd1043392a248aeb5

https://gist.github.com/yuriy77k/2ad6bbb8b76b9e781a91ed82e1ab6177

https://gist.github.com/yuriy77k/8a5fe62729e63dd952270856161b3fbf

