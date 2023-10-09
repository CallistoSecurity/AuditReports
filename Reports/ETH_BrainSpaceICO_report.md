# 1. Summary

[Brain Space ICO](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit hash:c031339b720445a1a9debbf1d720ac74f5740a69
- [ERC20.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/ERC20.sol).
- [IMPCrowdsale.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol).
- [IMPERIVMCoin.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPERIVMCoin.sol).
- [Lifecycle.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Lifecycle.sol).
- [Ownable.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Ownable.sol).
- [Rate.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Rate.sol).
- [SafeMath.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/SafeMath.sol).
- [StandardToken.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/StandardToken.sol).
- [Verification.sol](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Verification.sol).

# 3. Findings

In total, **11 issues** were reported including:

- 1 medium severity issues.

- 8 low severity issues.

- 2 minor observation.

## 3.1. Fallback Function

### Severity: medium

### Description

Any ether sent directly to the contract through the fallback function will not result in an automatic buy of tokens but instead is directly sent to the `initialOwner`. This mechanism is maybe meant to be as an offline sale agreement between owner and investors, however many users might misuse it since many ICO use the fallback as a direct buy function or simply send ether by mistake to the contract address.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L70-L72

## 3.2. Precision issue

### Severity: low

### Description

Solidity operates only with integers. Thus, variables multiplication should be done before the division in order to reduce the rounding errors.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L83

## 3.3. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to:

- manual token transfer;
- change rate, including zero;
- stop CrowdSale;
- change the hardcap - `requiredDollarAmount`.

The contract is managed manually by the owner which is not good for investors.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L117-L130

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L111-L115

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Lifecycle.sol#L64-L84

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L63

## 3.4. Check input value of change Rate.

### Severity: low

### Description

- There is necessary to check the input value of the `stringCourse` function for zero-value. In case of the argument is not specified, the user can pay money but not get tokens.
- Remove payable modifier. 

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L111

## 3.5. Ether To Tokens

### Severity: low

### Description

`ethersToTokens` function member of `Rate` contract, compute the equivalent value of tokens from a value in ether. The decimals value of ether has been omitted, meaning that the inputted ether value on `ethersToTokens` in not in wei but in ether.

- If an ether value is inputted the output of the function will be correct, however is someone wants to buy 1.2 ether worth of tokens the computation won't be possible since no decimals are taken into account in the computation.
- If a wei value is inputted the output will be wrong since the final value has to be divided by 10^18 to return the right answer.

Knowing that all ether value inside solidity like `msg.value` are in wei and not in ether, this function can create issues if it is used.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Rate.sol#L45-L52

## 3.6. State Variable Update

### Severity: low

### Description

Token price in usd cents and the minimum required amount in usd are directly linked with ETH/USD rate, token price in wei and the minimum required amount in wei as we can see in [`stringUpdate`](https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Rate.sol#L83#L88) function, however each time that `setUpConditions` is called either in `IMPCrowdsale` constructor or in `nextStage` function neither `stringUpdate` nor an equivalent logic is used to update the token price in wei or the minimum required amount in wei that are one of the principal state variable when `buyTokens` is called.

Please note that right after deploying the crowdsale contract `buyTokens` is accessible to any approved address for buy since `crowdsaleStage` will be equal to `Stages.Private`, meaning that `ETHUSDC`, `tokenWeiPrice` and `requiredWeiAmount` will be wrong.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Rate.sol#L83#L88

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L63

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Lifecycle.sol#L95

## 3.7. Missing event

### Severity: low

### Description

In the constructor of `IMPERIVMCoin` after adding the token supply to the owner a Transfer event should be emitted to let the token be fully complaint with ERC20 requirements.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPERIVMCoin.sol#L23#L27

## 3.8. Token Sold

### Severity: low

### Description

The requirement that is set to not allow the tokens sold during one of the stages to not be higher than the requirement is checked before calling `_postValidatePurchase` which means that the sold token for one of the stages can be higher than the allowed total supply percentage.

### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L83

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L103

## 3.9. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```

## 3.10. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.24. It is suggested to use the latest version and fix all compiler warnings that arise. 
Compiler version should be fixed to avoid any potential discrepancies in smart contract behavior caused by different versions of compiler.

## 3.11. `PercentLimits` array is not correct.

### Severity: minor observation

### Description

Percent limits array contain zero on four Crowdsale stages [10, 27, 53, 0]. In this case the crowdsale will be stopped on the four stage.
     
### Code snippet

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/Rate.sol#L33

https://github.com/IMPERIVM-TEAM/BrainSpace-ICO-Smart-Contracts/blob/c031339b720445a1a9debbf1d720ac74f5740a69/IMPCrowdsale.sol#L83

# 4. Conclusion

Many issues have been highlighted and should be taken into account by the developers and by the investors to be fully aware of all the risks.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/9a1348f5569bf832255e39c84d99fe79

https://gist.github.com/yuriy77k/e9733eb9b6de691ebec94007aab1f59c

https://gist.github.com/yuriy77k/5423b205c4e2fd1c6b930db6bc8f5cec
