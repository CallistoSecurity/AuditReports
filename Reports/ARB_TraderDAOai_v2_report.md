# TraderDAOai v2 Contracts Security Audit Report

# 1. Summary

[TraderDAOai](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- Webpage: https://traderdao.ai/
- Discord: https://discord.gg/traderdao
- Youtube: https://www.youtube.com/@TraderDAO_ai
- Twitter: https://twitter.com/traderdao_ai
- Telegram: https://t.me/traderdaoai
- Medium: https://medium.com/@traderdao-ai
- Litepaper: https://traderdao.gitbook.io/traderdao/traderdao/litepaper-english

- Chain to deploy: Arbitrum (ARB)

Audited contracts don't implement the functionality described in the [Litepaper](https://traderdao.gitbook.io/traderdao/traderdao/litepaper-english); therefore, it uses centralized server-side for all `TraderDAO` logic.

# 2. In scope

Commit: 41f87758492e7af5f3f51b6d68b48069bd70b74e

- [Ambassador_Redeem_Contract.sol](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Ambassador_Redeem_Contract.sol)
- [Liquidity_Wallet.sol](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Liquidity_Wallet.sol)
- [POT_Token.sol](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/POT_Token.sol)
- [Proof_Of_Trade_Arbi_One.sol](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Proof_Of_Trade_Arbi_One.sol)


# 3. Findings

In total, **1 issue** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **5 notes** were reported, including:

- 4 notes.

- 16 owner privileges.


## 3.1. Owner privileges of [Ambassador_Redeem_Contract](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Ambassador_Redeem_Contract.sol)

### Severity: owner privileges

### Description

1. Contract `Ambassador_Redeem_Contract` contract inherits basic access control properties from Openzeppelin's [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) contract, where the contract's ownership can be transferred or renounced. Renouncing ownership will leave the contract without an owner, thereby disabling any functionality that is only available to the owner.
2. Function `SetPause()` allows the owner to pause or resume the contracts functionalities available to the users. Users would be unable to redeem USDT tokes for the signature signed by the `signerAddress` if the contract is paused.
3. Function `SetSigner()` allows the owner to update the `signerAddress`. All previous un-redeemed signatures will be invalid if the `signerAddress` is updated. New signatures must be generated for previous unclaimed signatures.
4. Function `Save()` allows owner to withdraw ERC-20 tokens from the contract.

## 3.2. Owner privileges of [Liquidity_Wallet](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Liquidity_Wallet.sol)

### 3.2.1. Functions SetDecimal() and SetRate() allow gov address to modify POT<>USDT conversion rate

#### Severity: owner privileges

#### Description

The functions `SetDecimal()` and `SetRate()` allow the `gov` address to modify POT<>USDT conversion parameters. With current values of `Rate = 100` and `Decimals = 10**18`, the 1 POT = 0.0001 USDT

Users should know that the `gov` address can set any conversion rate without restriction.

#### Code Snippet

- https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Liquidity_Wallet.sol#L56-L62
 
### 3.2.2. Owner privileges

#### Severity: owner privileges

#### Description

1. Contract `Liquidity_Wallet` contract inherits basic access control properties from Openzeppelin's [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) contract, where the contract's ownership can be transferred or renounced. Renouncing ownership will leave the contract without an owner, thereby disabling any functionality that is only available to the owner.
2. Function `SetPause()` allows the owner to pause or resume the contracts functionalities available to the users. Users cannot trade POT tokens for USDT tokens if the contract is paused.
3. Function `SetGov()` allows the owner to update the `gov` address.
4. Function `Save()` allows owner to withdraw ERC-20 tokens from the contract.
5. Function `SetFee()` allow the `gov` address to set fee for user's claim amount.

## 3.3. [POT_Token.sol](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/POT_Token.sol)

### 3.3.1. Owner privileges

#### Severity: owner privileges

#### Description

1. Contract `POT_Token` contract inherits basic access control properties from Openzeppelin's [Ownable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) contract, where the contract's ownership can be transferred or renounced. Renouncing ownership will leave the contract without an owner, thereby disabling any functionality that is only available to the owner.
2. Function `ownerMint` allows the owner to mint arbitrary tokens.
3. Function `SetSigner()` allows the owner to update the `signerAddress`. All previous un-redeemed signatures will be invalid if the `signerAddress` is updated. New signatures must be generated for previous unclaimed signatures.

## 3.4. [Proof_Of_Trade_Arbi_One](https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Proof_Of_Trade_Arbi_One.sol)

### 3.4.1. Users Claim reward with USDT tokens

#### Severity: note

#### Description

Based on the [docs](https://traderdao.gitbook.io/traderdao/start-here/proof-of-trade/introduction-to-proof-of-trade-pot-interface#:~:text=Available%20to%20Claim), the Proof_of_Trade_Arbi_One contract should rewards users in POT tokens, but the contract's implementation rewards users in USDT tokens.

#### Code Snippet
- https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Proof_Of_Trade_Arbi_One.sol#L68

#### Recommendation

Consider reviewing the implementation or updating documentation based on the business requirements.

### 3.4.2. Owner privileges

#### Severity: owner privileges

#### Description
1. Function `SetSigner()` allows the owner to update the `signerAddress`. All previous un-redeemed signatures will be invalid if the `signerAddress` is updated. New signatures must be generated for previous unclaimed signatures.
2. Function `SetPause()` allows the owner to pause or resume the contracts functionalities available to the users. Users cannot deposit USDT tokens or Claim rewards if the contract is paused.
3. Function `Save()` allows owner to withdraw ERC-20 tokens from the contract.

## 3.4.3 Deposit with arbitrary parameters

### Severity: note/medium

### Description:

The function `deposit()` can be called by any user with any parameters. It allows an attacker to deposit with `id_` that already exists (duplicate deposit).
Since all logic is based on the backend, we can't say what severity it has.

### Code Snippet

- https://github.com/TraderDAOai/contracts/blob/41f87758492e7af5f3f51b6d68b48069bd70b74e/Proof_Of_Trade_Arbi_One.sol#L58


## 3.5. Signature verification

### Severity: note

### Description

In the current implementation there isn't this issue, but if you want to reuse this code, please, pay attention to the following:

1. Since message hash `getMessage(timestamp_, amount_, msg.sender)` has the same format for `Ambassador_Redeem_Contract`, `POT_Token`, `Proof_Of_Trade_Arbi_One`, the `signerAddress` must be different for each contract to avoid replaying signature.

2. The message hash generation also does not append any blockchain-specific information (PREFIX) to the message hash, allowing newly generated signatures to be replayed across many chains in case of deploying these contracts on other chains.



## 3.6. Follow good coding practice

### Severity: note

### Description

1. Missing docstrings

The contracts in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention, which is fundamental to correctly assess not only security but also correctness. Additionally, docstrings improve readability and ease maintenance. They should explicitly explain the purpose or intention of the functions, the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API. Functions implementing sensitive functionality, even if not public, should be documented as well. When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

2. Missing test suite

The contract is missing a test suite to validate and verify the behavior of the contract functionalities. Add tests are recommended to ensure that the contract functions and behaves as expected.

3. Missing zero address checks

In several places in the code, addresses are passed as parameters to functions. In many of these instances, the functions do not validate that the passed address is not the address 0. While this does not currently pose a security risk, consider adding checks for the passed addresses being nonzero to prevent unexpected behavior where required, or documenting the fact that a zero address is indeed a valid parameter.

4. Insufficient event logging

Multiple functions with owner privileges in the contracts do not emit an event. Events are useful to inform external dapps or users that an important state was modified on the contract.


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Pay attention to issue 3.4.3, it can have affect on backend part of project.

Users should be aware of the complete centralization of `TraderDAO`, where the owner can withdraw any tokens from smart contacts without limitation. Users can claim USDT from `TraderDAO` only if the owner adds enough USDT to contracts. The owner can mint `POT` tokens without restriction. 

Audited contracts don't implement the functionality described in the [Litepaper](https://traderdao.gitbook.io/traderdao/traderdao/litepaper-english); therefore, it uses centralized server-side for all `TraderDAO` logic.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

# 6. Previous audit reports

- https://gist.github.com/yuriy77k/57b8d726be90e8ee4e74c1a4151a235d
