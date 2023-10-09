# Quarashi Vesting v.2 Security Audit Report

# 1. Summary

[Quarashi Vesting](https://bscscan.com/address/0xe6394181e5fdfff83bebe76ef5898c22fa62c85d#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

- https://vesting.quarashi.network/

# 2. In scope

https://bscscan.com/address/0xe6394181e5fdfff83bebe76ef5898c22fa62c85d#code

# 3. Findings

In total, **0 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 0 low severity issues.

In total, **1 notes** were reported, including:

- 1 notes.

- 0 owner privileges.

No critical security issues were found.


## 3.1. The event `claimedTokenForUser` use inaccurate value

### Severity: note

### Description

In the function `transferTokens()` of `Vesting` contract when tokens transferred emit the event `claimedTokenForUser` with `processForInvestor[_investor][index].part` (line 869) as amount. But in case of last part transfer this amount may be little bit bigger, because it transfer all the rest amount `userSign.lockForUser` (line 854).

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
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.

# 5. Conclusion

The audited smart contract can be deployed. No security issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.



