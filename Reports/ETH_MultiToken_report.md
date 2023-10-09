# 1. Summary

[MultiToken](https://github.com/multitoken/MultiToken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [AbstractDeployer.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/AbstractDeployer.sol) github commit hash 94e3dedb66b2f5deeb405eefbf9ff77683b9a602.
- [BasicMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/BasicMultiToken.sol) github commit hash acb9a0f58a5b5b629e06f9a8f09b1fd635ad3682.
- [FeeBasicMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/FeeBasicMultiToken.sol) github commit hash 9d194d32e640795e0dacca14256b7334484a877a.
- [FeeFundMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/FeeFundMultiToken.sol) github commit hash 0fc0508282077dcbb5b12b931d7c9136f251537d.
- [FeeMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/FeeMultiToken.sol) github commit hash 9d194d32e640795e0dacca14256b7334484a877a.
- [FundMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/FundMultiToken.sol) github commit hash acb9a0f58a5b5b629e06f9a8f09b1fd635ad3682.
- [MultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/MultiToken.sol) github commit hash acb9a0f58a5b5b629e06f9a8f09b1fd635ad3682.
- [RemoteToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/RemoteToken.sol) github commit hash 6344d61c88a6993879c358f4efe320e990b62900.
- [CheckedERC20.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/ext/CheckedERC20.sol) github commit hash 5b262041c24dbe23b4183cc96751888b56832913.
- [ERC1003Token.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/ext/ERC1003Token.sol) github commit hash 1d9a9e0893dd63c382e056469833c7b7743413a3.
- [EtherToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/ext/EtherToken.sol) github commit hash 1d9a9e0893dd63c382e056469833c7b7743413a3.
- [ExternalCall.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/ext/ExternalCall.sol) github commit hash aa19a65016d210a78fad30a2c00c92264e1c0855.
- [AstraBasicMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/implementation/AstraBasicMultiToken.sol) github commit hash bff622c0d56c2237fdcfe0ed3527c3eb14427972.
- [AstraMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/implementation/AstraMultiToken.sol) github commit hash 762d9068dfe84b156bf4932327f84ebcdd78c6c1.
- [EOSToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/implementation/EOSToken.sol) github commit hash d32fde2e4625e3ece7016d71417877b89731026f.
- [AstraBasicMultiTokenDeployer.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/implementation/deployers/AstraBasicMultiTokenDeployer.sol) github commit hash bff622c0d56c2237fdcfe0ed3527c3eb14427972.
- [AstraMultiTokenDeployer.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/implementation/deployers/AstraMultiTokenDeployer.sol) github commit hash bff622c0d56c2237fdcfe0ed3527c3eb14427972.
- [IBasicMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/interface/IBasicMultiToken.sol) github commit hash 1226cc6f5fb33023e8f7562e527203cced13c68d.
- [IFundMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/interface/IFundMultiToken.sol) github commit hash 1226cc6f5fb33023e8f7562e527203cced13c68d.
- [IMultiToken.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/interface/IMultiToken.sol) github commit hash 1226cc6f5fb33023e8f7562e527203cced13c68d.
- [IMultiTokenInfo.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/interface/IMultiTokenInfo.sol) github commit hash 1226cc6f5fb33023e8f7562e527203cced13c68d.
- [MultiBuyer.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/network/MultiBuyer.sol) github commit hash aa19a65016d210a78fad30a2c00c92264e1c0855.
- [MultiChanger.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/network/MultiChanger.sol) github commit hash 683444a29300273d7cd3db73cd52ebb137f8739b.
- [MultiSeller.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/network/MultiSeller.sol) github commit hash aa19a65016d210a78fad30a2c00c92264e1c0855.
- [MultiShopper.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/network/MultiShopper.sol) github commit hash aa19a65016d210a78fad30a2c00c92264e1c0855.
- [MultiTokenInfo.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/network/MultiTokenInfo.sol) github commit hash 5b262041c24dbe23b4183cc96751888b56832913.
- [MultiTokenNetwork.sol](https://github.com/multitoken/MultiToken/blob/master/contracts/network/MultiTokenNetwork.sol) github commit hash 0fc0508282077dcbb5b12b931d7c9136f251537d.

# 3. Findings

In total, **15 issues** were reported including:

- 4 high severity issues.

- 7 medium severity issues.

- 2 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Possible for first bundle to get more out than malicious user deposited.

### Severity: medium

### Description

It is possible to add the same token several times for MultiToken. In this case the maker of first bundle will be benefit. For example, the contract is init with two instances of BNB token as `_tokens` and someone make `bundleFirstTokens()` with `amount = 100` and `tokenAmounts = [100, 100]`. In this case user will pay 200 BNB and get 100 MultiTokens on the balance. But second user who make bundle will pay 400 BNB for 100 MultiTokens.
 
### Code snippet

https://github.com/multitoken/MultiToken/blob/9d194d32e640795e0dacca14256b7334484a877a/contracts/BasicMultiToken.sol#L49

https://github.com/multitoken/MultiToken/blob/9d194d32e640795e0dacca14256b7334484a877a/contracts/BasicMultiToken.sol#L79

https://github.com/multitoken/MultiToken/blob/9d194d32e640795e0dacca14256b7334484a877a/contracts/BasicMultiToken.sol#L72

### Recommendation

Prohibit the use the same tokens several times.

## 3.2. Bundled Token Swap

### Severity: medium

### Description

`change` function member of `MultiToken` allows any address to provide a token to the contract using `approve` to be swapped to another token and sent to it, both tokens addresses should be contained in the `_tokens` array. Knowing that the weights are set when initializing the contract and if `_changesEnabled` is set to true, anyone can take advantage of this implementation to swap a token to a more lucrative one and all the bundled token holders will be impacted since it modifies every wallet directly. 

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/MultiToken.sol#L58#L67

## 3.3. Token Bundle/Unbundle

### Severity: medium

### Description

`_tokens` is the allowed list of tokens to be bundled/Unbundled and is initialized in `BasicMultiToken` contract using the `init` function. Please note that the tokens addresses are not crosschecked to avoid duplicated addresses inside the init function, however inside `_unbundle` function `sometokens` list that is equal to `_tokens` list is checked for duplicate addresses and require that the addresses are different in the contrary of `_bundle` function that bundle the tokens directly and transfer the equivalent amount of the contract without checking.

In conclusion the above described issue may result in tokens definitively frozen inside the contract since they can be bundled but not unbundled if one token address is duplicated.

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/BasicMultiToken.sol#L132#L135

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/BasicMultiToken.sol#L108#L121

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/BasicMultiToken.sol#L39#L52

## 3.4. Weights Function

### Severity: medium

### Description

`weights` function member of `FundMultiToken` contain multiple line that aren't used :

- In the case if `_nextWeightStartBlock` is equal to zero `weights` function will keep calling it self until out of gas of 1024 calls depth limit is reached, check [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L90#L92).
- multiple line of code inside the function it self are useless (they do not change any state variable or return any value), check [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L90#L97).

`weights` function is called from many other function possibly making all of them obsolete and wrong (developers should explain their intentions and add more comment to the code).

If the value of `_nextWeightStartBlock` is different than zero then the function will return `_nextWeights[token]`.

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L90#L92

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L90#L97

## 3.5. Return Computation

### Severity: minor observation

### Description

1) `getReturn` function member of `FundMultiToken`, calls `linearInterpolation` multiple times:
```
            scaledFromWeight = linearInterpolation(weights(fromToken), _nextWeights[fromToken], blockProgress, _nextWeightBlockDelay);
            scaledToWeight = linearInterpolation(weights(toToken), _nextWeights[toToken], blockProgress, _nextWeightBlockDelay);
            scaledMinWeight = linearInterpolation(_minimalWeight, _nextMinimalWeight, blockProgress, _nextWeightBlockDelay);
```
As described in the previous issue `weights` function returns `_nextWeights[token]` as you can see [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L98), so basically the `linearInterpolation` function will always get the same two points to interpolate between them making it useless to the code, due to missing comment and explanation about the role of each function the auditor cannot provide any recommendation to fix this issue.

2) The formula used [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L154#L155) to compute the final returned value cannot be understood and the developers demonstrate it. 

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L147#L149

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L154#L155

## 3.6. Obsolete Lock Mechanism

### Severity: minor observation

### Description

The function  [`getReturn`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L137) member of `FundMultiToken` does not call [`getReturn`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L49) function member of`LockableMultiTokenMixin` or implement the locking mechanism making the implemented lock mechanism obsolete. However `change` function locking is still undisrupted and there is no direct impact for this issue, any future implementation should take this issue into account.
 
### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L49

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FundMultiToken.sol#L137

## 3.7. Referral Fee Transfer

### Severity: medium

### Description

[`change`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FeeMultiToken.sol#L38) function member of `FeeMultiToken` calls [`changeWithRef`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FeeMultiToken.sol#L42) member of the same contract by setting `ref` address to 0x0 address, knowing that `changeWithRef` will transfer the referral amount to the `ref` address and that most tokens do not allow transfer to address 0x0 the function execution will most likely throw or transfer the referral fee to 0x0 address.
 
### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FeeMultiToken.sol#L38

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FeeMultiToken.sol#L42

## 3.8. Referral Fee Balance

### Severity: medium

### Description

No dedicated referral balance is dedicated for the referral function `changeWithRef` member of `FeeMultiToken` contract, as you can see [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FeeMultiToken.sol#L44#48) the referral fee is computed and sent from the contract address balance directly, which means that every users who bundled his tokens will see his real tokens balances reduced.

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/FeeMultiToken.sol#L44#48

## 3.9. MultiToken Deployment

### Severity: medium

### Description

`deploy` function member of `MultiTokenNetwork` should be only accessible for `MultiTokenNetwork` since any address deploying a `MultiToken` contract using deploy won't be the owner of the newly created contract but the owner of `MultiTokenNetwork` contract will be. As you can see [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/AbstractDeployer.sol#L11) the ownership is transferred to the `msg.sender` which is the `MultiTokenNetwork` address. 

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiTokenNetwork.sol#L84#L88

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/AbstractDeployer.sol#L11

## 3.10. Direct Contract Asset Manipulation (`MultiShopper` contract)

### Severity: High

### Description

Multiple functions implemented on `MultiShopper` allow any address to handle Ether, tokens and `EtherToken`:

- [`sendEthValue`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L65) allows any address to transfer ether from the contract to any other address or call any contract. 
- [`sendEthProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L70) same as `sendEthValue` but the ether sent will be in proportion with the contract balance and the function parameters values.
- [`approveTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L76) allow `amount` of tokens to a target address then make a call to it using `data`.
- [`approveTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L85) same as `approveTokenAmount` but the `amount` is in proportion to the contract token balance and the function parameters values.
- [`transferTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L95) transfers `amount` of tokens to `target` address then make a call to `target` using `data` if `data` length is higher than 0.
- [`transferTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L103) same as `transferTokenAmount` but the `amount` is in proportion to the contract token balance and the function parameters values.
- [`transferTokenProportionToOrigin`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L112) same as `transferTokenProportion` but the target is `tx.origin`.
- [`multitokenChangeAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L120) approves the total contract balance of `fromToken` to a `MultiToken` address and then change `amount` using `change` to `toToken`.
- [`multitokenChangeProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L127) same as `multitokenChangeAmount` but the `amount` is in proportion to the contract `fromToken` balance and the function parameters values.
- [`withdrawEtherTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L134) withdraws `amount` of `etherToken` from the contract token balance to the contract ether balance, which means that the equivalent ether is sent back to the contract.
- [`withdrawEtherTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L138) same as `withdrawEtherTokenAmount` but the `amount` is in proportion to the contract `etherToken` balance and the function parameters values.
- [`bancorSendEthValue`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L145) call bancor api (`convert` function) using `value` of ether.
- [`bancorSendEthProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L149) same as `bancorSendEthValue` but the `value` is in proportion to the contract ether balance and the function parameters values.
- [`bancorApproveTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L154) approves the total amount of `path[0]` token from the contract to the bancor contract then call bancor api using `claimAndConvert`.
- [`bancorApproveTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L161) same as `bancorApproveTokenAmount` but the value approved is in proportion to `MultiShopper` contract `path[0]` token balance and to the function parameters values.
- [`bancorTransferTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L169) transfers `MultiShopper` `path[0]` token balance to bancor contract then call bancor api using `convert`. 
- [`bancorTransferTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L174) same as `bancorTransferTokenAmount` but the value approved is in proportion to `MultiShopper` contract `path[0]` token balance and to the function parameters values.
- [`bancorAlreadyTransferedTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L180) convert an already transfered token to bancor using their api.
- [`bancorAlreadyTransferedTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L184) same as `bancorAlreadyTransferedTokenAmount` but with a proportional value.
- [`kyberSendEthProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L191) trade between ether sent from the contract to kyber api `trade` function to another token.
- [`kyberApproveTokenAmount`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L204) trade between `fromToken` approved from `MultiShopper` to `kyber` contract to `toToken`.
- [`kyberApproveTokenProportion`](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiShopper.sol#L219) same as `kyberApproveTokenAmount` but the traded value is proportional.

Please not that with any address and a good combination of the previously explained function since there is no restriction, any asset sent to `MultiShopper` contract address can be stolen. 
All contract that inherit from `MultiShopper` contract are applicable for this issues (`MultiSeller`, `MultiBuyer`).

## 3.11. Direct Contract Asset Manipulation (`MultiChanger` contract)

### Severity: High

### Description

Multiple functions implemented on `MultiChanger` allow any address to handle Ether, tokens and `EtherToken`:

- ['sendEthValue'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L33) allows any address to transfer ether from the contract to any other address or call any contract.
- ['sendEthProportion'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L38) same as `sendEthValue` but the ether sent will be in proportion with the contract balance and the function parameters values.
- ['depositEtherTokenAmount'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L46) allows to wrap `amount` of ether into `etherToken` token. 
- ['depositEtherTokenProportion'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L50) same as `depositEtherTokenAmount` but the ether wrapped will be in proportion with the contract balance and the function parameters values.
- ['withdrawEtherTokenAmount'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L55) withdraws `amount` of `etherToken` from the contract token balance to the contract ether balance, which means that the equivalent ether is sent back to the contract.
- ['withdrawEtherTokenProportion'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L59) same as `withdrawEtherTokenAmount` but the `amount` is in proportion to the contract `etherToken` balance and the function parameters values.
- ['transferTokenAmount'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L66) transfers `amount` of tokens to `target` address.
- ['transferTokenProportion'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L66) same as `transferTokenAmount` but the `amount` is in proportion to the contract `etherToken` balance and the function parameters values.

- ['transferFromTokenAmount'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L75) transfer an approved token from `tx.origin` to the contract address.
- ['transferFromTokenProportion'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L80) same as `transferFromTokenAmount` but the value is proportional to `MultiChanger` contract token balance and the function params values (please note that instead of `tx.orign`, `this` is used when computing amount which is a mistake).
- ['multitokenChangeAmount'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L88) approves the total contract balance of `fromToken` to a `MultiToken` address and then change `amount` using `change` to `toToken`.
- ['multitokenChangeProportion'](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiChanger.sol#L95) same as `multitokenChangeAmount` but the `amount` is in proportion to the contract `fromToken` balance and the function parameters values.

Please not that with any address and a good combination of the previously explained function since there is no restriction, any asset sent to `MultiChanger` contract address can be stolen. 

## 3.12. Sell Token

### Severity: High

### Description

`sell` function member of `MultiSeller` allows a user to sell its `mtkn` token holding to the contract, however after the execution of many calls inside the function the whole ether balance of the contract is sent to the `to` address specified in the parameters.

Please note that `change` function will make external calls to `MultiSeller` or more precisely to the inherited function from `MultiShopper` toward which we cannot define the consequences.

Same issue is applicable for `sellForOrigin` function member of `MultiSeller` contract.

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiSeller.sol#L36#L49

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiSeller.sol#L47

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiSeller.sol#L19#L34

## 3.13. Buy Token

### Severity: High

### Description

1) `buy` function member of `MultiBuyer` require a user in order to buy a certain amount of `mtkn` token to either `transfer` or `approve` the required minimum token balances to the contract first.
Please note that `change` can be used to call inherited functions from `MultiShopper`, depending the logic needed.

After analyzing all the function at least a token transfer or approval has to be done before that buy function can be used, meaning that users assets will be at risk like demonstrated in previous issues.

2) following the naming of the function `buy` any user should buy `mtkn` tokens but instead the main operation that is done is bundling the tokens required by `mtkn` token that were transfered or approved to the contract. As you can see [here](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiBuyer.sol#L39#41) the ether sent through the buy function is sent back to the `msg.sender`.

3) same 1,2 issues can be applied for `buyFirstTokens` except that the bunding is the first to be processed by `mtkn` contracts.

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiBuyer.sol#L12#L48

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/network/MultiBuyer.sol#L50#L82

## 3.14. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Recommendation

Add into a function `transfer(address _to, ... )` following code:
```solidity
require( _to != address(this) );
```

## 3.15. No possibility of enabling changes for admins

### Severity: low

### Description

The admin can only [disable changes](https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/MultiToken.sol#L71), but there is no possibility of enabling it in case of having troubles.

### Code snippet

https://github.com/multitoken/MultiToken/tree/9d194d32e640795e0dacca14256b7334484a877a/contracts/MultiToken.sol#L71

# 4. Conclusion

The audited project is unsafe for deployment. Users assets are at critical risks.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5eee6c425aaf78b29bc0d22bc34610b5

https://gist.github.com/yuriy77k/e5b8d4ccfa29dff933e79c2befbe9fde

https://gist.github.com/yuriy77k/92db3cbf666a3accc299dac6df34bd12
