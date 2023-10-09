# CryptoAtoms Security Audit Report

# 1. Summary

[CryptoAtoms](https://www.cryptoatoms.org/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash 07d0159a506093e7fb90807198d1daa46e3797bb.

- [CryptoAtoms.sol](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol).
- [CryptoAtomsLogic.sol](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol).
- [CryptoAtomsToken.sol](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol).

# 3. Findings

In total, ** issues** were reported including:

- 2 high severity issues.

- 4 low severity issues.

- 2 notes.

- 10 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. tx.origin exploit (onlyOwnerOf)

### Severity: High

### Description

tx.origin should never be used for authorization, `CryptoAtomsLogicV2` contain a modifier that checks if tx.orign is the owner of a non-fungible token. The same modifier is used in multiple functions to grant access to them and it can be exploited using a proxy contract to do multiple actions like setting a very low buy price and take any possible tokens in possession of the victim.

If the victim is lured to call the attacker contract, the victim address will be equal to tx.orgin thus any external call from the attacker contract using the same transaction will grant access to most sensible functions of `CryptoAtomsLogicV2`.

Attacker Contract example:

```
   contract CryptoAtomsLogicV2Interface {
      function setIsBuyByAtom(uint _atomId, uint128 _fee) external; // just an example, other functions using `onlyOwnerOf` modifier can be exploited.
   }

   contract AttackerContract {
      constructor() {}
      CryptoAtomsLogicV2Interface contract_address:

      function setContractAddress(address addr)
      {
         contract_address= CryptoAtomsLogicV2Interface(addr);
      }
   
      function proxyFunction() public {
         contract_address.setIsBuyByAtom(1,23 finney);
      }
   }
```

Targeted functions:

- [`setIsRentByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L112)
- [`setIsBuyByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L118)
- [`unsetIsRentByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L124)
- [`unsetIsBuyByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L129)
- [`autoRentByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L134)
- [`rentByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L141)
- [`evolveByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L173)

Please note also that `tx.origin` has to be removed from [1](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L166#L169) and [2](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L186) otherwise it might set the wrong address when for example `setAtomOwner` is called, since a contract can be used to handle the tokens and the tokens might end up by being owned by the transaction initiator instead of the contract (tx.orgin has also to be removed from all event inside the same contract). 

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L56#L59

### Recommendation

Never use tx.origin, unless being aware of all the risks.

## 3.2. Token Approval

### Severity: High

### Description

Once [`approve`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L220) member of `CryptoAtomsToken` contract is called, if the `msg.sender` is the owner, `tokenApprovals` for the token id is set to the approved address, however once one of the following function is called:

- [`safeTransferFrom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L167)
- [`safeTransferFrom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L184)
- [`transferFrom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L206)
- [`setAtomOwner`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L144)
- [`transfer`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L218)

`tokenApprovals` state variable is not reset to `address(0)` meaning the the previously allowed address will still be able to transfer the tokens, this can be used for example to resell a token multiple time by the attacker, setting its own address as approved will guarantee the attacker to get back his token.

Please note that the same issue is applicable for `CryptoAtoms` contract through:

- [`approve`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L223)
- [`transfer`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L218)
- [`takeOwnership`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L229)
- [`setAtomOwner`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L144)

### Recommendation

Both `tokenApprovals` member of `CryptoAtoms` contract and `tokenApprovals` member of `CryptoAtomsToken` contract should be reset to zero after changing the any token's owner.

## 3.3. Precision issue

### Severity: low

### Description

Solidity operates only with integers. Thus, variables multiplication should be done before the division in order to reduce the rounding errors.

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L159

### Recommendation

We recommend changing the order of division and multiplication.

## 3.4. Payable Fallback Function

### Severity: low

### Description

The payable fallback function implemented in `CryptoAtoms`, `CryptoAtomsLogicV2` and `CryptoAtomsToken` contracts allow ether deposit to the contract that can be withdrawn later on through `withdrawBalance` by the admins, please note that no logic is implemented when ether is deposited. For `CryptoAtomsLogicV2` the fallback deposit is implemented but no withdrawal function exist, meaning that the ether sent through the fallback will be frozen in the contract.

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L87

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L38

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L103

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L123#L126

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L105

### Recommendation

If ether deposit is needed, custom function can be implemented with a different name to avoid users mistakes.

## 3.5. Self Destruct

### Severity: owner privileges

### Description

`CryptoAtoms` contract contain a kill function that trigger 'selfdestruct' if the `msg.sender` is the `COO`, however most non-fungible token might be owned by other parties than the contract owners and kill should never be called since the owners doesn't own all of them.

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L81#L85

## 3.6. Address(0) Verification

### Severity: low

### Description

`transferAdmin` and `transferContract` function members of `ADM312` should check if the new addresses are different than `address(0)`.
And the destination address when the token is sent, is not checked for a null value. Transfers to `address(0)` can occure.

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L25#41

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L43#49

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L209

## 3.7. Owner Privileges

### Severity: owner privileges

### Description

Please note that even function with `onlyContract` modifier can be accessed by the owners since [`transferContract`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L43) function allow the owners to change the contract address to any other address.

1. [`setDnaExist`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L132) function allow the owner to change the state of a dna to non-existing, making the owner able to re-assign the same dna to a new non-fungible tokens that are supposed to be unique using [`createCustomAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L114).
 
2. [`setOwnerAtomsCount`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L140) allow the owners to change the [`balanceOf`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L201) a user, even if he still owner a number of tokens that is different than what the function returns, which also make the contract non-complaint to [ERC721](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md).
    
3. [`setAtomOwner`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L144) allow the owner to change the the non-fungible token owners without permission.

4. [`pushAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L148) function allow the owner to create a non-fungible token with even an existing dna since it does not check if the dna previously exists.

5. Please note that the following functions allow the owners to change every propriety of the non-fungible tokens even if they are owned and bought by different addresses.
	- [`setAtomDna`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L153), 
	- [`setAtomGen`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L157) 
	- [`setAtomLev`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L161) 
	- [`setAtomCool`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L165)
	- [`setAtomSons`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L169)
	- [`setAtomFath`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L173)
	- [`setAtomMoth`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L177)
	- [`setAtomIsRent`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L181) 
	- [`setAtomIsBuy`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L185)
	- [`setAtomIsReady`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L189)

All users should be aware that the contract owners have allowed themselves to change every aspect of the tokens that aren't even owned by them.

Also, the contract owner allow himself to:

6. [Withdraw](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L123) all eth at any time.

7. Owner can [change contract](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtoms.sol#L43) addresses without any restrictions and there is possibility that this contracts will be not audited.

8. Owner can [pause](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L92) contract.

9. Owner can [change rates](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L104) any time.

## 3.8. Crypto Atoms Core Contract

### Severity: note

### Description

Crypto atoms core contract defined by the interface `CaCoreInterface` in `cryptoatomslogic.sol` was not disclosed to us, the audit was conducted on the provided sol file. The audit conducted by "github.com/RideSolo" do not guarantee the safety of the audit when it comes to the following functions.

The following functions uses `CaCoreInterface`:

- [`autoRentByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L134)
- [`rentByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L141)
- [`receiveBonus`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L184)
- [`buyByAtom`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L152)

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L5#L9

## 3.9. Naming Error

### Severity: note

### Description

The compiler raised the following issue "Identifier not found or not unique. CaData public CaDataContract = CaData(CaDataAddress);", `CaData` contract is not defined in the provided contracts, however following the external functions calls using `CaData`, `CaData` is `CryptoAtoms` contract contained in `CryptoAtoms.sol`. 

### Code snippet

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsLogic.sol#L14

https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L95

## 3.10. Gas Limit

### Severity: low

### Description

If the tokens total supply is big enough, [`tokenOfOwnerByIndex`](https://github.com/Project312/CryptoAtoms/blob/07d0159a506093e7fb90807198d1daa46e3797bb/CryptoAtomsToken.sol#L359) might throw for gas limit reached since most nodes even for view functions throws, except if a special configuration is set through the geth or parity node for example.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/013d60c04bed49dee39f101bdd573dc0

https://gist.github.com/yuriy77k/5e82d78c80c5e0ac9269d5ad130a335e

https://gist.github.com/yuriy77k/61051e7a67b1a32f4b2ee05665a1b004
