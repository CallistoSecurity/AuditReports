# DeFiFarms protocol Security Audit Report

# 1. Summary

[DeFiFarms protocol](https://bscscan.com/address/0x08d1Ed0e3816183e703a492dDD28d68fcc13bb61#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

>
> DeFiFarms protocol is the first automatic liquidity acquisition yield farm and AMM decentralized exchange running on Binance Smart Chain with lots of unique and creative features that let you earn and win.

- Website: https://defifarms.org/
- Twitter: https://twitter.com/DeFiFarmsNFTs
- Platform: https://app.defifarms.org/

# 2. In scope

Upgradable proxy contract: 
https://bscscan.com/address/0x08d1Ed0e3816183e703a492dDD28d68fcc13bb61#code

Implementation contract: 
https://bscscan.com/address/0xd023618fa3d91f7862d277d59f2e8ad560df01fc#code

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **11 notes** were reported, including:

- 2 notes.

- 9 owner privileges.

No critical security issues were found.

## 3.1. Unused `require`

### Severity: note

### Description

In the function `_transfer()` in the `DefiFarmToken.sol` there are two requires conditions which couldn't be `true`, because it already checks in the `SafeMath` library:
1. https://bscscan.com/address/0xd023618fa3d91f7862d277d59f2e8ad560df01fc#code#F1#L129
2. https://bscscan.com/address/0xd023618fa3d91f7862d277d59f2e8ad560df01fc#code#F1#L133


## 3.2. There is no function to rescue BNB from the contract address

### Severity: note

### Description

Since the contract can accept BNB payment, somebody can transfer BNB to its address by mistake. 
A good security practice is to allow the owner to rescue BNB from the contract. It will not hurt the users because the contract should not hold BNB.



## 3.3. Owner privileges.

### Severity: owner privileges

### Description

The contract owner can:
1. Mint any amount of tokens to any address;
2. Upgrade contract code. A new contract may be non-audited and has functions dangerous for users.


## 3.4. Operator privileges

### Severity: owner privileges

### Description

The contract's operator has the right to:
1. Update transfer tax rate;
2. Update burn rate;
3. Update max transfer amount rate;
4. Update min amount to liquify;
5. Set/remove excluded addresses from AntiWhale list;
6. Enable/disable the `Swap And Liquify` function;
7. Update the swap router that is using in the `Swap And Liquify` function.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit. Users have to pay attention to the owner's right to upgrade the contract on another which was not audited and may contain dangerous functionality.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
