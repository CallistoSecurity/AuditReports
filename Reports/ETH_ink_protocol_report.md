# 1. Summary

[Ink Protocol](https://github.com/InkProtocol/contracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

InkProtocol is a escrow contract that should assure to buyers and sellers safe transactions, InkProtocol aim to make a decentralized escrow system that keep track of users feedbacks and allow any third party website to use it.

The basic definition of an escrow is a third party entity that hold on behalf of two other parties an asset in order to complete a transaction when the specified conditions of the contract are met.

# 2. In scope

- [InkProtocol.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkProtocol.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkProtocolCore.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkProtocolCore.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkProtocolInterface.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkProtocolInterface.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkProtocolTestnet.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkProtocolTestnet.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkMediator.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkMediator.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkOwner.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkOwner.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkPolicy.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/InkPolicy.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [ErrorPolicyMock.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/mocks/ErrorPolicyMock.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [InkProtocolMock.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/mocks/InkProtocolMock.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [MediatorFeeMock.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/mocks/MediatorFeeMock.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [MediatorMock.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/mocks/MediatorMock.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [OwnerMock.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/mocks/OwnerMock.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.
- [PolicyMock.sol](https://github.com/InkProtocol/contracts/blob/master/contracts/mocks/PolicyMock.sol) github commit hash 3e4de52bd6fc63954fd4242406e2abbcc2b98f94.

# 3. Findings

In total, **2 issues** were reported including:

- 2 low severity issues.

## 3.1. Transaction Creation

### Severity: low

### Description

`createTransaction` function allows the buyer to initiate a transaction with a seller, however the mediator contract and policy contract address is inputted by buyer which allows the buyer to use a modified contracts and let himself after Escalation of the transaction state to the mediator to refund his deposit even after that he receives the object of the transaction. The actual user interface of Listia allows avoiding this problem, but others marketplaces which will use Ink protocol may have this issue.

### Code snippet

https://github.com/InkProtocol/contracts/blob/d2178674670b69b6fdaf174578a267b4edbf304c/contracts/InkProtocolCore.sol#L126

https://github.com/InkProtocol/contracts/blob/d2178674670b69b6fdaf174578a267b4edbf304c/contracts/InkProtocolCore.sol#L191

## 3.2. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/InkProtocol/contracts/blob/d2178674670b69b6fdaf174578a267b4edbf304c/contracts/InkProtocolCore.sol#L9

# 4. Conclusion

The audited contracts can be deployed. 

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f9eb1338accd656c71fae5cbd08c63e6

https://gist.github.com/yuriy77k/aab57fd5ca97c7a50ffeba98bd7e9add

https://gist.github.com/yuriy77k/19ad91ff85f738ef09929ac052d118b1
