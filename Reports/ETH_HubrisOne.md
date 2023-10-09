# 1. Summary

[HubrisOne](https://ropsten.etherscan.io/address/0x28cb78786638824e760adb0868935f0bf57d8872#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [HubrisOne.sol](https://ropsten.etherscan.io/address/0x28cb78786638824e760adb0868935f0bf57d8872#code)

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 1 low severity issues.

- 2 minor observation.

No critical security issues were found.

## 3.1. Owner Privileges

### Severity: Medium

### Description

The contract owner allow himself to:

* Block/Unblock transfer for all tokens.
* Whitelist/restrict address to allow/restrict transfer when the general suspension of transfer is applied.
* Evacuate Tokens at any time.
* Missing burn function after crowdsale.

Restrictive functions are allowed during a crowdsale, but upon completion, they are not acceptable. 
The contract is managed manually by the owner which is not good for investors.

### Code snippet

https://gist.github.com/yuriy77k/66a4051406483517cf661fa99179b6dc#file-hubrisone-sol-L312#L339

https://gist.github.com/yuriy77k/66a4051406483517cf661fa99179b6dc#file-hubrisone-sol-L434#L437

### Recommendation

Add time limitation for restrictive functions. Add auto burn function after complete crowdsale.

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

## 3.3. Extra checking.

### Severity: minor observation

### Description

Extra checking in 131, 182, 183 lines. SafeMath library checks it anyway.

### Code snippet

https://gist.github.com/yuriy77k/66a4051406483517cf661fa99179b6dc#file-hubrisone-sol-L131

https://gist.github.com/yuriy77k/66a4051406483517cf661fa99179b6dc#file-hubrisone-sol-L182

https://gist.github.com/yuriy77k/66a4051406483517cf661fa99179b6dc#file-hubrisone-sol-L183

### Recommendation

Those lines may be deleted.

## 3.4. Unused code.

### Severity: minor observation

### Description

The function `forwardFunds` is internal and can be called only from inside but there is no any usage of that function.

### Code snippet

https://gist.github.com/yuriy77k/66a4051406483517cf661fa99179b6dc#file-hubrisone-sol-L426-L428

### Recommendation

Those function may be deleted.

# 4. Conclusion

No critical vulnerabilities were detected, however users should be aware of the implemented restrictions and owner's rights.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/af047c2256da90ee496c5a9210a1a0fc

https://gist.github.com/yuriy77k/f78114d6f2c1ec0e2826bc99fa246962

https://gist.github.com/yuriy77k/96e4f640b1e1501c8b8d928754f70396
