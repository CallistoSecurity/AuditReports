# 1. Summary

[ProofOfCommunity](https://etherhub.io/addr/0x08f7039d36f99eedc3d8b02cbd19f854f7dddc4d) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

Audited smart contract is one kind of Ponzi scheme, where the first investors make a profit at the expense of subsequent ones. Ponzi scheme ("pyramid") where most investors lose their money.

# 2. In scope

- [ProofOfCommunity.sol](https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5)

# 3. Findings

In total, **5 issues** were reported including:

- 4 low severity issues.

- 1 minor observation.

No critical security issues were found.

## 3.1. Insufficient autonomy.

### Severity: low

### Code snippet

https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5#file-proofofcommunity-sol-L323#L328

### Description

The administrator of contract can manually disable Initial Stage and become a "whale", therefor contract has no "anti early whale" protection.

## 3.2. A small values of max quota variables for Ethereum Classic network.

### Severity: low

### Code snippet

* [ProofOfCommunity.sol, lines 141-142](https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5/4e881cdbb2038d2102187d17729c5b77ed592cef#file-proofofcommunity-sol-L141-L142)

### Description

The `ambassadorMaxPurchase_` variable is just 1 ETC or ~9 USD. And the `ambassadorQuota_` is ~180 USD. This is probably a small value for the "only ambassadors" stage. In this case this is not "anti early whale" solution.

## 3.3. `tokensToEthereum_` and `ethereumToTokens_` do not work with 100% accuracy.

### Severity: low

### Code snippet

* [ProofOfCommunity.sol, lines 619-647](https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5/4e881cdbb2038d2102187d17729c5b77ed592cef#file-proofofcommunity-sol-L619-L647)

* [ProofOfCommunity.sol, lines 654-676](https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5/4e881cdbb2038d2102187d17729c5b77ed592cef#file-proofofcommunity-sol-L654-L676)

### Description

If try to call `ethereumToTokens_` with 1 ether and the total supply equals 10000 tokens then we will get `7316283502240173144892*1e-18` tokens, but if we call `tokensToEthereum_` function with the same number of tokens we will get `0.9999` ether. These functions do not work with 100% accuracy.

### Recommendation

There is no explanation for this methods. It is hard to understand. Having a description of the formula it will be possible to give recommendations.

## 3.4. Known vulnerability of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5#file-proofofcommunity-sol-L291#L317

## 3.5. `buy` function always returns `0`.

### Severity: minor observation

### Code snippet

* [ProofOfCommunity.sol, line 193](https://gist.github.com/yuriy77k/49846b89b3804e2cc45fcb0f1e64f9f5/4e881cdbb2038d2102187d17729c5b77ed592cef#file-proofofcommunity-sol-L193)

### Description

The `buy` function doesn't have a return statement and always returns `0`.

### Recommendation

Use return statement.

# 4. Conclusion

The contract has some issues but it's not critical. Its safe for deployment, but there is high risk to lose money invested to Ponzi scheme.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/fb0efc15981e74a04725e003df9f99f3

https://gist.github.com/yuriy77k/d7f3aec38e4cbbd3692cf92ac24afa16

https://gist.github.com/yuriy77k/2a23eb12730ad8426a81ffeab7669560
