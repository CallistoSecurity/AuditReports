# 1. Summary

[TRET Token](https://github.com/tretchainvn/smart-contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [tretchain.sol](https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol) github commit hash 6dff08db644c1fc79d89683ba7bd76e0c92311a3.

# 3. Findings

In total, **10 issues** were reported including:

- 5 medium severity issues.

- 3 low severity issues.

- 2 minor observation.

## 3.1. Allowance Approval
 
### Severity: medium

### Description

Following ERC-20 final [description](https://eips.ethereum.org/EIPS/eip-20):

"NOTE: To prevent attack vectors like the one [described here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit) and discussed [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729), clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn't enforce it, to allow backwards compatibility with contracts deployed before"

The implemented `approve` function:
- Do not preserve backwards compatibility since it enforces the allowance values to be set.
- Do not throw in case if the following condition is true `if (_value != 0 && allowed[msg.sender][_spender] != 0)` and return false, users might not notice that the changes didn't occur, and external contract calls to this function will highlight many other issues.

### Recommendation

Do not try to mitigate the issue and implement `increaseApproval` and `decreaseApproval` functions instead.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L252

## 3.2. Admin Burn Logic
 
### Severity: medium

### Description

Token burning logic is only accessible by the contract owner using `burn` or `adminBurnToken`, however if `adminBurnToken` is used, the amount to be burned is first added to the balance of the owner then deducted from it, which ends in only the `totalSupply` value being reduced instead of both balance of the owner and `totalSupply`.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L163

## 3.3. Owner Privileges
 
### Severity: low

### Description

- Reopen sale even if the token to be sold limit is reached using `updateTradeStatus`.
- Change the value of the token limit to be sold `tokenPublicSale` as wished using `updatePublicSale`, making the process of reopening the sale possible.  
- Change token price as wished using`updateTokensPerEth`.
- Transfer all received ether to owner account;

The contract is managed manually by the owner which is not good for investors.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L181

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L185

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L175

## 3.4. Total Supply
 
### Severity: medium

### Description

The total distributed tokens value can be higher than `totalSupply` since `distributionFinished` is set to true after adding the new distributed tokens to the beneficiary. The last call to `distr` or to `doAirdrop` functions before setting `distributionFinished` to true, will allow to give or to buy an amount high enough to surpass the limited `totalSupply`.  

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L129

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L141

## 3.5. Token Distribution
 
### Severity: medium

### Description

Even if `finishDistribution` is called manually, owner can keep on airdropping tokens until `totalDistributed` will reach `totalSupply`. since none of `doAirdrop`, `adminClaimAirdrop` and `adminClaimAirdropMultiple` check if `distributionFinished` is set to true. 
Contract developers should explain the reason why the airdrop do not stop especially knowing that there is no dedicated funds for either airdrop or sales and the dedicated amount for sales can be airdropped if not completely sold.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L123

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L141

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L167

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L171

## 3.6. Length Check

### Severity: low

### Description

`adminClaimAirdropMultiple` amounts and addresses list length should be compared. 

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/master/contracts/tretchain.sol#L171

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```
## 3.8. Bug in the getTokens function  

### Severity: medium

### Description

The investor can buy more amount of tokens than restriction in value tokenPublicSale.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/6dff08db644c1fc79d89683ba7bd76e0c92311a3/contracts/tretchain.sol#L207-L216

## 3.9. Discrepancy with the ERC20 standard

### Severity: minor observation

### Description

According to the ERC20 standard, the variable decimals should be declared as uint8.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/6dff08db644c1fc79d89683ba7bd76e0c92311a3/contracts/tretchain.sol#L77

## 3.10. Extra checking.

### Severity: minor observation

### Description

The second require statement is unnecessary.

### Code snippet

https://github.com/tretchainvn/smart-contracts/blob/6dff08db644c1fc79d89683ba7bd76e0c92311a3/contracts/tretchain.sol#L201

### Recommendation

Those line may be deleted.

# 4. Conclusion

The audited smart contract is not safe to use. The highlighted issues should be fixed before deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/eab9da2c3d8e763c78f2cdc896a00560

https://gist.github.com/yuriy77k/608da407e264e050cd13219250c87604

https://gist.github.com/yuriy77k/d495cbf958e5c3cd2e07fefafdb8db11
