# Sector Token Security Audit Report

# 1. Summary

[Sector Token](https://github.com/sectorius/sector-contracts/tree/main/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> Smart contracts for sector investment platform

# 2. In scope

Commit [1d4482fcee61db9033d7305b44658797eb408536](https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/)

- SecToken4.sol
- SECICO5.sol

# 3. Findings

In total, **3 issues** were reported, including:

- 0 high severity issues.

- 3 medium severity issues.

- 0 low severity issues.

In total, **1 notes** were reported, including:

- 0 notes.

- 1 owner privileges.


## 3.1. Multiple issues in the function `transferOwnership`

### Severity: medium

### Description

1. Transfer event don't emitted - ERC20 incompatibility. In the function [transferOwnership](https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/SecToken4.sol#L37) the balance transfer from old owner to new owner. Due to ERC20 standard the event `Transfer` should be emitted.
2. If `newOwner` has tokens on balance it will not be counted when transfer balance from `owner`. 
3. Very recommended to emit event `OwnershipTransferred` in transfer ownership.

### Recommendation

Add `event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);` into `Sector` contract.

Replace function [transferOwnership](https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/SecToken4.sol#L33-L39) with this:

```Solidity
    function transferOwnership(address newOwner) external {
        require(msg.sender == owner,"Only owner");
        require(newOwner != address(0),"Cannot set empty owner");
        uint256 ownerBalance = _balances[owner];
        _balances[newOwner] = _balances[newOwner] + ownerBalance;
        _balances[owner] = 0;
        emit Transfer(owner, newOwner, ownerBalance);
        emit OwnershipTransferred(owner, newOwner);
        owner = newOwner;
    }
```

## 3.2. Owner can't transfer tokens

### Severity: medium

### Description

In the function [_transfer]https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/SecToken4.sol#L166) the require condition do not allow `owner` to transfer token. Using function `sendTokensToSale()` will not help due to it use `transferFrom` and `transferFrom` use `_transfer` in anyways. 

## 3.3. Loosing accuracy

### Severity: medium

### Description

1. In the function [depositX()](https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/SECICO5.sol#L99-L100) the small amount may left on the contract if `userSent` not a multiple of the 7000 or 3000.
Use `uint256 Invest = userSent - Dev;`
2. In calculation [tokenPerWEI](https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/SECICO5.sol#L139) should be used nominator to avoid loosing accuracy. 




## 3.4. Owner may withdraw any tokens from SECICO contract

### Severity: owner privileges

### Description

The function [retrieveAnyERC20](https://github.com/sectorius/sector-contracts/blob/1d4482fcee61db9033d7305b44658797eb408536/contracts/SECICO5.sol#L272-L274) allow `owner` to withdraw any tokens from contract including rewards tokens.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [x] **Standard ERC20-related issues.** - IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
