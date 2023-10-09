# [CargoCoin](https://github.com/CargoCoinRepo/Cargo-Coin) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

Operability issues and compatibility issues were highlighted, the issues should be fixed before deployment.

# 2. High severity issues:

No  High severity issues

# 3. Medium severity issues:

## 3.1. Escrow Logic

### Description

The escrow logic is not described (this issue can be high since the contract operability is clearly not fulfilling a basic escrow logic), contract developers should explain the escrow design for better analysis.

- In order for the exporter to receive the fund both importer and exporter have to call 'accept'.
- If the importer accepts to send the fund to the exporter and the exporter do not call `accept` before `startTime + waitingTime`, the contract will be destructed and the fund sent back to the importer if the importor call `accept`.
- Importer will never be able to cancel the transaction to get his fund back, since when calling `cancel` function member of `CargoContract`, `importerAccepted` will be set to false.
- The exporter will be able to cancel the fund only if the importer call `accept` and set `importerAccepted` to true. in this case the exporter is pushed to call accept so he can take the fund since the importor has already accepted to transfer the fund.

### Code snippet

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/Main.sol#L26#L45

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/Main.sol#L67#L82

## 3.2. ERC223 Standard Compliance

### Description

The reviewed token contract is not ERC223 fully compliant, the overloaded [`transfer`](https://github.com/CargoCoinRepo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L148#159) function member of `StandardToken` contract, calls `tokenFallback` function on the receiver contract before adding the `value` to `balances[_to]`. The original implementation add the tokens value to the balance before making the external call (check the link below).

https://github.com/Dexaran/ERC223-token-standard/blob/master/token/ERC223/ERC223_token.sol#L63#L68

### Code snippet

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L1


# 4. Low severity issues:

## 4.1. Token ERC20/ERC223 Design 

### Description

The main token contract `StandardToken` implement both ERC20 function and ERC223, however ERC223 is backward compatible with ERC20, by doing this the contract developers added the same issues to the token that were supposed to be solved by the ERC223 standard.

ERC20 Tokens have some well-known issues (listed below), This is just a reminder for the contract developers.

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

### Code snippet

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L91#99

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L101#L112

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L114#L119

## 4.2. Kill Specifier

### Description

The function `kill` member of `CargoContract` is marked as `constant` which is an alias for `view`, this specifier promess not to modify the state. however `kill` function does modify the contract state by calling `selfdestruct`.

when directly using an instance of the contract that has been set with the abi, `kill` will not destruct the contract since it will just a call `kill` and it won't submit a transaction.

A transaction can still be submitted by using for example:

```
eth.sendTransaction({from:_CargoCoinAuthority_address_, to:_address_of_contract_, data:_kill_function_data_});
```

### Code snippet

https://github.com/RideSolo/Cargo-Coin/blob/master/contracts/Main.sol#L84#L90

### Recommendation

Remove `constant` specifier, and set the function to `public`

## 4.3. Zero address checking. 

### Description

In function [`transfer`](https://github.com/CargoCoinRepo/Cargo-Coin/blob/c41f304ca1053d9fd817389c2e52a44202d67342/contracts/CargoCoin.sol#L148), [`StandartToken`](https://github.com/CargoCoinRepo/Cargo-Coin/blob/c41f304ca1053d9fd817389c2e52a44202d67342/contracts/CargoCoin.sol#L70) and [`CargoContract`](https://github.com/CargoCoinRepo/Cargo-Coin/blob/c41f304ca1053d9fd817389c2e52a44202d67342/contracts/Main.sol#L17) there are no checking for zero address.

### Recommendation

Add checking for zero address.
```solidity
require(_to != address(0));
```


# 5. Minor observation:

## 5.1. Extra checking

### Description

Extra checking in [94](https://github.com/CargoCoinRepo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L94), [104-105](https://github.com/CargoCoinRepo/Cargo-Coin/blob/master/contracts/CargoCoin.sol#L104-L105) lines. SafeMath library checks it anyway.

### Recommendation

Those lines may be deleted.


# Revealing audit reports:

https://gist.github.com/yuriy77k/292c4fe9b6636794ae11935dced0aa5e

https://gist.github.com/yuriy77k/b1df15860fbec939cde829dc46e2d473

https://gist.github.com/yuriy77k/5f579ef2bb6ca27583ccc5f7697b35ee

