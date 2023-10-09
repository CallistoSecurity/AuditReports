# Data Mall Coin (DMC) Security Audit Report

# 1. Summary

[Data Mall Coin (DMC)](https://github.com/datamallchain) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `4e4070cdef9ed001b2851ed36822b795c1f90934`

- [eosio.token.hpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/include/eosio.token/eosio.token.hpp)

- [utils.hpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/include/eosio.token/utils.hpp)

- [classic_token.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/classic_token.cpp)

- [dmc.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp)

- [dmc.deliver.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_deliver.cpp)

- [lock_account.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/lock_account.cpp)

- [eosio.token.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/eosio.token.cpp)

- [dmc_challenge.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_challenge.cpp)

- [uniswap.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/uniswap.cpp)

- [smart_token.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/smart_token.cpp)

- [smart_extend.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/smart_extend.cpp)

- [record.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/record.cpp)

- [nft.cpp](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/nft.cpp)

# 2.1 Excluded

The business logic of the audited application is outside of the scope of this audit. This is a security audit intended to verify the correctness of the technical part of the system and ensure its fault resistance.

# 3. Findings

In total, **5 issues** were reported, including:

- 0 high severity issues.

- 0 medium severity issues.

- 5 low-severity issues.

In total, **9 notes** were reported, including:

- 8 notes.

- 1 owner privilege.

## 3.1 Indirect logical expression can potentially lead to missing overflow checks

### Severity: note

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L16-L19

```
    eosio_assert(price >= 0.0001 && price < std::pow(2, 32), "invaild price");
    eosio_assert(asset.amount > 0, "must bill a positive amount");

    uint64_t price_t = price * std::pow(2, 32);
```

### Description

In this expression, `price_t` is defined by the value of `price` multiplied by 2^32, and this can not lead to overflow because the previous assertion `price >= 0.0001 && price < std::pow(2, 32)` regulates the value of `price` i.e. it is a positive value less than 2^32. 

However, there is no overflow check specifically for `price_t` and change in L16 can potentially lead to the breach of the `price_t` logic. This is not the case in the current version of the contract `4e4070cdef9ed001b2851ed36822b795c1f90934`, but it can be safer to implement a direct check to avoid any potential problems in the future if the code is updated.

### Recommendation

Implement an overflow check specifically for `price_t` after its assignment [in L19](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L19).

## 3.2 Comment typo (Observation / not a security flaw)

### Severity: note 

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L16

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L205

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L312

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L479-L483

### Description

It should be "invalid price" / "invalid rate" etc.

## 3.3 Missing memo length check
### Severity: note

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L346

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L516

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L660

### Description

Memo length should not exceed [256 characters](https://eosio.stackexchange.com/questions/4404/what-is-the-character-limit-of-the-eos-memo-field).

In the current `4e4070cdef9ed001b2851ed36822b795c1f90934` state of the contract, the overflow cannot be directly caused since `memo` is the only arg that [liquidation](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L346) function accepts but it should be taken into account.

## 3.4 `change_order` function accepts `name payer` and does nothing with it (Unclear)
### Severity: note

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_deliver.cpp#L10

### Description

Is it intentional? Or authorization requirement is missing `require_auth(payer);` ? Documentation does not provide a sufficient explanation of how this function is intended to work.

## 3.5 Hardcoded privileged account name

### Severity: low

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_deliver.cpp#L298

### Description

`dmcmonitor11` account is hardcoded as a privileged account to call [destorypst](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_deliver.cpp#L295) function. This is not a good practice of having just one strictly hardcoded account with privileges. In case the privileged account will be (1) lost, (2) compromised, (3) it will not have enough resources to perform a transaction on EOSIO mainnet or (4) it will be blacklisted by Block Producers for some reason, the system will require a significant update.

It would be more scalable to introduce a more sophisticated access management system.

### Recommendation

- It is possible to use at least two privileged accounts so that each of them would be allowed to perform the required action. 

```
    require_auth(payer);
    account_name payer_name1 = N(dmcmonitor11);
    account_name payer_name2 = N(name.x);
    eosio_assert(payer == payer_name1 || payer == payer_name2, "only privileged account can use this function");

```

- Alternatively, it is possible to store `privileged_account_name` in a singletone and have one more privileged account that can manage this singletone content, and therefore, it will not be as likely that those two accounts will become compromised at the same time.


## 3.6 Privileged account `dmcmonitor11` does not exist
### Severity: low

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_deliver.cpp#L298

### Description

This privileged account is not yet signed up on EOSIO mainnet. It can be potentially taken by someone before the audited DMC contracts will be deployed.

### Recommendation

Sign up for a privileged account first, then update the code so that an already existing account under developers' control would be used as a privileged one.

## 3.7 Privileged account should have a significant amount of resources
### Severity: note

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc_deliver.cpp#L298

### Description

It is possible that this privileged account will be required to perform an action on its own, so it is recommended to stake enough CPU and NET. EOSIO users normally rely on "free CPU/NET" but it can be unavailable in case of ongoing network attacks or depletion of resources on the CPU/NET provider.

### Recommendation

Stake CPU and NET on the privileged account.

## 3.8 There are no "halt" functions implemented
### Severity: note

### Description

In recent years, most of the hacks have happened to DeFi platforms. Every platform that operates with significant stakes of funds is a big target for hackers. As a result, the platform must be as fault-tolerant as possible, and it must be prepared for worst-case scenarios.

In order to be prepared for such scenarios, the structure of the platform must allow for "emergency stops" that will be enacted should any suspicious activity be detected.

It is possible to remove the bytecode from the contract account completely in case of emergency, thus rendering it "frozen" but it can be better to implement built-in halt functions in case of a system of interconnected contracts.

### Recommendation

Implement a "status" contract that will only store a state variable that will allow the platform to operate or render it "frozen".

Let other contracts read the state of the "status" contract.

Implement a security check that will allow to effective freeze all contract functions in all contracts at the same time should the state of the "status" contract change:

```
eosio_assert(status == "operating", "all functions can be called when _status_ contract is in _operating_ mode");
```

## 3.9 There are no cleanup functions in the system
### Severity: note

### Description

If it is required to update or change data structures in the contract within an update of the contract code, then it will require to empty the existing data structures first. A contract with existing data structures can not be updated or it will result in a breach of the functions that interact with the data structures.

As a result, it may be necessary to implement data cleanup functions in the contract logic.

## 3.10 `require_auth(eos_account)` requires authorization from the system contract (Unclear)
### Severity: note
### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L348

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L478

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/dmc.cpp#L518

### Description

[eos_account](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/include/eosio.token/eosio.token.hpp#L19) is assigned to `eosio` which is a [system account](https://github.com/EOSIO/eosio.contracts/blob/master/contracts/eosio.system/src/eosio.system.cpp).

## 3.11 Memo check requires `memo` to be less than 512 bytes
### Severity: low

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/nft.cpp#L104

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/nft.cpp#L143


### Description

Common check is `256` bytes.

## 3.12 `nfttransfer` and `nfttransferb` are not notifying recipients
### Severity: low

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/nft.cpp#L101-L138

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/nft.cpp#L140-L185

### Description

The NFT transfer function does not notify the RECIPIENT and SENDER of the transaction. Communication model implementation is missing.

### Recommendation

Add `require_recipient( from )` and `require_recipient( to )` to the `nfttransfer` and `nfttransferb` functions.

## 3.13 Only the asset contract (owner) can destroy the record of its asset in the contract table
### Severity: owner privileges

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/smart_extend.cpp#L10-L42

### Description

Only the asset contract (contract of each token) can cause its symbol to be erased from the table of the exchange contract. It can be a good practice to have another option to erase the token records with a privileged account in order to clean up the exchange contract.

## 3.14 `exissue` function does not notify the recipient of the new token if `to` == `foundation`
### Severity: low

### Code snippet

- https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/smart_token.cpp#L58-L60

### Description

In the case of `exissue` function call [extranfer](https://github.com/datamallchain/dmchain_contract/blob/4e4070cdef9ed001b2851ed36822b795c1f90934/dmc.contracts/eosio.token/src/smart_token.cpp#L59) in subsequent call will notify the recipient of the incoming transaction. However, if `to != foundation` is false i.e. `to` and `foundation` is the same account, then no notification will be created. This can result in `foundation`s balance being greater than it expects as it fails to record new tokens in case `foundation` is a contract.

# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**


# 5. Conclusion

The audited smart contract can be deployed. Only low-severity issues were found during the audit.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.

