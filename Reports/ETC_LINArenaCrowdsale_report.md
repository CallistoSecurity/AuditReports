# 1. Summary

[LIN Arena Crowdsale](https://etherscan.io/address/0x16a34f5f4de683996846c911891f612445b346eb) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

[DskCrowdSale.sol](https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol)

# 3. Findings

In total, **8 issues** were reported including:

- 4 medium severity issues.

- 3 low severity issues.

- 1 minor observation.

## 3.1. Block Gas Limit

### Severity: medium

### Description

`DskTokenToInvestors` function member of `DskCrowdSale` contain a loop that iterates following the size of `InverstorList` that is equal to `InvestorNum` if there is a high enough number of addresses that invested in the token ICO, the function will probably throw at each call since they won't be enough of gas to cover the transaction fee since block gas limit will be reached. Please note that there is no function that cover this situation to send the ether back to the investors and the ether has to be first sent to the owner.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L662#L694

### Recommendation

"The recommended method of sending funds after an effect is using the withdrawal pattern" as per solidity documentation. `Inverstors` mapping can be used directly to lookup to the investor structure of each users, a function that can be called by each investor to withdraw his token is mandatory. 

## 3.2. ICO Remaining Supply

### Severity: medium

### Description

- After the ICO deadline the remaining supply can be added by the contract owner to his address by calling `DskToOwner` function member of `DskCrowdSale`, check below:

```
        address RequestAddress = msg.sender;
        Inverstors[RequestAddress].DskTokenAmount =  Inverstors[RequestAddress].DskTokenAmount.add(ICOSupply);
```
Following the general adopted ICO rules the remaining supply should be burned and not sent to a different address.
- In order for the owner to withdraw the tokens to his address the msg.sender should be included in `InverstorList` by either investing previously or by being one of the team addresses:

```
    address internal DonscoinOwner = 0x100eAc5b425C1e2527ee55ecdEF2EA2DfA4F904C;  // Set a DonscoinOwner's address
    address internal DonscoinFree =  0x026A175C90118a958E854E1972D72192459E4ED8;  // Set a DonscoinFree's address
```
Token withdrawal functions can be used later to transfer the tokens.

- Please note that the owner address can be different than the team addresses and that the investors can be deceived since this amount of tokens was not intended to be given for free to any address.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L647#L654

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L246#L247

## 3.3. Burn Function

### Severity: medium

### Description

`burn` function do not burn tokens from `Balance[msg.sender]` (`msg.sender` can only be the contract owner) but reduce the value of `ICOSupply` only.
- This issue can be acceptable at some levels since any value remaining in `ICOSupply` will be the undistributed tokens, however since `DskToOwner` is also implemented making this two functions contradictory. developers have to explain their intentions.
- In the case if `DonscoinOwner` is also the contract owner address, the 50% received coins will not never be burned since the owner balance cannot be reduced.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L633#L645

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L243

## 3.4. Owner Privileges

### Severity: medium

### Description

The owner allows himself to:

- Pause/Unpause token transfers at any moment, even `balanceOf` function is not accessible directly when the token contract is paused.
- 50% of the total token supply is allowed to `DonscoinOwner` and 20% to `DonscoinFree`, the total amount hold by the team represent the majority of the project tokens and can be a risk for the investors.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L174

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L202

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L202

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L83#L121

## 3.5. ICO Phases

### Severity: low

### Description

The sale phases are differentiated following the period as it can be seen below:
```
        if(CurrentTime<PrivateSaleEndTime) {
            rate = Private_rate;
        } else if(CurrentTime<PreSaleEndTime) {
            rate = Pre_rate;
        } else {
            rate = Public;
        }
```
Most ICO uses phases with a dedicated sale period and different fund not a unified fund like in this case, this means that the total `ICOSupply` can be sold  before `PrivateSaleEndTime`, the team should take the rate of the private sale into account in this case. 

The worst case scenario is where the amount of ether that will be collected if all the tokens are sold on the private sale will not reach the minimum requirement to run the project to an end.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L488#L500

## 3.6. HardCap

### Severity: low

### Description

In order for `HardCap` to be reached `ICOSupply` should be zero since `HardCap` is initialized to be equal to `ICOSupply`, but in order for the following condition to be true `ICOSupply` should be higher than zero, which means that `CheckHardCap` function will never change the value of `HardCapReached` to true:

```
        ICOSupply = ICOSupply.sub(NumDskToken); // Decrease in ICOSupply quantity
        
        if(ICOSupply > 0) {     
            Inverstors[RequestAddress].DskTokenAmount =  Inverstors[RequestAddress].DskTokenAmount.add(NumDskToken);
            SaleAmountDSK = SaleAmountDSK.add(NumDskToken); // Total amount of dsk tokens sold
            CheckHardCap(); // Execute hard cap check function
            CheckSoftCap(); // Execute soft cap check function
            InverstorList[InvestorNum] = RequestAddress;    // Assign the investor address to the current index
            InvestorNum++;  // Increase number of investors
            emit SuccessInvestor(msg.sender, msg.value);    // Investor Information event print
        } else {
            revert();   // If ICOSupply is less than 0, revert
        }
```

The impact of this issue is almost null since the deadline mechanism is the most important, but the logical error will alway cause the fallback function to be open since `SaleClosed` won't be set to true. 

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L505#L529

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L536#L550

## 3.7. Required check for an `0x0` address

### Severity: low

### Description

It is possible to send tokens to 0x0 address by accidentally.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L185

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L212

#### Recommendation

1. Use condition like `require(_to != address(0))`.

2. Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.8. Event issues

### Severity: minor observation

### Description

`CheckHardCap` function emits `SuccessSoftCap` event and `CheckSoftCap` function emits `SuccessHardCap` event. It should be the other way around.

### Code snippet

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L546

https://gist.github.com/yuriy77k/abc065a1b27e711659c83a11ba55ede4#file-dskcrowdsale-sol-L564

# 4. Conclusion

The audited contract is not safe for deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/56592ad0662f595f905c114507d16189

https://gist.github.com/yuriy77k/47ceb8e650da5b1db3cb2880215bf96a

https://gist.github.com/yuriy77k/098d9530fe860071ddc760aacc9e5ff5
