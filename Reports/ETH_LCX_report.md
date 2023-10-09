# LCX Security Audit Report

# 1. Summary

[LCX](https://etherscan.io/address/0x037a54aab062628c9bbae1fdb1583c195585fe41#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://etherscan.io/address/0x037a54aab062628c9bbae1fdb1583c195585fe41#code

# 3. Findings

In total, **7 issues** were reported including:

- 2 high severity issues.

- 3 low severity issues.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. No checking for zero address

### Severity: low

### Description

Incoming addresses should be checked for an empty value(0x0 address).

### Code snippet

Lines 241, 561, 600

```js
        function setTokenAddress(IERC20 token) public onlyOwner returns(bool){
                LCXToken = token;
                return true;
        }


        function setTokenVestingAddress(TokenVesting tokenVestingAddress) public onlyOwner returns(bool){
                vestingContractAddress = tokenVestingAddress;
                return true;
        }       

        function batchTransfer(address[] memory accounts, uint256[] memory values ) public onlyOwner returns(bool){
                require(accounts.length == values.length);
                for(uint256 i=0;i< accounts.length;i++){
                _transfer(msg.sender, accounts[i], values[i]);
                }
                return true;
        }
        
```


## 3.2. Owner Privileges

### Severity: owner privileges

### Description
Contract owner allow himself to:

1. Upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

Lines 241, 561.

```solidity
        function setTokenAddress(IERC20 token) public onlyOwner returns(bool){
        LCXToken = token;
        return true;
        }


        function setTokenVestingAddress(TokenVesting tokenVestingAddress) public onlyOwner returns(bool){
        vestingContractAddress = tokenVestingAddress;
        return true;
        }       
```

2. Revoke the vesting.

```solidity
        function revoke(address account) public onlyOwner {
        VestedToken storage vested = vestedUser[account];
        require(!vested.revoked);
        uint256 balance = vested.totalToken;
        uint256 unreleased = _releasableAmount(account);
        uint256 refund = balance.sub(unreleased);
        vested.revoked = true;
        vested.totalToken = unreleased;
        LCXToken.safeTransfer(owner(), refund);
        emit VestingRevoked(account);
        }  
```
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

Line: 262.

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
## 3.5. During revoking the owner receives more tokens than it should

### Severity: high

### Code snippet

* Line 372.

### Description

During `revoke()` the owner receives `vested.totalToken` value minus the unreleased tokens:
```solidity
uint256 refund = balance.sub(unreleased);
```
This is wrong because it does not take into account the number of tokens already paid(`vested.releasedToken`).

Let's look at an example.

|Passed periods|`totalToken`|`releasedToken`|`unreleasedToken`|TokenVesting Balance|Owner Balance
|---|---|---|---|---|---
|0|1000|0|0|2000|0

The total number of tokens Michael should receive is 1000 during 10 vesting periods. After 2 vesting periods he calls the `releaseToken` function and receive 300 tokens.

|Passed periods|`totalToken`|`releasedToken`|`unreleasedToken`|TokenVesting Balance|Owner Balance
|---|---|---|---|---|---
|2|1000|200|0|1800|0

Then 4 more periods passed.

|Passed periods|`totalToken`|`releasedToken`|`unreleasedToken`|TokenVesting Balance|Owner Balance
|---|---|---|---|---|---
|6|1000|200|400|1800|0

The owner decided to cancel the vesting payout and called `revoke` function. He receives `totalToken` value minus the unreleased tokens `1000 - 400 = 600`:

|Passed periods|`totalToken`|`releasedToken`|`unreleasedToken`|TokenVesting Balance|Owner Balance
|---|---|---|---|---|---
|6|400|200|400|1200|600

And if Michael asks for the unreleased amount, the balance of the vesting contract will be less than expected: `1200 - 400 = 800`. But the initial balance was 2000 and 1000 were allocated for Michael.

## 3.6. The beneficiary will not receive tokens after revoking

### Severity: high

### Code snippet

* Line 374
* Line 341

### Description

After the owner calls `revoke` function, the value of `vested.totalToken` variable is set to number of unreleased tokens.
```Solidity
    vested.totalToken = unreleased;
```
And when beneficiary calls `releaseToken` function, the amount of unreleased tokens will be calculated: `_vestedAmount(account).sub(vestedUser[account].releasedToken)`. But `_vestedAmount` for revoked beneficiary is `totalToken` amount. In the example from issue 3.5. if in second step we passed not 4 but 1:

|Passed periods|`totalToken`|`releasedToken`|`unreleasedToken`|TokenVesting Balance|Owner Balance|revoked
|---|---|---|---|---|---|---
|6|100|200|100|900|900|true

And during the `releaseToken` the calculation will be following: `100 - 200 = -100` and that would lead to `throw`.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/317f4b690d3b800d4fcd06c84a468ab1

https://gist.github.com/yuriy77k/84cf07db10680d1e63ee8951d89e0f14

https://gist.github.com/yuriy77k/a45940dd96edeb7eff93e43a2ba43d7b

