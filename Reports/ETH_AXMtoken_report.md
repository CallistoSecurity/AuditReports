# AXM Token Security Audit Report

# 1. Summary

[AXM Token](https://github.com/mahamihirion/axmtoken) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> To provide cross border payments for people of NorthEast India.

https://assamtoken.xyz/

# 2. In scope

Commit hash: `8ac50f805184bade0fb9470aa170e455a254e6f8`

1. [axm.sol](https://github.com/mahamihirion/axmtoken/blob/8ac50f805184bade0fb9470aa170e455a254e6f8/axm.sol)

# 3. Findings

In total, **5 issues** were reported including:

- 3 medium severity issues.

- 1 low severity issues.

- 1 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Wrong `Transfer` event call

### Severity: medium

### Description

`Transfer` event should be emitted only for tokens transfers. But in this case it will be emitted when the transfer of ether. dApps will interpret this as a token transfer and this can lead to loss of funds or incorrect application behavior.

### Code snippet

https://github.com/mahamihirion/axmtoken/blob/8ac50f805184bade0fb9470aa170e455a254e6f8/axm.sol#L288

## 3.3. After the end of ICO, the Total Supply may be incorrect

### Severity: medium

### Description

After the end of ICO, remaining tokens will be send (minted) to the owner. But at the same time the code misses the increase of the `totalSupply_`. Also `Transfer` event is missed.
Following the general adopted ICO rules the remaining supply should be burned and not sent to a different address.

### Code snippet

https://github.com/mahamihirion/axmtoken/blob/8ac50f805184bade0fb9470aa170e455a254e6f8/axm.sol#L310

## 3.4. Owner privileges

### Severity: owner privileges

### Description

After each purchase of tokens, the owner receives the ether.

### Code snippet

https://github.com/mahamihirion/axmtoken/blob/8ac50f805184bade0fb9470aa170e455a254e6f8/axm.sol#L294

### Recommendation

Funds must be received by the owner only after the ICO finalization.

## 3.5. Wrong calculation

### Severity: medium

### Description

To avoid losing accuracy multiply operation should be done before division.
In this implementation, if the investor wants to purchase the remaining tokens for a larger amount he could get up to 99 tokens free.

### Code snippet

https://github.com/mahamihirion/axmtoken/blob/8ac50f805184bade0fb9470aa170e455a254e6f8/axm.sol#L276-L291

### Recommendation

Replace [line 278](https://github.com/mahamihirion/axmtoken/blob/8ac50f805184bade0fb9470aa170e455a254e6f8/axm.sol#L278) by following:
```
uint256 newWei = newTokens.mul(1 ether).div(basePrice);
```

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/3a4fdd12d7ff987fb25a3bccba71c8c0

https://gist.github.com/yuriy77k/bc828af53b91334d42579395ead78615

https://gist.github.com/yuriy77k/5a0e99a5ff23723b73ee841227ec6a80
