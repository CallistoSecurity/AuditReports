# Security Audit Report

# 1. Summary

[OmiseGO (OMG) Token](https://etherscan.io/address/0xd26114cd6EE289AccF82350c8d8487fedB8A0C07#code) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [OMGToken.sol](https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645).

# 3. Findings

In total, **9 issues** were reported including:

- 6 low severity issues.

- 3 minor observation.

No critical security issues were found.

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

## 3.2. Allowance Approval

### Severity: low

### Description

Following ERC-20 final [description](https://eips.ethereum.org/EIPS/eip-20):

"NOTE: To prevent attack vectors like the one [described here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit) and discussed [here](https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729), clients SHOULD make sure to create user interfaces in such a way that they set the allowance first to 0 before setting it to another value for the same spender. THOUGH The contract itself shouldn't enforce it, to allow backwards compatibility with contracts deployed before.

The implemented approve function:

    Do not preserve backwards compatibility since it enforces the allowance values to be set.

    Do not throw in case if the following condition is true if (_value != 0 && allowed[msg.sender][_spender] != 0) and return false, users might not notice that the changes didn't occur, and external contract calls to this function will highlight many other issues.

### Code snippet

https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645#file-omgtoken-sol-L164

### Recommendation

Do not try to mitigate the issue and implement increaseApproval and decreaseApproval functions instead.

## 3.3. No checking for zero address.

### Severity: low

### Description

`Transfer`, `transferFrom`, `mint`, `TokenTimelock` functions do not prevent from sending tokens to address 0x0.

### Recommendation

Add zero address checking

```
solidity
require(to != address(0));
```

## 3.4. Poor workaround of Short Address Attack

### Severity: low

### Description

`onlyPayloadSize` modifier is workaround to avoid the Short Address Attack. But it doesn't work properly.

1. This method failed to execute when it was called from Parity multisignature wallet. The EVM pads call from this multisignature wallet, making the total 96 bytes instead of the expected 68.

2. If `transfer` and `transferFrom` are used by a subcontract function with fewer arguments, the onlyPayloadSize check will fail. It is not possible to adapt the workaround to prevent this issue.

More details [here](https://blog.coinfabrik.com/smart-contract-short-address-attack-mitigation-failure/).

### Code snippet

https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645#file-omgtoken-sol-L81-L86

### Recommendation 

Remove this workaround.

## 3.5. Owner Privileges

### Severity: low

### Description

The contract owner allow himself to pause functions of contract (transfer, transferFrom), mint any value of tokens.

The contract is managed manually by the owner which is not good for investors.
 
## 3.6. ERC20 Compliance — event missing

### Severity: low

### Description

The `mint` function should emit the `Transfer` event.

### Code snippet

https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645#file-omgtoken-sol-L254

## 3.7. Discrepancy with the ERC20 standard

### Severity: minor observation

### Description

In OMGToken.sol, according to the ERC20 standard, the variable decimals should be declared as uint8.

### Code snippet

https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645#file-omgtoken-sol-L382

## 3.8. It is required to limit the maximum of _releaseTime argument.

### Severity: minor observation

### Description

For various reasons (accident, vulnerability on the caller smart contract), _releaseTime variable may have a large value which can lead to overflow and that is more critical to blocking tokens for many many years. We can not rely on the correct input data and should check it in this contract.

### Code snippet

https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645#file-omgtoken-sol-L355

## 3.9. There is no minting cap

### Severity: minor observation

### Description

Owner could `mint` any amount he wants and there is no hard cap.

### Code snippet

https://gist.github.com/yuriy77k/d52b7674ca7cf9b221a819db168fb645#file-omgtoken-sol-L251

# 4. Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/5f84f69befd3f6681f8b5445e24bdb53

https://gist.github.com/yuriy77k/960e5a709d35522a5deb3f35c4ba15b3

https://gist.github.com/yuriy77k/9d28025ce4468a986c8c469a33f34172
