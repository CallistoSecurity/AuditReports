# [SUAPP](https://docs.google.com/document/d/1wSAGWYC9LnEZDoKpjPg97vxLSYSklsxwo3HjAFCaziw/edit?usp=sharing) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The Token contract is safe to be deployed. But the token description do not fully match the implementation - token is **not Burnable**.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

No Medium severity issues


# 4. Low severity issues:

## 4.1. Known vulnerabilities of ERC-20 token

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 4.2. Token Burnability 

### Description

Following the token description provided by the developers SUAPP token should be burnable.
`SUAPPToken` does not inherit from `BurnableToken`, therefore the token description is not valid since no burn function is implemented or inherited from `BurnableToken`.

### Code snippet

https://gist.github.com/RideSolo/63a226868ca720f60b320cc4032728c9#file-suapp-sol-L108

https://gist.github.com/RideSolo/63a226868ca720f60b320cc4032728c9#file-suapp-sol-L335


# 5. Minor observation:

## 5.1. Reclaim Token 

### Description

The description of `reclaimToken` function is unclear, the implemented function allows any token with a certain value that is sent to SUAPP token contract to be transfered to SUAPP contract owner.

### Code snippet

https://gist.github.com/RideSolo/63a226868ca720f60b320cc4032728c9#file-suapp-sol-L328#L331

## 5.2. Extra checking

### Description

Extra checking in [88](https://gist.github.com/yuriy77k/523a8833e7be851d12c576c490c019c1#file-suapp-sol-L88), [117](https://gist.github.com/yuriy77k/523a8833e7be851d12c576c490c019c1#file-suapp-sol-L117), [160](https://gist.github.com/yuriy77k/523a8833e7be851d12c576c490c019c1#file-suapp-sol-L160), [161](https://gist.github.com/yuriy77k/523a8833e7be851d12c576c490c019c1#file-suapp-sol-L161) lines. SafeMath library checks it anyway.

### Recommendation

Those lines may be deleted.


# Revealing audit reports:

https://gist.github.com/yuriy77k/0b2df5d5550ae664e3ba820324680016

https://gist.github.com/yuriy77k/b6b6fe245aed49bff8758a96da930f89

https://gist.github.com/yuriy77k/946b1774955e11f5116e39c8f476b855
