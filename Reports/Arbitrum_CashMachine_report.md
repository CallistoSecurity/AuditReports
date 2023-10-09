# The Cash Machine Security Audit Report

# 1. Summary

[The Cash Machine](https://github.com/d-sfounis/TheCashMachine) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

> The below contract is one of two smart contracts that we will deploy. This contract is for the token itself. It is a USDC auto dividends token, deployed on Arbitrum. The second contract is for our governance schematic. The token has been deployed but is not yet available on the open market.

- website: https://www.thecashmachine.io/

# 2. In scope

https://arbiscan.io/token/0x070Aa7BCE60Ae4d7a459026f339E3C94284E21c7

The code corresponds to Github commit [826dc9efb3458975d70ba50b55901c3730a0fddf](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/)

# 2.1 Excluded

The correctness of the mathematical calculations was not verified during the audit due to the lack of complete documentation of what the contract should do and under what conditions.

# 3. Findings

In total, **2 issues** were reported, including:

- 0 high severity issues.

- 1 medium severity issues.

- 1 low severity issues.

In total, **10 notes** were reported, including:

- 2 notes.

- 8 owner privileges.


## 3.1. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).

2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Declare hardcoded addresses as `constant`

### Severity: note

### Description

To reduce gas usage the hardcoded addresses [WETH, DEAD, ZERO](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L298-L300) and this [WETH](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L97) should be declared as `address constant`.



## 3.3. Incorrect comments or values

### Severity: note

### Description

1. The [_totalSupply](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L307) is set to 10 million tokens but in comment specified `10 Billion`. 
2. The [liquidityFee](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L321) is set to 0 but in comment specified `Used to be 200 == 2%`.

Please, pay attention to these.

## 3.4. The function `shouldDistribute()` may never returns true

### Severity: medium

### Description

The function [shouldDistribute()](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L232) to returns `true` require `getUnpaidEarnings(shareholder) > minDistribution`. The function `getUnpaidEarnings(shareholder)` returns amount in USDC which has 6 decimals, however [minDistribution](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L113) set to `1 * 10**18` (has 18 decimals). This means that `minDistribution = 10**12 USDC` (1 Trillion USDC). Even if owner will try to [setDistributionCriteria](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L163-L165) the `minDistribution` will have 18 decimals as well. The only why to pass this verification is set `minDistribution` to 0.

### Recommendation

Use `minDistribution` value with the same decimals number as [TOKEN](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L95). 

Remove from function [setDistributionCriteria](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L163-L165) preset decimals value. It will allow owner to set `minDistribution` according to the [TOKEN](https://github.com/d-sfounis/TheCashMachine/blob/826dc9efb3458975d70ba50b55901c3730a0fddf/contracts/TheCashMachine.sol#L95) decimals.

```Solidity
    function setDistributionCriteria(uint256 _minPeriod, uint256 _minDistribution) external override onlyToken {
        minPeriod = _minPeriod;
        minDistribution = _minDistribution;
    }
```

## 3.5. Owner privileges

### Severity: owner privileges

### Description

The owner of The Cash Machine contract has right to:

1. Set amount limit per transaction;
2. Set reflect token;
3. Set maximum amount per wallet;
4. Set Dividend Exempt addresses;
5. Set Fee Exempt addresses;
6. Set Transaction Limit Exempt addresses;
7. Set fees;
8. Set Distribution Criteria.



# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. At least medium severity issue must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
