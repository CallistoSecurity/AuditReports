# 1. Summary

[STARAMBA Token](https://github.com/staramba/com.staramba.token.STT) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

The STARAMBA Token is for the users of the VR world STARAMBA. spaces the currency for payments. The bigger STARAMBA.spaces the bigger the Token. For the company STARAMBA it's the payment split included in the smart contract to manage the license fee for our license partners.

# 2. In scope

- [RelocationToken.sol](https://github.com/staramba/com.staramba.token.STT/blob/master/RelocationToken.sol) github commit hash ef5e4316a3d495a0aadd303ad01f30c3e7834ca7.
- [SafeMath.sol](https://github.com/staramba/com.staramba.token.STT/blob/master/SafeMath.sol) github commit hash ef5e4316a3d495a0aadd303ad01f30c3e7834ca7.
- [StandardToken.sol](https://github.com/staramba/com.staramba.token.STT/blob/master/StandardToken.sol) github commit hash ef5e4316a3d495a0aadd303ad01f30c3e7834ca7.
- [StarambaToken.sol](https://github.com/staramba/com.staramba.token.STT/blob/master/StarambaToken.sol) github commit hash ef5e4316a3d495a0aadd303ad01f30c3e7834ca7.

# 3. Findings

In total, **5 issues** were reported including:

- 5 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)

* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.2. The vendors can "mint" any value of tokens for their own purposes.

### Severity: low

### Code snippet

- https://github.com/staramba/com.staramba.token.STT/blob/ef5e4316a3d495a0aadd303ad01f30c3e7834ca7/StarambaToken.sol#L201

- https://github.com/staramba/com.staramba.token.STT/blob/ef5e4316a3d495a0aadd303ad01f30c3e7834ca7/StarambaToken.sol#L222

### Description

The vendors can mint any value (within `TOKEN_CREATION_CAP`) of tokens for their own purposes. This is dangerous for investors.

## 3.3. When the burning of tokens, it is necessary to reduce the `TOKEN_CREATION_CAP`.

### Severity: low

### Code snippet

- https://github.com/staramba/com.staramba.token.STT/blob/ef5e4316a3d495a0aadd303ad01f30c3e7834ca7/StarambaToken.sol#L284

### Description

When the burning of tokens, it is necessary to reduce the `TOKEN_CREATION_CAP`, otherwise, they can again be minted by `deliverTokens` function.

## 3.4. The relocating is not secure process for investors.

### Severity: low

### Code snippet

- https://github.com/staramba/com.staramba.token.STT/blob/ef5e4316a3d495a0aadd303ad01f30c3e7834ca7/StarambaToken.sol#L151

### Description

The owners can implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

## 3.5. No checking for zero address.

### Severity: low

### Description

Transfer & transferFrom, approve functions do not prevent from sending tokens to address 0x0.

# 4. Conclusion

The contract has no critical issues, but the owner has a lot of power over contract and investors should know about it.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/867cd39fa187cb9f6130ac7f1999c16a

https://gist.github.com/yuriy77k/086642a6f3a582f9732599db8d48ae7e

https://gist.github.com/yuriy77k/b05e3114da128ce5868293cc1316564d
