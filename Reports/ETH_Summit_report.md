# Summit Security Audit Report

# 1. Summary

[Summit](https://github.com/SNOSDEV/SummitContracts/tree/master/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit [13d3e55c7e7933aa412af6d1174382982d358d73](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/)

- IOxygen.sol
- Oxygen.sol
- Summit.sol
- VestedSale.sol

## 2.1. Excluded

1. Calculation formulas excluded from audit due to lack of contract documentation.

2. Standard OpenZeppelin contracts were excluded from audit:

- @openzeppelin/contracts/token/ERC20/IERC20.sol
- @openzeppelin/contracts/access/Ownable.sol
- @openzeppelin/contracts/token/ERC20/ERC20.sol
- @openzeppelin/contracts/utils/math/SafeMath.sol

3. Standard Uniswap Interfaces:

- @uniswap/v2-core/contracts/interfaces/IUniswapV2Pair.sol
- @uniswap/v2-core/contracts/interfaces/IUniswapV2Factory.sol
- @uniswap/v2-periphery/contracts/interfaces/IUniswapV2Router02.sol

4. Standard Hardhat:

- hardhat/console.sol



# 3. Findings

In total, **7 issues** were reported, including:

- 0 high severity issues.

- 4 medium severity issues.

- 3 low severity issues.

In total, **0 notes** were reported, including:

- 0 notes.

- 0 owner privileges.



## 3.1. Known vulnerability of ERC-20 token

### Severity: low

### Description

Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20)  This is a very common issue, and it already caused millions of dollars in losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit).

### Recommendation

Add the following code to the `transfer(_to address, ...)` function:

```
require( _to != address(this) );

```

## 3.2. Recheck calculation in function calculate02()

### Severity: low

### Description

Since we have not documentation with the calculation formulas, we can't say exactly how it should be. But in the function [calculate02()](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L112-L114) the calculation looks strange. 

```Solidity
        uint256 ethInput = uniswapV2Router.getAmountIn(tokenAmount, ethReserve, tokenReserve);
        uint256 tokenPrice = (ethReserve + ethInput) * magnitude / (tokenReserve - tokenAmount);
        return ethInput * tokenPrice / magnitude * getAltitude();
```
 
The function `getAmountIn` returns amount of ETH that should be send to the pool to receive `tokenAmount` (it already include slippage and fee). So `tokenPrice` can be calculated as `ethInput * magnitude / tokenAmount`. This value means how much ETH 1 token cost (ETH/token). But in `return` statement you multiply `ethInput * tokenPrice` which means `ethInput * ethInput / tokenAmount`. 

What sense of this calculation?


## 3.3. `expeditionFrequency` was not initialized

### Severity: medium

### Description

The variable [expeditionFrequency](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L31) was not initialize and used in the functions [getAltitude()](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L158) and [getNextExpedition()](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L162) with its default value `0`. It can affect on the entire contract logic.




## 3.4. Condition does not match to description

### Severity: low

### Description

In the function [_transfer](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Summit.sol#L82) the condition `require(gameStart > block.timestamp, "The game has not started");` does not match with description.

It should be `require(gameStart < block.timestamp, "The game has not started");` or `require(gameStart > block.timestamp, "Game has already started"");`.


## 3.5. Incorrect call of function `onSummitBuy()`

### Severity: medium

### Description


The function [onSummitBuy(uint256 tokenAmount, address account, uint256 altitude)](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L86) require three parameters, but contract `Summit` tries to [call it with two parameters](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Summit.sol#L86), so this calls will fail.

### Recommendation

Use the same number of parameters in function call.

## 3.6. Wrong behavior of function `onSummitBuy()`

### Severity: medium

### Description

The function [onSummitBuy()](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L86) should be called when somebody buy `Summit` token. This function mint `Oxygen` tokens to the buyer and call function [mint02](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L93). But this function will fail due to underflow exception when try subtract form [magnifiedEthCorrections[account]](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L118) (that is 0 for new buyer) some value `magnifiedEthPer02 * amount`.

Please, recheck calculation formula.


## 3.7. Calculation in the function `accumulativeEthOf()` looks strange.

### Severity: medium

### Description

Since we have not documentation with the calculation formulas, we can't say exactly how it should be. But in the function [accumulativeEthOf()](https://github.com/SNOSDEV/SummitContracts/blob/13d3e55c7e7933aa412af6d1174382982d358d73/contracts/Oxygen.sol#L154) the calculation looks strange (incorrect). 

Please, recheck it with the required formulas.




# 4. Security practices

- [x] **Open-source contact**.
- [ ] **The contract should pass a bug bounty after the completion of the security audit.**
- [ ] **Public testing.**
- [ ] **Automated anomaly detection systems.** - NOT IMPLEMENTED. A simple anomaly detection algorithm is recommended to be implemented to detect behavior that is atypical compared to normal for this contract. For instance, the contract must halt deposits in case a large amount is being withdrawn in a short period of time until the owner or the community of the contract approves further operations.
- [ ] **Multisig owner account.**
- [ ] **Standard ERC20-related issues.** - NOT IMPLEMENTED. It is known that every contract can potentially receive an unintended ERC20-token deposit without the ability to reject it even if the contract is not intended to receive or hold tokens. As a result, it is recommended to implement a function that will allow extracting any arbitrary number of tokens from the contract.
- [ ] **Crosschain address collisions.** ETH, ETC, CLO, etc. It is possible that a transaction can be sent to the address of your contract at another chain (as a result of a user mistake or some software fault). It is recommended that you deploy a "mock contract" that would allow you to withdraw any tokens from that address or prevent any funds deposits. Note that you can reject transactions of native token deposited, but you can not reject the deposits of ERC20 tokens. You can use this source code as a mock contract: [extractor contract source code](https://github.com/EthereumCommonwealth/GNT-emergency-extractor-contract/blob/master/extractor.sol). The address of a new contract deployed using `CREATE (0xf0)` opcode is assigned following this scheme `keccak256(rlp([sender, nonce]))`. Therefore you need to use the same address that was originally used at the main chain to deploy the mock contract at a transaction with the `nonce` that matches that on the original chain. _Example: If you have deployed your main contract with address 0x010101 at your 2021th transaction then you need to increase your nonce of 0x010101 address to 2020 at the chain where your mock contract will be deployed. Then you can deploy your mock contract with your 2021th transaction, and it will receive the same address as your mainnet contract._

# 5. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

It is recommended to adhere to the security practices described in pt. 4 of this report to ensure the contract's operability and prevent any issues that are not directly related to the code of this smart contract.
