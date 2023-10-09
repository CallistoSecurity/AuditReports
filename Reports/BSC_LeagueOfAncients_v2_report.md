# League of Ancients v.2. Security Audit Report

# 1. Summary

[League of Ancients](https://gitlab.com/jingyi2811/luo/-/tree/main/original-contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Smart Contract info : https://docs.google.com/document/d/1Obi4nCMnSF3Ji3aBWW-r0k0ZTysZYe2mFMwPuX3kMBM/edit
- Token Unlock Schedule : https://docs.google.com/spreadsheets/d/1S25D8yl6l0J9ZHHpFxeYfmuTFv6iUQuwqPec27Q7JWk/edit#gid=93878185
- Website : https://leagueofancients.com
- Twitter : @loaofficialmoba

# 2. In scope

Commit [ef025a4498362ccd65af62ff81a2f38a2b2abd1b](https://gitlab.com/jingyi2811/luo/-/tree/ef025a4498362ccd65af62ff81a2f38a2b2abd1b/contracts)

## 2.1 Excluded

The schedule correctness of tokens distribution was excluded from audit.

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **5 notes** were reported, including:

- 0 notes.

- 5 owner privileges.

No critical security issues were found.


## 3.1. Admin privileges

### Severity: owner privileges

### Description

`LoaToken` contract Admin can:

1. Pause/unpause token transfer.
2. Pause/unpause token transfer for selected address.

`LoaPresale` contracts Admin can:

3. Add/remove addresses to/from whitelist.
4. Withdraw deposited BNB.
5. Finish `LoaPresale` earlier.

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
