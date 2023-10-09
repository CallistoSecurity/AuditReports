# Cryptonomica EthID Tokens v.2 Security Audit Report

# 1. Summary

[Cryptonomica EthID Tokens v.2](https://ropsten.etherscan.io/address/0xd9fbcb890aeafbd37852f614689b0c6d9ff05024#contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> This smart contract implements share revenue (share income) mechanism as follows:
> This contract collects ETH from other contract(s).That is, he receives periodic transfers of funds from other contract(s).
> When a contract is deployed the finite amount of tokens (ERC20/223/677) is created.
> Every token owner at any moment can burn his/her tokens and receive a share/part of ETH contained in this smart contract on the moment tokens were burned that corresponds to share of the burned tokens in the total number of tokens.
> The idea is that in this way the funds in the contract become more and more, and the number of tokens less and less.
> Thus, we lay the mechanism for the constant growth of the price of the token in the market.

# 2. In scope

https://ropsten.etherscan.io/address/0xd9fbcb890aeafbd37852f614689b0c6d9ff05024#contracts

# 3. Findings

In total, **3 issues** were reported including:

- 3 medium severity issues.

## 3.1. ERC-223 Compliance

### Severity: medium

### Description

ERC-223 standard do not implement a transferFrom function.
For example if an address approve tokens to a contract address and the contract address try to call transferFrom to transfer the tokens to itself to be able to execute any logic and knowing that most contract do not implement tokenFallback function the transaction will throw.
ERC223 is intended to fix issues for direct transfer using transfer function only not transferFrom. For more information check [here](https://github.com/ethereum/EIPs/issues/223).

### Code snippet

Line 365.

## 3.2. Double call of `tokenFallback`

### Severity: medium

### Description

The `transfer(address, uint, bytes)` function calls `tokenFallback()` function what also called by `transferFrom()`. Thus, the recipient of the tokens will receive two messages(calls of `tokenFallback()`) with one transfer of funds.
Also in this function there is no checking that `address _to` is contract address.

### Code snippet

Line 388.

### Recommendation

Add fallback call only inside of `transfer` functions.

## 3.3. Support for ERC677 standard not working

### Severity: medium

### Description

The contract promises support for ERC677 standard, but in fact it does not work. The reason is that the `transferAndCall()` (ERC677) function calls `transferFrom()`, which in turn will call `tokenFallback()` function of ERC223. Thus, the recipient in addition to ERC677 must support ERC223 (but this makes no sense and in reality this will not happen), otherwise the transfer will not work.

### Code snippet

* Line 406.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/4f9fd5ee2eb016adceeddb51a589124a

https://gist.github.com/yuriy77k/f938a5505abe0dbd0719848f3bc22e67

https://gist.github.com/yuriy77k/a9512ba571d0c47b84abac7fe4d3882d
