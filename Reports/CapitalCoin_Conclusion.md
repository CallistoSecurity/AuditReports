# [Capital coin](https://github.com/capital-technologies/truffle-crowdsale) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The audited contracts contain multiple issues that have to be fixed.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

# 3.1. Minting Restriction

### Description

Following the website and the [crowdsale white paper](https://drive.google.com/file/d/1cJRG4xGptQuL4nZSt_ASSyx55hwHylGv/view) description, 5% of the tokens are allocated for bounty, 10% to the dev team and 85% to the public sale. The initial minting of the dev and bounty fund is done when calling `powerUpContract` function member of `CapitalTechCrowdsale`, After this function the team should not be able to mint extra tokens.

Even if the tokens ownership has to be changed to the ICO contract address to enable minting for the ICO address, a function is implemented that will allow the ICO contract owner to mint tokens also. The implemented `transferTokens` function member of `CapitalTechCrowdsale` do not support the white paper definition. 

### Code snippet

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalTechCrowdsale.sol#L236#L252

## 3.2. Misuse of Fallback function

### Description

The following contracts accept ether using a fallback function, but doesn't implement any function to withdraw it:

- `CapitalBountyDelivery` 
- `BountyVault` 
- `TeamVault`

### Code snippet

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalBountyDelivery.sol#L26#L27

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/BountyVault.sol#L18#L19

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/TeamVault.sol#L18#L19

## 3.3. It is possible for Owner to take away funds of investors, even if soft capitalization is not reached.

### Code snippet
* [CapitalTechCrowdsale.sol, Line 278](https://github.com/capital-technologies/truffle-crowdsale/blob/0586b0d150cd835159cfe3b10fb07f2573cde09d/contracts/CapitalTechCrowdsale.sol#L278)

* [RefundVault.sol, Line 47](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/c05918c3cc10d9394fd5fb95deb04036204ac896/contracts/crowdsale/distribution/utils/RefundVault.sol#L47)

### Description

Not achieving goal of soft capitalization implies not the possibility of implementing the project and the return of funds to investors. But it is possible for `CapitalTechCrowdsale` contract owner to take away funds of investors, even if soft capitalization is not reached. Because the owner has the opportunity to increase `callDistributed` variable by calling `transferTokens` function.

Steps to reproduce:

```solidity
/* 1 step: increasing `callDistributed` variable to turn `goalReached()` to `true`. */
function transferTokens(address _to, uint256 _amount) public onlyOwner {
  // ...
  callDistributed = callDistributed.add(_amount);
  callgDistributed = callgDistributed.add(_amount.mul(200));
  // ...
}

/* 2 step: finalizing the crowdsale */
function finalize() onlyOwner public {
  stage = stages.FINALIZED;
  finalization();
}

function finalization() internal {
  // ...
  if (goalReached()) {
    emit GoalReached(callSoftCap, callgSoftCap);
    vault.close();
  }
  // ...
}

/* RefundVault.sol: transfer the funds of investors to owner address */
function close() onlyOwner public {
  // ...
  wallet.transfer(this.balance);
}
```

### Recommendation

Only purchased tokens should be accounted for `goalReached()` result.


# 4. Low severity issues:

## 4.1. Ethereum To Token Conversion Error

### Description

`fiat_contract.USD(0)` return the value of 0.01 usd in wei, inside `getAmountForCurrentStage` function member of `CapitalTechCrowdsale` no reason is advanced why inside each condition the value `tokenPrice.mul(XXX)` is divided by `10**8` this will just truncate the value of the token in wei. 

When doing a conversion the value to be converted should be first multiplied than devided.

### Code snippet

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalTechCrowdsale.sol#L124#L140

### Recommendation

```
  function getAmountForCurrentStage(uint256 _amount) public view returns(uint256) {
    uint256 tokenPrice = fiat_contract.USD(0) // get the value of 0.01 usd in wei;
    
    if(stage == stages.PRIVATE_SALE) {
      tokenPrice = tokenPrice.mul(35); // token price set to be 0.35 usd (white paper definition)
    } else if(stage == stages.PRE_SALE) {
      tokenPrice = tokenPrice.mul(50);
    } else if(stage == stages.MAIN_SALE_1) {
      tokenPrice = tokenPrice.mul(70);
    } else if(stage == stages.MAIN_SALE_2) {
      tokenPrice = tokenPrice.mul(80);
    } else if(stage == stages.MAIN_SALE_3) {
      tokenPrice = tokenPrice.mul(90);
    } else if(stage == stages.MAIN_SALE_4) {
      tokenPrice = tokenPrice.mul(100);
    }
    return _amount.mul(10 ** 18).div(tokenPrice);
  }
```

## 4.2. Parameters Length Verification

### Description

Add a condition to `sendFirstBatch` and `sendSecondBatch` functions members of `CapitalBountyDelivery` to check if `_userWallet.length == call.length` and throw if not equal. otherwise in case of an unequal length users might receive a reward that is not them or zero reward.

### Code snippet

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalBountyDelivery.sol#L52

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalBountyDelivery.sol#L66

## 4.3. Minting End

### Description

`finalization` function member of `CapitalTechCrowdsale` contract, call `finishMinting` marked with `onlyOwner` modifier. `finalization` function can be called by a normal user if the next stage is stages.FINALIZED when he buys tokens making the transaction throw. however this issue will be applicable for the last investor only.

### Code snippet

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalTechCrowdsale.sol#L274

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalTechCrowdsale.sol#L267

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalTechCrowdsale.sol#L202

### Recommendation

Place `MintableToken(token_call).finishMinting()` and `MintableToken(token_callg).finishMinting()` inside `finalization` without calling `finishMinting` function, or mark `finishMinting` as internal function.

## 4.4. Last version of OpenZeppelin `MintableToken.sol` contract doesn't have `finishMinting()` function.

### Code snippet
* [CapitalTechCrowdsale.sol, Lines 268-269](https://github.com/capital-technologies/truffle-crowdsale/blob/0586b0d150cd835159cfe3b10fb07f2573cde09d/contracts/CapitalTechCrowdsale.sol#L268-L269)

### Description

Mintable token of OpenZeppelin solution in up to date version doesn't have `finishMinting()` function. The reason is described [here](https://github.com/OpenZeppelin/openzeppelin-solidity/issues/1348).

### Recommendation

Use the latest version of [this token](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/token/ERC20/ERC20Mintable.sol) and change the mechanism of stop the minting if this is required. Or use older version of this token, but `MintableToken.sol` not audited, because we do not know the version you were considering at the time of this contract development.

## 4.5. Most variables are set after `powerUpContract` call, and it is possible to call some functions when the crowdsale is not `powered_up`.

### Code snippet
* [CapitalTechCrowdsale.sol, Line 261](https://github.com/capital-technologies/truffle-crowdsale/blob/0586b0d150cd835159cfe3b10fb07f2573cde09d/contracts/CapitalTechCrowdsale.sol#L261)

### Description

For example, `goalReached` is `true` right after the contract is deployed, because `callSoftCap` is 0 in this moment.

### Recommendation

Check if the crowdsale is powered up in function where is needed.

## 4.6. Known vulnerabilities of ERC-20 token

### Code snippet
* [CALLToken.sol](https://github.com/capital-technologies/truffle-crowdsale/blob/0586b0d150cd835159cfe3b10fb07f2573cde09d/contracts/CALLToken.sol#L12)
* [CALLGToken.sol](https://github.com/capital-technologies/truffle-crowdsale/blob/0586b0d150cd835159cfe3b10fb07f2573cde09d/contracts/CALLGToken.sol#L12)

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4.7. Zero address checking required. 

### Description

In functions [`changeCreator`](https://github.com/capital-technologies/truffle-crowdsale/blob/757ef02fdcb7e8ad954d00c9af61956334a368cc/contracts/FiatContract.sol#L77) and [`changeSender`](https://github.com/capital-technologies/truffle-crowdsale/blob/757ef02fdcb7e8ad954d00c9af61956334a368cc/contracts/FiatContract.sol#L83) there are possibility to set creator or sender address to zero address.
Also in functions [`withdrawBounty`](https://github.com/capital-technologies/truffle-crowdsale/blob/757ef02fdcb7e8ad954d00c9af61956334a368cc/contracts/CapitalTechCrowdsale.sol#L101) and [`withdrawTeam`](https://github.com/capital-technologies/truffle-crowdsale/blob/757ef02fdcb7e8ad954d00c9af61956334a368cc/contracts/CapitalTechCrowdsale.sol#L105) there are no checking for zero address.

### Recommendation

Add zero address checking
```solidity
require(_creator != address(0));
```

# 5. Minor observation:

## 5.1. Naming Convention

### Description

Variable names should be intuitive, the input parameter `date` of `extendPeriod` function should be named `duration` for example, to avoid confusion.

### Code snippet

https://github.com/capital-technologies/truffle-crowdsale/blob/mainnet/contracts/CapitalTechCrowdsale.sol#L233#L235

## 5.2. Test values left. 

### Description

In [`powerUpContract`](https://github.com/capital-technologies/truffle-crowdsale/blob/757ef02fdcb7e8ad954d00c9af61956334a368cc/contracts/CapitalTechCrowdsale.sol#L60) there left test values, which should be changed before deploying to mainnet.


# Revealing audit reports:

https://gist.github.com/yuriy77k/3b1e35dd4d6b67fa18034b61903aa95f

https://gist.github.com/yuriy77k/9cd795514d90b48a62e3837cdb96dbec

https://gist.github.com/yuriy77k/972e019e4be71cd22ac06521d93f0f59
