# 1. Summary

[Enect](https://etherscan.io/address/0x27ce70a584c085712cd42f20181237178ddc5a45#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ENECT.sol](https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol)

# 3. Findings

In total, **5 issues** were reported including:

- 2 medium severity issues.

- 3 low severity issues.

No critical security issues were found.

## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

* It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
* Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)


## 3.2. The `acceptOwnership` function always returns `false`.

### Severity: low

### Description

This function is marked as `returns(bool)`, but doesn't have a `return` statement and always returns `false` by default. This can disrupt the work of a dApp.

### Code snippet

* https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol-L60

## 3.3. The owner can prohibit any user to transfer funds.

### Severity: medium

### Description

The owner or a hacker (if the owner's private key will be compromised) can freeze any user at any time, which will make it impossible to transfer funds. This is dangerous for investors and for exchanges too. 

### Code snippet

* https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol-L277

* https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol-L165

## 3.4. The owner can mint any value of tokens he wants.

### Severity: medium

### Code snippet

* https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol-L345

### Description

The owner or a hacker (if the owner's private key will be compromised) can mint any value of tokens he wants. The token can become worthless very quickly. This is dangerous for investors.

## 3.5. There is no transfer of funds when changing the owner.

### Severity: low

### Description

When a contract is created, the initial supply is on the owner's balance, but when the owner's rights are transferred to the new address, the supply is not transferred. Unallocated tokens will be on the balance of the previous owner.

### Code snippet

* https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol-L60

* https://gist.github.com/yuriy77k/3a798a1c944f55b7be61dc203e4fd234#file-enect-sol-L152

# 4. Conclusion

The contract contain some medium severity issues that need to be fixed.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7bab15b17ce0202dd388328a6067e2ca

https://gist.github.com/yuriy77k/53ac3f90c5847a1845d9892467779462

https://gist.github.com/yuriy77k/88c7ba3c27f7041f9a5958068a10e57a
