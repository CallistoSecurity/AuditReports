# 1. Summary

[V-ID](https://github.com/V-ID/V-ID-Token) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [V-IDT-commented.sol](https://github.com/V-ID/V-ID-Token/blob/master/V-IDT-commented.sol) github commit hash d17efaba1080a1c17d28fc67e3f7ab1a4a0735a4.
- [V-IDT.sol](https://github.com/V-ID/V-ID-Token/blob/master/V-IDT.sol) github commit hash c3e666878ac55fbad3f138e999f80ad0ab7c0cae.

# 3. Findings

**4 issues** were reported including:

- 4 low severity issues.

## 3.1. Decrease Approval Logic

### Severity: low

### Description

`decreaseAllowance` function do not check if the value of `subtractedValue` is higher than `_allowed[msg.sender][spender]`, making the transaction execution throw. 

This implementation of `decreaseAllowance` can create a racing condition, if the address owner that allows a value to be spent by another address wants to remove the allowance, and the spender send a transaction to transfer any amount of that value, the token owner transaction will throw.

### Code snippet

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT-commented.sol#L248

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT.sol#L173

### Recommendation

Developers should add a condition to check the above case and set the value of `_allowed[msg.sender][spender]` to zero. 

## 3.2. ERC223 Compatibility

### Severity: low

### Description

`tokenFallback` function is implemented to make the contract able to receive ERC223 tokens, however for unknown reasons the contract developers have added useless extra computation inside the function (knowing that the function is marked as pure) that will risk to throw ERC223 transfer due to gas limit.
As per the ERC223 standard the function can be used to handle an important operation or left empty, contract developers should explain the function logic.

### Code snippet

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT-commented.sol#L268

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT.sol#L187

## 3.3. Owner Address Update

### Severity: low

### Description

`setOwner` function should check that `newOwner` is different than zero to avoid setting the owner to 0x0.

### Code snippet

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT.sol#L53

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT-commented.sol#L76

## 3.4. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

The first issue has been solved partialy by the implemented `approve` function, knowing that the users have to check first if their tokens have been withdrawn after setting the value to zero and before allowing the new value.

### Code snippet

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT-commented.sol#L226

https://github.com/V-ID/V-ID-Token/blob/master/V-IDT.sol#L155

## 4. Conclusion

The contract is safe, but the highlighted issues should be solved.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f8bdee42b4e33a99909f908059a604c1

https://gist.github.com/yuriy77k/7dd6d9b84991fc429ce1bd84e324853a

https://gist.github.com/yuriy77k/4e1cac728133b4e4f1c228ae6ac89f12
