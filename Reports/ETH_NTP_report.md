# 1. Summary

[NTP Tokens](https://github.com/networktokenpayment/networktokenpayment/blob/master/ntp) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- [ntp.sol](https://github.com/networktokenpayment/networktokenpayment/blob/master/ntp) github commit hash 72fac4492abee0b14ac0133b94197b5bfe2ca9d6.

# 3. Findings

In total, **4 issues** were reported including:

- 1 medium severity issues.

- 3 low severity issues.

No critical security issues were found.

## 3.1. The owner can mint any value of tokens he wants.

### Severity: medium

### Description

The owner or a hacker (if the owner's private key will be compromised) can mint any value of tokens he wants using functions `add` + `DistributeAirdrop` at any time. The token can become worthless very quickly. This is dangerous for investors.

### Code snippet

https://github.com/networktokenpayment/networktokenpayment/blob/72fac4492abee0b14ac0133b94197b5bfe2ca9d6/ntp#L337-L341

https://github.com/networktokenpayment/networktokenpayment/blob/72fac4492abee0b14ac0133b94197b5bfe2ca9d6/ntp#L191-L193

## 3.2. Check the input value of change TokensPerEth.

### Severity: low

### Description

It is necessary to check the input value of the updateTokensPerEth function for zero-value. In case the argument is not specified, the user can pay money but not get tokens.

### Code snippet

https://github.com/networktokenpayment/networktokenpayment/blob/72fac4492abee0b14ac0133b94197b5bfe2ca9d6/ntp#L199-L202

## 3.3. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit). 
(Adding this condition ` if (_value != 0 && allowed[msg.sender][_spender] != 0) { return false; }` to `approve` function do not fully solve the problem with double withdrawal attack, since the address that allows the spending has to check if the tokens where spent after resetting the allowance to zero.)

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add into a function `transfer(address _to, ... )` following code:

```solidity
require( _to != address(this) );

```
## 3.4. Owner Privileges

### Severity: low

### Description

The contract owner allows himself to:

- set the price
- finish distribution tokens
- withdraw all fonds of contract
- burn and mint tokens
- DistributeAirdrop and DistributeAirdropMultiple functions do not have a airdrop limit except the total supply itself, please not that even the amount of airdroped tokens can be higher than totalsupply if the last call to DistributeAirdrop with the amount of tokens to be airdroped is high enough.

The contract is managed manually by the owner which is not good for investors.

## 3.5. Always false condition

### Severity: low

### Description

`getTokens` function include a condition `if( now >= deadline && now >= round1 && now < round2)` that will always be false since `now` cannot be higher than `deadline` and lower than `round2` knowing that `deadline` is higher than `round2`. the impact of this issue is small, however, the developers should explain their intentions by adding it to the code to know the correct severity.
 
### Code snippet

https://github.com/networktokenpayment/networktokenpayment/blob/master/ntp#L109#L111

https://github.com/networktokenpayment/networktokenpayment/blob/master/ntp#L249


# 4. Conclusion

The review did not show any critical issues, but investors should be aware of the risks when using contract that can be externally influenced by the owners.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/6f4d04788ac52ae87257822f729eabb0

https://gist.github.com/yuriy77k/606c40dcc152fd1b0ad40ba4d3761618

https://gist.github.com/yuriy77k/6eb9ca648639798f8c9217e22af1f3f5
