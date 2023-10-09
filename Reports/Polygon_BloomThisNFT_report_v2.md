# BloomThis NFT (V2) Security Audit Report

# 1. Summary

[BloomThis NFT](https://github.com/Realmstack/BloomThisSmartContracts/tree/main/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `717807a5e20928a007f76cbac9046c14c0fa647a`

[BloomThis.sol](https://github.com/Realmstack/BloomThisSmartContracts/blob/717807a5e20928a007f76cbac9046c14c0fa647a/contracts/BloomThis.sol)

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **6 notes** were reported, including:

- 3 notes.

- 3 owner privileges.




## 3.1. Owner Privileges

### Severity: owner privileges

### Description

1. New NFT tokens can only be minted by an authorized admin using the function `mint()`.

2. Token fusion rules can only be added by an authorized admin using the function `addFusionRule()`.

3. The royalty fee collected by admins can be changed by the admin using the function `setRoyaltyInfo()` for the rewards collected impacting the reward per token for the users. 

## 3.2. Infinite minting of tokens possible

### Severity: note

### Description

If `_maxTokens` is initialized as zero, it would allow the admin to mint unlimited tokens.

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/717807a5e20928a007f76cbac9046c14c0fa647a/contracts/BloomThis.sol#L105


## 3.3. Follow good coding practice

### Severity: note

### Description

1. Missing docstrings. 

Many functions in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention,
which is fundamental to correctly assess not only security, but also correctness. Additionally, docstrings improve
readability and ease maintenance. They should explicitly explain the purpose or intention of the functions,
the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API.
Functions implementing sensitive functionality, even if not public, should be clearly documented as well.
When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

2. Unspecific compiler version pragma

Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler, which may have higher risks of undiscovered bugs. Contracts may also be deployed by others, and the pragma indicates the compiler version intended by the original authors.


## 3.4. Transfer function dependent on gas costs

### Severity: note

### Description

Gas refers to the unit that measures the amount of computational effort required to execute specific operations on the Ethereum network. Since each Ethereum transaction requires computational resources to execute, each transaction requires a fee. Gas refers to the fee required to conduct a transaction on Ethereum successfully. Gas fees are paid in Ethereum's native currency, ether (ETH). Gas prices are denoted in gwei, which itself is a denomination of ETH - each gwei is equal to 0.000000001 ETH (10-9 ETH).

Each opcode supported by the EVM has an associated gas cost. For example, SLOAD, which reads a word from storage, currently costs 200 gas. The gas costs aren’t arbitrary. They’re meant to reflect the underlying resources consumed by each operation on the nodes that make up Ethereum. Any smart contract that uses transfer() or send() is taking a hard dependency on gas costs by forwarding a fixed amount of gas: 2300.

In case when associated gas costs increase the function would fail to leave admin/users unable to withdraw or claim royalty from the contract leading to a denial of service (DoS).

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/717807a5e20928a007f76cbac9046c14c0fa647a/contracts/BloomThis.sol#L249
- https://github.com/Realmstack/BloomThisSmartContracts/blob/717807a5e20928a007f76cbac9046c14c0fa647a/contracts/BloomThis.sol#L277

### Recommendation

it is recommended to stop using the transfer() and send() in your code and switch to using call() instead. And follow the following protective measures to prevent re-entrancy attacks.

- Make sure all internal state changes are performed before the call is executed. This is known as the [Checks-Effects-Interactions pattern](https://solidity.readthedocs.io/en/latest/security-considerations.html#use-the-checks-effects-interactions-pattern)
- Use a reentrancy lock (ie. [OpenZeppelin's ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol).

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


# 6. Revealing audit reports

Previous audit report: https://gist.github.com/yuriy77k/77fa9584d2e96c0b75236a89c2ad10ce
