# USDC Token Security Audit Report

# 1. Summary

[USDC Token](https://coinmarketcap.com/currencies/usd-coin/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

1. [USDC Proxy contract](https://etherscan.io/token/0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48#code): 0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48
2. [USDC Implementation contract](https://etherscan.io/address/0xa2327a938febf5fec13bacfb16ae10ecbc4cbdcf#code): 0xa2327a938febf5fec13bacfb16ae10ecbc4cbdcf

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **4 notes** were reported, including:

- 0 notes.

- 4 owner privileges.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Upgradeable contract

### Severity: owner privileges

### Description

The USDC contract utilizes an upgradeable design, which allows for the implemented code to be overwritten with new code. While this provides flexibility for future improvements, it also introduces the risk of errors being introduced into the contract during an upgrade.

## 3.3. Blacklist system

### Severity: owner privileges

### Description

The USDC token adheres to a strict set of [terms of use](https://www.circle.com/en/legal/usdc-terms), which includes a blacklist system. An account that is blacklisted cannot send or receive USDC tokens. This mechanism is in place to ensure compliance with regulatory requirements and mitigate risks associated with illicit activities.

## 3.4. Multiple minters

### Severity: owner privileges

### Description

The USDC contract has the capability to support multiple minters. This feature provides greater flexibility in managing the token's supply. However, it also introduces potential risks if one of the minters becomes compromised. It is essential to maintain strict control over the minters to minimize potential security risks.

## 3.5. Pauseable contract

### Severity: owner privileges

### Description

The USDC contract includes a pauseable feature, which allows for the token's operations to be halted in case of an emergency, such as a depegging event. This functionality can help protect user funds and prevent further damage in the event of a serious issue. However, it also centralizes control over the token's operations, which may be a point of concern for some users.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The USDC token is a well-established stablecoin with a robust design. However, it is important for users to be aware of the potential risks associated with its upgradeable, blacklist, multiple minter, and pauseable features. It is highly recommended to closely monitor any upgrades to the contract and stay informed about changes in the blacklist policy to ensure the continued security and stability of the token.

# 6. Revealing audit reports

https://gist.github.com/ESNJS/3cae3219b1fb66b7f025d299d1103ec6
