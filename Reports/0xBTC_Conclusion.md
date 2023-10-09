# 1. Summary

[0xBTC](https://github.com/0xbitcoin/0xbitcoin-token/blob/master/contracts/_0xBitcoinToken.sol) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

0xBTC is the first Pure Mined PoW Token for the Ethereum Mainnet. It was developed by Infernal_Toast in February 2018 and readapted by the 'Bitcoin Classic Token' team at a later time to incorporate into Ethereum Classic.

Please note that while there is an owner, the owner can only withdraw tokens that have been explicitly transferred to the contract address. This is to recover funds accidentally sent into the contract and the contract owner can be burned using a standard ownership burn contract.

# 2. In scope

- [_0xBitcoinToken.sol](https://github.com/0xbitcoin/0xbitcoin-token/blob/master/contracts/_0xBitcoinToken.sol) github commit hash 9f60a4a0117d10dbe39d44895ed01c2bd2d2c13c.

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

No critical security issues were found.

## 3.1. Transfer to Address 0x0 and Burn Mechanism

### Severity: low

### Description

Transfer to address zero in the audited contract is used as basic burn mechanism (check `totalSupply`, `balances[address(0)]` is deducted from the total supply), this mechanism open the doors also for sending tokens by mistake to address 0x0.

### Code snippet

https://github.com/RideSolo/0xbitcoin-token/blob/master/contracts/_0xBitcoinToken.sol#L488

https://github.com/RideSolo/0xbitcoin-token/blob/master/contracts/_0xBitcoinToken.sol#L520

https://github.com/RideSolo/0xbitcoin-token/blob/master/contracts/_0xBitcoinToken.sol#L580

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

# 4. Conclusion

The contract logic has been audited, the results confirm that the mining process of the token is safe and coherent with the white paper.
The audited contract is safe to be deployed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/bbe004836e44b56b7663c8e3350c5d86

https://gist.github.com/yuriy77k/8cacfd5523627493fd74f308d909d43b

https://gist.github.com/yuriy77k/b8380047f1f6d4da86d121c2d854537d
