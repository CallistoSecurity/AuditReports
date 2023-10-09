# Enjin Coin Security Audit Report

# 1. Summary

[Enjin Coin](https://enjincoin.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Audit Top 200 CoinMarketCap tokens.

https://enjincoin.io/

# 2. In scope

1. [ENJ.sol](https://ropsten.etherscan.io/address/0xd9fbcb890aeafbd37852f614689b0c6d9ff05024#contracts)

# 3. Findings

In total, **2 issues** were reported including:

- 1 note.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.


## 3.1. ERC20 Compliance — event missing

### Severity: note

### Description

According to ERC20 standard when coins are minted a `Transfer` event should be emitted.

### Code snippet

* Lines 352, 353.
        balanceOf[_crowdFundAddress] = minCrowdsaleAllocation + maxPresaleSupply; 
        balanceOf[_incentivisationFundAddress] = incentivisationAllocation; 

## 3.2. Owner privileges

### Severity: owner privileges

### Description

The owner can allocate unsold tokens after crowdsale to `incentivisationFundAddress` which is managed by the owner. It is common practice to burn unsold tokens.

### Code snippet

* Lines 456.

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/037ef6b053af5c51bb1942699a3995fe

https://gist.github.com/yuriy77k/3588be37d4f56a9b8f4cfc82b486ab3a

https://gist.github.com/yuriy77k/deebe5089b27642706b8fcd4c7e0bc28
