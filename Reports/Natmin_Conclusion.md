# [Natmin](https://github.com/NatminPureEscrow/Token) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

The contract is safe to be deployed. However the current implementation of Natmin risks to conflict with contract when transferring token to them and throw the transfer transaction.

# 2. High severity issues:

No High severity issues

# 3. Medium severity issues:

## 3.1. ERC223 Standard Compliance

### Description

The reviewed token contract is not ERC223 fully compliant, `transferToContract` function member of `NatminToken` contract call `tokenFallback` external function on the receiver contract before adding the `value` to `balances[_to]`. The original implementation adds the token value to the balance before making the external call (check the link below).

https://github.com/Dexaran/ERC223-token-standard/blob/master/token/ERC223/ERC223_token.sol#L63#L68

Different issues can be raised depending on `tokenFallBack` implementation on the receiver contract. A good example is if the contract tries to move the tokens from its balance when the tokens are not yet added to it.

### Code snippet

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L188#L224

## 3.2. Token Transfer to Address 0x0

### Description

The implemented version of ERC20/ERC223 Token do not require the `_to` address in `transferToAddress` and `transferFrom` to be non null before token transfer. Accidental token loss to address 0x0 can be applicable.

### Code snippet

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L207

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L220


# 4. Low severity issues:

## 4.1. Vesting Logic

### Description

If the owner set a vesting amount and an end time for a user using `addVesting` function, and if the user receives tokens from another address, he won't be able to transfer the extra amount even if `balancesOf[user] > vestings[user].amount`.

Actually purpose of `vestings[user].amount` is unclear. It used only in function `getVestedAmount`, but this function is unused in contract.

### Code snippet

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#L274#L277

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#190

https://github.com/NatminPureEscrow/Token/blob/master/contracts/NatminToken.sol#209

## 4.2. Known Issue of ERC20 Standard 

### Description

This is just a reminder for the contract developers (the described ERC20 issue is well-known and well documented).

Approve + transferFrom mechanism allows double Withdrawal attack. 


# 5. Minor observation:

## 5.1. No need of require.

### Description

SafeMath.sub() will automatically throw, if someone will try send more, than he has. In transfer and transferFrom functions no need to check it with require.

### Code snippet
https://github.com/NatminPureEscrow/Token/blob/fcfdf37b07ba613bf8ca4ecd566865344e72dd82/contracts/NatminToken.sol#L189
https://github.com/NatminPureEscrow/Token/blob/fcfdf37b07ba613bf8ca4ecd566865344e72dd82/contracts/NatminToken.sol#L208
https://github.com/NatminPureEscrow/Token/blob/fcfdf37b07ba613bf8ca4ecd566865344e72dd82/contracts/NatminToken.sol#L222

### Recommendation

In lines 189, 208, 222 no need of require.


# Revealing audit reports:

https://gist.github.com/yuriy77k/7a8c8bdd49b85fe6c60266fe89c46e77

https://gist.github.com/yuriy77k/3ba02eb8a956fc3e94b3fabdae292419

https://gist.github.com/yuriy77k/fa0cf2c82f5765345e4f9f05ce8fa9b8

