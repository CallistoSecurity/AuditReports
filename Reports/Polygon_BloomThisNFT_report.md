# BloomThis NFT Security Audit Report

# 1. Summary

[BloomThis NFT](https://github.com/Realmstack/BloomThisSmartContracts/tree/main/contracts) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Commit `d90de582dbb4e5c23d275341e849853fc917b85d`

[BloomThis.sol](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol)

# 3. Findings

In total, **6 issues** were reported, including:

- 3 high severity issues.

- 1 medium severity issue.

- 2 low severity issues.

In total, **5 notes** were reported, including:

- 4 notes.

- 3 owner privileges.


## 3.1. withdraw() function does not reset _ownerBalance

### Severity: high

### Description

The function [withdraw()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L259-L264) allows admin(s) to withdraw royalty collected from the contract by the sale of NFT from the secondary market. The function uses the variable `_ownerBalance` to track the royalty collected, admins can use the function `setRoyaltyInfo()` to set the variable `_treasury` address. The `withdraw()` function transfers the amount of `_ownerBalance` ETH from the contract to the `_treasury` address.

The function does not reset the state of `_ownerBalance` after a successful transfer allowing Admins to withdraw multiple times, which could lead to a lack of funds when users try to claim the royalty.

#### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L259-L264

### Recommendation

Reset the _ownerBalance in the withdraw function upon successful transfer.

```solidity
    function withdraw() public validAdmin {
        uint256 balance = _ownerBalance;
        if(balance > 0) {
            _ownerBalance = 0;
            payable(address(_treasury)).transfer(balance);
        }
    }
```

## 3.2. Incorrect computation of _perTokenCumulativeReward leads to locking of funds in the contract

### Severity: high

### Description

The function [issueRewards()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L233) transfers royalty collected by the NFT holders equally when computing `_perTokenCumulativeReward` the computation does not take into consideration of burned tokens. In case tokens are burned, a part of the reward cannot be claimed by the Users and the admins resulting in the lock of funds in the contract.

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L233

### Recommendation

Add counter of burned tokens in the [burn](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L117-L123) function, and in [totalSupply()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L90) function returns the difference between `_tokenCounter` and burned counter.

For example:

```solidity
    Counters.Counter private _burnedCounter;

    function totalSupply() public view returns (uint256) {
        return _tokenCounter.current() - _burnedCounter.current();
    }

    function burn(uint _tokenId, address owner) private {
        super._burn(_tokenId);

        _userTokens[owner][_tokenIndex[_tokenId]] = _userTokens[owner][_userTokens[owner].length -1];
        _tokenIndex[_userTokens[owner][_tokenIndex[_tokenId]]] = _tokenIndex[_tokenId];
        _userTokens[owner].pop();

        _burnedCounter.increment();
    }

```

## 3.3. Lock of unclaimed rewards for users in event fusion

### Severity: high

### Description

When performing a token [fusion](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L196) multiple tokens are burned and one token is minted. When claiming rewards `_userTokens[user].length` i.e the number of tokens owned by the user is considered as a payout multiple for the cumulative token reward. If a fusion is performed on multiple tokens held by the user the rewards decrease and the unclaimed reward is locked in the contract.

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L238

### Recommendation

Add `issueRewards(msg.sender);` at begin of function `doFusion()`.


## 3.4. Incorrect variable type for tokenId

### Severity: medium

### Description

In the function [doFusion()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L196) the array of tokens id has type `uint8[] memory ids`. In this case, if tokenId is bigger than 255 it can not be used in fusion because the maximum value in `uint8` is 255.

### Code snippet
https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L196

### Recommendation

Use `uint256[] memory ids` instead of `uint8[] memory ids`.

## 3.5. Missing zero address check

### Severity: low

### Description

In several places in the code, addresses are passed as parameters to functions. In many of these instances, the functions do not validate that the passed address is not the address 0.

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L253-L257

### Recommendation
While this does not currently pose a security risk, consider adding checks for the passed addresses being nonzero to prevent unexpected behavior where required, or documenting the fact that a zero address is indeed a valid parameter.


## 3.6. There is no limit for the max value of _ownerRoyaltyFeeInBips

### Severity: low

### Description

Setting the [_ownerRoyaltyFeeInBips](https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L255) should check the fee don't exceed 100%.

### Recommendation

Use require statement like `require(_ownerRoyaltyFeeInBips <= 1000)`


## 3.7. Owner Privileges

### Severity: owner privileges

### Description

1. New NFT tokens can only be minted by an authorized admin using the function [mint()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L95-L97).

2. Token fusion rules can only be added by an authorized admin using the function [addFusionRule()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L178-L183).

3. The royalty fee collected by admins can be changed by the admin using the function [setRoyaltyInfo()](https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L253-L257) for the rewards collected impacting the reward per token for the users. 

Consider adding an event to update users in the event of a change in royalty fee.

## 3.8. Infinite minting of tokens possible

### Severity: note

### Description

If `_maxTokens` is initialized as zero, it would allow the admin to mint unlimited tokens.

### Code snippet
- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L101

### Recommendation

Add checks to verify that `_maxTokens` is not zero during initialization.

## 3.9. Gas optimization

### Severity: note

### Description

The value of [_adminList.length](https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L79) should be cached outside the loop, so that the compiler does not calculate it's value on every iteration. Therefore it should be this 

```solidity
uint len = _adminList.length;
for(uint256 i = 0; i < len; i++) {
    if(_adminList[i] == adminAddress) {
        _adminList[i] = _adminList[len - 1];
        _adminList.pop();
        break;
    }
```
We ran this with 8 values in the `_adminList` (till index 7), and it required about 19k gas less than before optimization. In the earlier version it used `67738 gas` whereas in the above changed version it used `48516 gas`.

## 3.10. Follow good coding practice

### Severity: note

### Description

1. It's a good coding practice to mark a function external when it's not called within the contract but only from outside the
contract. 

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L58
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L62
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L72
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L131
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L169
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L174
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L179
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L186
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L196
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L226
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L253
- https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L260

2. Missing docstrings. 

Many functions in the code base lack documentation. This hinders reviewers’ understanding of the code’s intention,
which is fundamental to correctly assess not only security, but also correctness. Additionally, docstrings improve
readability and ease maintenance. They should explicitly explain the purpose or intention of the functions,
the scenarios under which they can fail, the roles allowed to call them, the values returned, and the events emitted.

Consider thoroughly documenting all functions (and their parameters) that are part of the contracts’ public API.
Functions implementing sensitive functionality, even if not public, should be clearly documented as well.
When writing docstrings, consider following the Ethereum Natural Specification Format (NatSpec).

3. Unspecific compiler version pragma

Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler, which may have higher risks of undiscovered bugs. Contracts may also be deployed by others, and the pragma indicates the compiler version intended by the original authors.

4. Internal/private functions should start with an underscore

### Code snippet

https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L100
https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L117
https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L137
https://github.com/Realmstack/BloomThisSmartContracts/blob/main/contracts/BloomThis.sol#L231

## 3.11. Denial of service (DoS) - Transfer function dependent on gas costs

### Severity: note

### Description

Gas refers to the unit that measures the amount of computational effort required to execute specific operations on the Ethereum network. Since each Ethereum transaction requires computational resources to execute, each transaction requires a fee. Gas refers to the fee required to conduct a transaction on Ethereum successfully. Gas fees are paid in Ethereum's native currency, ether (ETH). Gas prices are denoted in gwei, which itself is a denomination of ETH - each gwei is equal to 0.000000001 ETH (10-9 ETH).

Each opcode supported by the EVM has an associated gas cost. For example, SLOAD, which reads a word from storage, currently costs 200 gas. The gas costs aren’t arbitrary. They’re meant to reflect the underlying resources consumed by each operation on the nodes that make up Ethereum. Any smart contract that uses transfer() or send() is taking a hard dependency on gas costs by forwarding a fixed amount of gas: 2300.

In case when associated gas costs increase the function would fail to leave admin/users unable to withdraw or claim royalty from the contract leading to a denial of service (DoS).

### Code snippet

- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L240
- https://github.com/Realmstack/BloomThisSmartContracts/blob/d90de582dbb4e5c23d275341e849853fc917b85d/contracts/BloomThis.sol#L262

### Recommendation

it is recommended to stop using the transfer() and send() in your code and switch to using call() instead. And follow the following protective measures to prevent re-entrancy attacks.

- Make sure all internal state changes are performed before the call is executed. This is known as the [Checks-Effects-Interactions pattern](https://solidity.readthedocs.io/en/latest/security-considerations.html#use-the-checks-effects-interactions-pattern)
- Use a reentrancy lock (ie. [OpenZeppelin's ReentrancyGuard](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol).

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

# 6. Revealing audit reports

- https://gist.github.com/chhajershrenik/5294ddf8395bb05283b78a076e11406f
- https://gist.github.com/SakshamGuruji3/f1101d03db17d45e7759f2c628ab018c

## 6.1. Notes about [Shrenik's report](https://gist.github.com/chhajershrenik/5294ddf8395bb05283b78a076e11406f)

### 6.1.1. [Denial of service (DoS) - Transfer function dependent on gas costs](https://gist.github.com/chhajershrenik/5294ddf8395bb05283b78a076e11406f#denial-of-service-dos---transfer-function-dependent-on-gas-costs)

The `transfer()` and `send()` functions are widely used in many contracts. Gas limit in 2300 was set to prevent a re-entrance attack. If some blockchain changes gas consumption for these functions, it will be an issue for that blockchain because many contracts stop working. 

I'm not in favor of using `call()` because it's a step back to when there wasn't a gas limit for transfer transactions. It requires the use of a re-entrance guard almost for all functions, which increases each transaction gas cost by about 10k gas or even more in some realization and increases contract size.

Therefore now it's not an issue for this contract.

## 6.2. Notes about [Saksham's report](https://gist.github.com/SakshamGuruji3/f1101d03db17d45e7759f2c628ab018c)

### 6.2.1. [BASIS POINTS USAGE DEVIATES FROM GENERAL DEFINITION MAKING THE OWNER RECEIVE 10x FEES](https://gist.github.com/SakshamGuruji3/f1101d03db17d45e7759f2c628ab018c#basis-points-usage-deviates-from-general-definition-making-the-owner-receive-10x-fees)

There is no `general definition of basis points`. The difference between basis points and percent is that 1% is always 1/100, but basis points can use any proportion. For example, the basis point on the FOREX market is 0.01%. 

If a developer wants to set the fee less the 1% (i.e. 0.1%) he sets the basis point equal to 1/1000. 

### 6.2.2. [IF (_ownerRoyaltyFeeInBips * msg.value) < 1000 , OWNER WILL GET 0 FEES](https://gist.github.com/SakshamGuruji3/f1101d03db17d45e7759f2c628ab018c#if-_ownerroyaltyfeeinbips--msgvalue--1000--owner-will-get-0-fees)

When calculating part of some number, we will always lose accuracy. In this case, we can lose the value less than 1000 wei, while the 1 gas cost X Gwei. So if we add any checks, we will lose much more on the gas cost.


