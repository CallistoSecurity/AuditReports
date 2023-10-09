# XIX-Token Security Audit Report

# 1. Summary

`XIX-Token` smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

./contracts/IdentityRegistry.sol                
./contracts/lib/token/Token.sol                 
./contracts/lib/MultisigMembers.sol             
./contracts/lib/token/MintableToken.sol         
./contracts/EscrowedToken.sol                   
./contracts/lib/MultisigActionMembers.sol       
./contracts/PreApprovedTransferRestrictions.sol 
./contracts/mock/TransferRestrictionsMock.sol   
./contracts/Migrations.sol                      
./contracts/mock/IdentityRegistryMock.sol       
./contracts/lib/ITransferRestrictions.sol       
./contracts/lib/IIdentityRegistry.sol           


# 3. Findings

In total, **6 issues** were reported, including:

- 2 high severity issues.

- 3 medium severity issues.

- 1 low severity issues.

In total, **2 notes** were reported, including:

- 2 notes.



## 3.1. Functions `increaseAllowance` and `decreaseAllowance` falls in endless loop

### Severity: high

### Description

The functions `increaseAllowance` (lines 112-115 in file `Token.sol`) and `decreaseAllowance` (lines 117-120 in file `Token.sol`) will failed due of calling itself.

### Recommendation

To call functions from the parent contract contract should be used keyword `super.`. I.e. `return super.increaseAllowance(spender, addedValue);` and `return super.decreaseAllowance(spender, addedValue);`

## 3.2. The function `transferFrom` and `delegatedDepositToEscrow` may fail without printing the reason

### Severity: note

### Description

In the function `transferFrom` (line 196 in file `Token.sol`) and `delegatedDepositToEscrow` (line 72 in file `EscrowedToken.sol`) when you call `_approve(sender, msg.sender, allowance(sender, msg.sender) - amount);`  the transaction will fail if `allowance(sender, msg.sender) < amount` bot no reason will be printed.

### Recommendation

To simplify debugging will be good to add before `_approve`:

`require(allowance(sender, msg.sender) >= amount, "Amount exceeds allowance");`


## 3.3. Anybody can set `transferRestrictions` to address(0)

### Severity: high

### Description

There is not restriction to set `transferRestrictions` to `address(0)` (line 150 in file `Token.sol`). Therefor anyone can set it to `address(0)` and block token transfers.

### Recommendation

Replace lines 149-155 in file `Token.sol` with:
```Solidity
if (address(0x0) != _newRestrictions){
    require(address(this) == ITransferRestrictions(_newRestrictions).getOwner(), "Owner of new transfer restrictions must be set to (this) relying contract.");
}
```

## 3.4. Gas wasting

### Severity: note

### Description

In the loops `for` (lines 95, 102, 123 in file `MintableToken.sol`), you declare `uint32 i`, but EVM natively works with `uint256` values, so it will need to spend additional gas for converting `uint32` to/from `uint256` on each step.

### Recommendation

Use `uint` or `uint256` types for local variables.

## 3.5. Identity provider may be added without provider meta

### Severity: medium

### Description

In the function `addIdentityProvider` the identity provider meta adding only if adding action is not approved (lines 82-86 in file `IdentityRegistry.sol`). But in the case of single `idmAdmins` member, this action will be approved on the first call of `addIdentityProvider`, and the identity provider will be added without metadata.

### Recommendation

Replace code in lines (lines 82-88 in file `IdentityRegistry.sol`) with:

```Solidity
    if(0 == idpMetadata[idprovider].timestamp){ // create meta entry for easier parsing by DAPPs
        idpMetadata[idprovider] = ProviderMeta(name, did, block.timestamp, false);
    }
    
    if(false == idmAdmins.approveAction(action_params, msg.sender)){
        return false; // we do not have enough signatures yet
    }
```

## 3.6. Identity provider signature may be reused

### Severity: low

### Description

In the function `addVerifiedUser` the identity provider sign message `userAddr, " is person with legitimate identity"` (line 169 in file `IdentityRegistry.sol`). 

In the event of reusing this contract for another project and using the same identity provider, it's possible to reuse the provider signature to add the same user to another project (even if that project has another requirement for verification).

### Recommendation

Add contract address to signed message:

```Solidity
bytes memory data = abi.encodePacked(address(this), userAddr, " is person with legitimate identity");
```

## 3.7. Not possible to restore revoked user

### Severity: medium

### Description

If some user will be revoked by mistake using function `revokeUser` (line 140 in file `IdentityRegistry.sol`) there is no way to restore him.

Please note, revoked users can transfer tokens.

### Recommendation

Add function that allows `idmAdmins` to restore the user.

## 3.8. Not possible to renew users verification

### Severity: medium

### Description

If `expirationDays` (line 136 in file `IdentityRegistry.sol`) will be set to some small period of time (i.e., a few years) and this time period was passed after users verification, the function `verifyIdentity` (line 62 in file `IdentityRegistry.sol`) will always return `false` for them.

### Recommendation

Add ability to renew users' verification.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion


The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

