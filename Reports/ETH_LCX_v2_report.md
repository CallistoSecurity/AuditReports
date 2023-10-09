# LCX v.2 Security Audit Report

# 1. Summary

[LCX](https://etherscan.io/address/0x037a54aab062628c9bbae1fdb1583c195585fe41#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://etherscan.io/address/0x037a54aab062628c9bbae1fdb1583c195585fe41#code (Token contract)

Vesting contract commit hash 1021bf8e087d1c3bd56ddc9f7f117e5d94a727ca.

- [vestingToken.sol](https://github.com/tech-lcx/LCX-Smart-Contract/blob/master/vestingToken.sol).

# 3. Findings

In total, **5 issues** were reported including:

- 3 low severity issues.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Owner Privileges

### Severity: owner privileges

### Description

Contract owner allow himself to:

1. Upgrade the token contract and implement any logic in the new contract: 

```
    function setTokenAddress(IERC20 token) public onlyOwner returns(bool){
        LCXToken = token;
        return true;
    }      
```

2. Revoke the vesting, and take users tokens for himself even if the tokens were bought by the users using ETH or a different asset.

```
    function revoke(address account) public onlyOwner {
        VestedToken storage vested = vestedUser[account];
        require(!vested.revoked);
        uint256 balance = vested.totalToken;
        uint256 vestedAmount = _vestedAmount(account);
        uint256 refund = balance.sub(vestedAmount);
        require(refund > 0);
        vested.revoked = true;
        vested.totalToken = vestedAmount;
        LCXToken.safeTransfer(owner(), refund);
        emit VestingRevoked(account);
    }
```

## 3.2. No checking for zero address

### Severity: low

### Description

Incoming addresses should be checked for an empty value(0x0 address).

### Code snippet

https://github.com/tech-lcx/LCX-Smart-Contract/blob/1021bf8e087d1c3bd56ddc9f7f117e5d94a727ca/vestingToken.sol#L229

## 3.3. Bypassing Smart Contract Timelocks

### Severity: low

### Description

There are no checks that the beneficiary address can be a contract address. 

Example: 

The user was able to divest himself of his interest even though the tokens never moved. He didn’t sell the timelocked tokens itself. He sold the future ownership of the tokens. 
The user is asked by the deployer of the LCX vesting contract for the address where he’d like to receive his tokens after the releaseTime expires in 3 years.
User deploys the “Bypasser” contract and gives its address to the deployer of the LCX vesting  contract. The Bypasser contract didn’t magically make the timelocked tokens transferable — it made the future ownership of the timelocked tokens transferable.

More details [here](https://blog.openzeppelin.com/bypassing-smart-contract-timelocks/).

### Code snippet

https://github.com/tech-lcx/LCX-Smart-Contract/blob/1021bf8e087d1c3bd56ddc9f7f117e5d94a727ca/vestingToken.sol#L287-L298

```solidity
        function _setVesting(address account, uint256 amount, uint256 cliff, uint256 duration, uint256 startAt) internal {
         
         require(account!=address(0));
         require(cliff<=duration);
         VestedToken storage vested = vestedUser[account];
         vested.cliff = cliff;
         vested.start = startAt;
         vested.duration = duration;
         vested.totalToken = amount;
         vested.releasedToken = 0;
         vested.revoked = false;
         }
```

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/d2acf4c4207940beb4e7e713c03ce357

https://gist.github.com/yuriy77k/b09a26298f55608e3fc519523ada2625

https://gist.github.com/yuriy77k/580a8a837ce98fcc6dcf7c8ccbe9b436

