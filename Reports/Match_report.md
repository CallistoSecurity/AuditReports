# Match-Contract Security Audit Report

# 1. Summary

[Match-Contract](https://github.com/CallistoSecurity/Match-Contract/tree/main/contract) smart contract security audit report performed by [GreyWolf](https://github.com/greywolf12)

# 2. In scope

Commit `7d806ffbdc8982a17b773d9786f37d1256b03e07`

- [Match.sol](https://github.com/CallistoSecurity/Match-Contract/blob/7d806ffbdc8982a17b773d9786f37d1256b03e07/contract/Match.sol)

# 3. Findings

In total, **2 issues** were reported, including:

- 1 high severity issue.

- 0 medium severity issues.

- 1 low severity issue.

In total, **3 notes** were reported, including:

- 2 notes.

- 1 owner privilege.



## 3.1. Owner privileges

### Severity: owner privileges

### Description

Only the owner can call the function `announceResult()` to check the winner. Challengers can't check winners if the owner is unavailable.


## 3.2. The constant value of the fee

### Severity: low

### Description

The chainlink request fee is set as a constant value in 1.4 Link tokens. But the fee can be changed during the time, so the request could not be fulfilled in case of increasing fee.

### Code snippet
- https://github.com/CallistoSecurity/Match-Contract/blob/7d806ffbdc8982a17b773d9786f37d1256b03e07/contract/Match.sol#L26

### Recommendation

Don't use constant `fee`; add a method to allow the owner to change the fee. 

## 3.3. Zero address check

### Severity: high

### Description

If `CHALLENGER_1` and `CHALLENGER_2` does not contain the correct addresses, the USDT that may be sent to the contract will be lost. But contract code set `CHALLENGER_1` and `CHALLENGER_2` to constant `address(0)`. It means that a contract can't be used without modification.

### Code snippet

- https://github.com/CallistoSecurity/Match-Contract/blob/7d806ffbdc8982a17b773d9786f37d1256b03e07/contract/Match.sol#L15-L16

### Recommendation

Don't use constant `CHALLENGER_1` and `CHALLENGER_2` addresses. Set these addresses in the constructor.

```Solidity
    address public CHALLENGER_1;
    address public CHALLENGER_2;

    constructor(address challenger_1, address challenger_2) ConfirmedOwner(msg.sender) {
        require(challenger_1 != address(0) && challenger_2 != address(0), "challenger address can't be 0");
        CHALLENGER_1 = challenger_1;
        CHALLENGER_2 = challenger_2;
        setChainlinkToken(CHAINLINK_TOKEN);
        setChainlinkOracle(CHAINLINK_ORACLE);
        _owner = msg.sender;
    }    

```

## 3.4. The `announcementHours` is in past

### Severity: note

### Description

According to the logic of the `announceResult()` function we should wait until `announcementHours`.
But it is set to `January 30, 2023 12:00:00 PM GMT+08:00`, so this requirement does not make sense because it is past.

### Code snippet

- https://github.com/CallistoSecurity/Match-Contract/blob/7d806ffbdc8982a17b773d9786f37d1256b03e07/contract/Match.sol#L14
- https://github.com/CallistoSecurity/Match-Contract/blob/7d806ffbdc8982a17b773d9786f37d1256b03e07/contract/Match.sol#L119

### Recommendation

Update `announcementHours` to date in the future or set it in the constructor.

## 3.5. Centralized source of the result

### Severity: note

### Description

The winner is chosen according to the result obtained from "https://api.derektoyzboxing.com/result" received from Operator contract "0x1db329cDE457D68B872766F4e12F9532BCA9149b". How the result is generated and how the operator translates it to this contract is not possible to confirm in this audit.

So users should trust them if they agree to use this contract. 

