# 1. Summary

[Time Lock Escrow](https://gist.github.com/p3c-bot/b533413550da179c5e89da410aca3dd7) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ escrow.sol](https://gist.github.com/p3c-bot/b533413550da179c5e89da410aca3dd7).

# 3. Findings

In total, **4 issues** were reported including:

- 1 high severity issues.

- 2 medium severity issues.

- 1 minor observation.


## 3.1. Front-running attack

### Severity: high

### Description

If there is a situation where the oracle can choose to either give funds to the user or to the provider, the unfair user/provider can watch the transaction pool for `oracleEvaluateJob` transaction contain the `oraclePasswordUnhashed` and create a new transaction of their own with a higher gasPrice and get their transaction included in a block before the original.

### Code snippet

https://gist.github.com/p3c-bot/b533413550da179c5e89da410aca3dd7#file-escrow-sol-L68-L93

### Recommendation

`oracleEvaluateJob` should be only accessible by the oracle (address or any other proof that can be used).

## 3.2. Customer Refund

### Severity: medium 

### Description

As described the contract allow the customer to get the funds back any time after the deadline, without needing the oracle, as long as they control the password.

However this do not make sense, the provider can finish his job perfectly and the customer can choose to not share the password with him and get his fund back after the deadline, if the oracle do not decide meanwhile.

### Code snippet

https://gist.github.com/p3c-bot/b533413550da179c5e89da410aca3dd7#file-escrow-sol-L53#L65

### Recommendation

The customer should not be allowed to withdraw his fund back except if the contractor accept or the oracle allows it.

## 3.3. Oracle Password

### Severity: medium 

### Description

- The Oracle password hash can be set by the customer since `createJob` function is public and anyone can create a job using it (please not that the function is also payable so it will be mainly be used by the customer since he will lock his funds), which means that the customer can control both password, making him even able to withdraw his fund back before any deadline since he can use `oracleEvaluateJob` at any moment. 

- All parties can check the hashed passwords, however the customer can also just slightly change the oracle password hash and deceive the other parties by making them think that the hashed password is the same, wait for the deadline and use `evaluateJob` as described previously to get his fund back.

### Code snippet

https://gist.github.com/p3c-bot/b533413550da179c5e89da410aca3dd7#file-escrow-sol-L68#L93

### Recommendation

Many crosschecking can be done in the user interface but the main goal of the ethereum blockchain is to be by itself 100% safe if the contract are implemented correctly.

`oracleEvaluateJob` should be only accessible by the oracle (address or any other proof that can be used) on which both customer and provider have agreed upon, the contract should reflect this logic by not giving any chance for a possible manipulation.

## 3.4. Consider using latest version of solidity.

### Severity: minor observation

### Description

The contracts use solidity version 0.4.21. It is suggested to use the latest version and fix all compiler warnings that arise.

# 4. Conclusion

Critical issues seriously endanger smart contracts security. We highly recommend fixing them. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/b3d0fb327db96efd901635f79d1afb1b

https://gist.github.com/yuriy77k/338702a32f984aa7659f6a6bf722e501

https://gist.github.com/yuriy77k/83280e9611ea56aa1402e1329f2ea8fc
