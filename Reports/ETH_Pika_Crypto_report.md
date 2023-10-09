# Pika Crypto Security Audit Report

# 1. Summary

[Pika Crypto](https://pikacrypto.com/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The Pika Token has primitive tokenomics designed to sustain organic growth with a few unique characteristics.

    Website: https://pikacrypto.com/
    Coin Market Cap: https://coinmarketcap.com/currencies/pika/
    Coin Gecko: http://coingecko.com/en/coins/pika
    Telegram: https://t.me/Pikatokenofficial
    Discord: https://discord.gg/VZRGnTAZWt
    Twitter: https://twitter.com/pika_token?lang=en
    Reddit: https://www.reddit.com/r/PIKAOFFICIAL/


# 2. In scope

https://etherscan.io/address/0x075fbc0a804de702bfda4d8e29834c763ffef1c3#code


# 3. Findings

In total, **3 issues** were reported including:

- 0 high severity issues.

- 0 medium severity issues.

- 2 low severity issues.

- 0 notes.

- 1 owner privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).


## 3.2. ERC20 compliant - no Transfer event on initial token creation

### Severity: low

### Description

Following [ERC20 standard](https://eips.ethereum.org/EIPS/eip-20):
>
> A token contract which creates new tokens SHOULD trigger a Transfer event with the _from address set to 0x0 when tokens are created.

But in the `constructor` (lines 539-541) where 50 trillion tokens are created and assigned to `owner` the `Transfer` event was not emitted.


## 3.3. The owner can exchange old Pika tokens to current Pika unlimited times

### Severity: owner privileges

### Description

Using function `extractOldPIKA()` (lines 486-489) `owner` can withdraw already exchanged old Pika tokens and then, using function `exchnagePika()` (lines 454-484) exchange it again to new Pika tokens. It can be repeated unlimited times until drain Pika tokens reserve.


# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

Users have to pay attention to assigning the entire TotalSupply (50 trillion tokens) to the owner account and the owner can withdraw old and new Pika tokens from the contract address (reserve).
