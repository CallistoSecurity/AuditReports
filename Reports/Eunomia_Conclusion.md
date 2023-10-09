# 1. Summary

[Eunomia](https://etherscan.io/address/0x0f612a09ead55bb81b6534e80ed5a21bf0a27b16#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Token description:

	Symbol      : ENTS
	Name        : EUNOMIA
	Total supply: 20,000,000,000
	Decimals    : 8
	Standard    : ERC20

# 2. In scope

- [EUNOMIA.sol](https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a)

# 3. Findings

In total, **6 issues** were reported including:

- 2 medium severity issues.

- 4 low severity issues.

## 3.1. ICO Time

### Severity: low

### Description

The ICO doesn't end following a fixed time but either when the `totalSupply` is reached or if the owner call `finishDistribution` function, this can impact the investors that expect a ROI after a certain period of time that is not guaranteed by the contract logic.

## 3.2. Token Distribution

### Severity: medium

### Description

It is possible to distribute a value of tokens higher than `totalSupply`:

- While the ICO phase is open (`distributionFinished` set to false), at any given time if an investor decides to buy a number of tokens higher than the remains tokens to be distributed, he will receive an amount that will make the circulating supply (`totalDistributed`) higher than the `totalSupply`, only after distributing the tokens that `distributionFinished` will be set to true. The implemented buy function do not revert the transaction if the circulating supply exceed the `totalSupply`.

- Contract owner can make an airdrop to a specific address where the amount of tokens can make the circulating supply exceed the `totalSupply`. The implemented airdrop logic do not revert the transaction if the circulating supply (`totalDistributed`) exceed the `totalSupply`.

### Code snippet

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L168#L170

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L172#L189

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L155#L161

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L137#L153


### Recommendation

```solidity
require( totalDistributed.add(_amount) <= totalSupply );
```

## 3.3. Token AirDrop

### Severity: medium

### Description

`adminClaimAirdrop` and `adminClaimAirdropMultiple` can be used in a wrong distribution pattern:

After setting `distributionFinished` state variable to true, both above mentioned functions can be called since `canDistr` modifier is not used, which means that if `finishDistribution()` function is called and `totalDistributed` is less than `totalSupply` then the contract owner can still use the airdrop logic and be able to airdrop any amount of tokens even higher than the `totalSupply` (please note that issue 3.2 will also be applicable in this case where only the owner can take advantage of it).

### Code Snippet

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L155#L161

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L137#L153

### Recommendation

```solidity
require( totalDistributed.add(_amount) <= totalSupply );
```

## 3.4. Approve Logic

### Severity: low

### Description

The implemented approve logic do not solve totally the ERC20 double withdrawal attack since a racing condition can happen when the user will try to reset the allowed value to zero, then he has to check his own balance before allowing a new value.

## 3.5. Token Price Update

### Severity: low

### Description

`updateTokensPerEth` allow the owner to set any given price even after the ICO start. Investors should check the price before buying the tokens.

### Code Snippet

https://gist.github.com/yuriy77k/b4bd4eae985e87a0f87f37f5f3bd814a#file-eunomia-sol-L163#L166

## 3.6. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4. Conclusion

Many issues have been highlighted, the developers should redesign the contract logic.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/c4333ff1c8ab5631d5e5e352c4593186

https://gist.github.com/yuriy77k/f543d12ed9734b3021acfdee173ec08d

https://gist.github.com/yuriy77k/37709fedfd613fb740391e43de8cd120
