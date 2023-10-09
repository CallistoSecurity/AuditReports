# Quarashi Vesting Security Audit Report

# 1. Summary

[Quarashi Vesting](https://bscscan.com/address/0x46589Ab934277E44A5060f3273761b86396d5429#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://vesting.quarashi.network/

# 2. In scope

https://bscscan.com/address/0x46589Ab934277E44A5060f3273761b86396d5429#code

# 3. Findings

In total, **1 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 1 low severity issues.

In total, **2 notes** were reported, including:

- 1 notes.

- 0 owner privileges.

No critical security issues were found.

## 3.1. Tokens claiming is centralized

### Severity: low

### Description

The function `claimForUser()` of `Vesting` contract checks the elapsed time and sends tokens if conditions are met to investor. But only address with `BACK_ROLE` can call it (file Vesting.sol lines 117-120). This restriction makes claim tokens centralized and completely depends on `BACK_ROLE` address. Since smart contract checks the elapsed time and amounts you may allow to call this function by any address. It makes vesting decentralized.

### Recommendation

Remove in file Vesting.sol lines 117-120:
```Solidity
        require(
            hasRole(BACK_ROLE, _msgSender()),
            "Vesting: caller is not back"
        );
```



## 3.2. The event `claimedTokenForUser` use inaccurate value

### Severity: note

### Description

In the function `transferTokens()` of `Vesting` contract when tokens transferred emit the event `claimedTokenForUser` with `processForInvestor[_investor][index].part` (file Vesting.sol line 191) as amount. But in case of last part transfer this amount may be little bit bigger, because it transfer all the rest amount `userSign.lockForUser` (file Vesting.sol line 176).

### Recommendation

Use additional variable `amount` like this:

```Solidity
    function transferTokens(address _investor, uint256 index) internal {
        Inv memory userSign = processForInvestor[_investor][index];
        uint256 amount;
        if (userSign.lockForUser / userSign.part == 1) {
            processForInvestor[_investor][index].lockForUser = 0;
            amount = userSign.lockForUser;
        } else {
            processForInvestor[_investor][index].lockForUser =
                userSign.lockForUser -
                userSign.part;
            amount = userSign.part;
        }
        require(
            erc20.transfer(_investor, amount),
            "Vesting: tokens didn`t transfered"
        );
        emit claimedTokenForUser(
            _investor,
            amount,
            block.timestamp,
            index
        );
    }
```


# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.



