# L-o-A Security Audit Report

# 1. Summary

[L-o-A contracts](https://github.com/L-o-A/SmartContracts/tree/main/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [b0f270c49ebcb707726e88c48018e684c00cf446](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/)

1. Capsule.sol
2. CapsueData.sol
3. CapsuleStaking.sol
4. LOAUtil.sol
5. LoANFT.sol
6. LoANFTData.sol
7. LoANFTFusion.sol
8. MultiSigAdmin.sol
9. NFTMarket.sol
10. Raffle.sol
11. RaffleHelper.sol
12. IAdmin.sol
13. IUtil.sol

# 2.1. Excluded

Calculation formulas excluded from audit due to lack of contract documentation.

Standard OpenZeppelin contracts were excluded from audit:

- @openzeppelin/contracts/token/ERC1155/ERC1155.sol
- @openzeppelin/contracts/access/Ownable.sol
- @openzeppelin/contracts/utils/Counters.sol
- @openzeppelin/contracts/security/ReentrancyGuard.sol
- @openzeppelin/contracts/utils/math/SafeMath.sol
- @openzeppelin/contracts/token/ERC1155/utils/ERC1155Holder.sol

# 3. Findings

In total, **5 issues** were reported, including:

- 1 high severity issues.

- 3 medium severity issues.

- 1 low severity issues.

In total, **6 notes** were reported, including:

- 4 notes.

- 2 owner privileges.



## 3.1. Claim requires all Raffle Ticket Ids should be winner

### Severity: note

### Description

If in the array of Raffle Ticket Ids will be at least one ticket that [don't win](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/Capsule.sol#L109) the transaction failed.

### Recommendation

Replace:
```Solidity
            require(status == 3, "Ticket is not a winner");
```
with
```Solidity
            if(status != 3) continue;
```


## 3.2. Claim function of Capsule contract vulnerable to reentrance attack  

### Severity: high

### Description

When [mint](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/Capsule.sol#L114) capsule token the callback function [onERC1155Received](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/98c3a79b5765d58ef27856b8211c70a4907c63be/contracts/token/ERC1155/ERC1155.sol#L497) will be called from destination address. So it allow attacker to call [claim](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/Capsule.sol#L98) function with the same ticket many time and mint many capsule tokens.

### Recommendation

[Burn](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/Capsule.sol#L115) ticket before `mint` capsule token.
```Solidity
            _raffleContract.burn(owner, ticketId);
            _mint(owner, capsuleId, 1, "");
```

Or use [ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol).

## 3.3. addNFTAttributeLimits of LoANFTData contract does not allow maxValues = minValues

### Severity: note

### Description

According error message of [these requirements](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L338-L343) `Max < Min` should be allowed values when `Max == Min`, but `require(maxValues[i] > minValues[i])` don't allow it.

### Recommendation

Fix require condition:
```Solidity
require(maxValues[i] >= minValues[i], "Max < Min");
```

or fix error message:
```Solidity
require(maxValues[i] > minValues[i], "Max <= Min");
```

## 3.4. Issues of using `sudoRandom`

### Severity: medium

### Description

Function [sudoRandom](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LOAUtil.sol#L25) returns value in range `[99..0]`.

1. Function [populateAttribute](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L368-L379) use `sudoRandom` to set `attributes` in range `[min..max]`. But according [this](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L370) and [this](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L377-L378) calculation the `max` value never reached.

For example: let's say `min = 2` and `max = 3`. According calculation `min + ((max-min) * [99..0] / 100` we get `2 + (1 * 99 / 100) = 2`. So max value 3 will be never assigned. 

Recommendation: use calculation `min + ((max - min + 1) * sudoRandom / 100`.

2. In the function [randomSubList](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L543) the `index` can be bigger then 99 even if `list.length` bigger than 99. Also, if `list.length` less then 99 the lower index has higher change to be selected.

For example, if `list.length = 70` then indexes from `[0..29]` has twice more chance to be selected than indexes `[30..69]`. I.e. index = 1 will be selected if `sudoRandom` returns 1 or 71, but index 30 will be only if `sudoRandom` returns 30.

Moreover, `sudoRandom` has [cycle of 31](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LOAUtil.sol#L27) values. I.e if `startCount = 32` than `sudoRandom` returns the same value as for `startCount = 1`. It means that [units](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L542) can't be greater than 31.

Recommendation: use another type of random generator.

## 3.5. Mismatch require condition and description

### Severity: note

### Description

1. The in the function `addNFTSupply` the [require](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L397) condition mismatch description.

* Recommendation

Use condition `<=` instead of `<`:

`require(nftSupply._consumed[heroes[i]] <= supply[i], "Supply cant be less than consumed");`

2. Require condition in the function [list](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/NFTMarket.sol#L149) check `price > 1 ether` but description say `Price must be at least 1 wei`.




## 3.6. addNFTSupply overwrite NFT total supply

### Severity: low

### Description

The [nftSupply._total_supply](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L393) reset to 0 each time when admin call `addNFTSupply`. If admin wants to change supply just for 1 hero the [total_supply](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L399) will be supply just of this hero.


## 3.7. Transaction may fail without error message

### Severity: note

### Description

This
https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L430
and this 
https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LoANFTData.sol#L434
requirements return error message only if `_supply == _consumed`. If `_supply < _consumed` transaction fails without error message due to of underflow.

### Recommendation

Use `require(_supply > _consumed)` instead of `require(_supply - _consumed > 0)`.

## 3.8. On-chain random number generator is predictable

### Severity: medium

### Description

[LOAUtil](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/LOAUtil.sol#L8-L30) contract contains functions for pseudo random number generation (RNG) which are using in other LOA contracts. But result of this function can be predicted.

If attacker create contract that calls one of `random` functions and if result satisfy attacker then `fusion` or `mint` function will be called, in other case transaction will revert. It will allow attacker to check result before send transaction to LOA.

### Recommendation

Use:
1. Off-chain RNG;
2. RNG from some Oracle provider.
3. Two-steps RNG where on one step user request random number and on next step (in few blocks) get result (in this case only miners can try to manipulate result). For example:

```Solidity
mapping(address => uint256) requestBlock;    // user address => block number

function requestRandom() external {
    requestBlock[msg.sender] = block.number;
}

// should be called in 2 blocks after request but not longer than 250 blocks
function claimRandom() external returns (uint256){
    uint256 reqBlock = requestBlock[msg.sender];
    require(block.number - reqBlock > 1 && block.number - reqBlock <= 250, "expired");
    return(uint256(keccak256(abi.encodePacked(msg.sender, blockhash(reqBlock+1)))));
}
```

## 3.9. Possible reentrance 

### Severity: medium

### Description

In the functions [unlist](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/NFTMarket.sol#L230) and [buy](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/NFTMarket.sol#L316) is possible reentrance attack to change queue in `_listed_items`. 

### Recommendation

Call function [safeTransferFrom](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/NFTMarket.sol#L239) in the end of [unlist](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/NFTMarket.sol#L261) function.

The same for [buy](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/NFTMarket.sol#L316) function.


## 3.10. Owner privileges

### Severity: owner privileges

### Description

1. Admin in `Capsule` contract can [burn](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/Capsule.sol#L80) NFT from any wallet.
2. Any of valid admins in [MultiSigAdmin](https://github.com/L-o-A/SmartContracts/blob/b0f270c49ebcb707726e88c48018e684c00cf446/contracts/MultiSigAdmin.sol#L39-L42) can add/remove `admin`, `RaffleAddress`. Can set/change address of `treasury`, `axionAddress`, `nftFusionAddress`, `marketAddress`, `nftAddress`, `capsuleAddress`, `capsuleStakingAddress`, `capsuleDataAddress`, `nftDataAddress`, `utilAddress`.


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

