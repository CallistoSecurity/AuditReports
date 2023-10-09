# [Hydrogen](https://github.com/hydrogen-dev/smart-contracts/tree/master/decentralization-ambassadors) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 1. Conclusion:

Contract contain high severity issue. Before deploying the contracts, the developers should fix the highlighted issues.

# 2. High severity issues:

## 2.1. An ambassador can receive a reward 35 times in a row.

### Code snippet
* [Lines 204-216](https://github.com/hydrogen-dev/smart-contracts/blob/608fcaf77c6417855c8b595c799a4eea5609d0a8/decentralization-ambassadors/DA.sol#L204-L216)

```solidity
function recieveHydro() public onlyDA {
    HydroToken hydro = HydroToken(hydroAddress);

    require(hydro.balanceOf(this)  >= payoutHydroAmount);
    uint daLastPayoutBlock = lastPayout[msg.sender];

    if (daLastPayoutBlock + payoutBlockNumber < block.number){
        if (hydro.transfer(msg.sender, payoutHydroAmount)) {
            lastPayout[msg.sender] = daLastPayoutBlock + payoutBlockNumber;
            emit Payout(msg.sender, payoutHydroAmount);
        }
    }
}
```

### Description

When an ambassador requests payout the last payout block is set to `lastPayout[msg.sender]` but the value of it is zero for new ambassadors. And after the payment is made to the value of `lastPayout[msg.sender]` is added 18000. For now the last block number in Ethereum network is 6376948. Thus it turns out that after addition, each ambassador can receive a reward `6376948/18000 = 35` times in a row. Or 7,777,770 Hydro tokens for each ambassador.

### Recommendation

Set `lastPayout[msg.sender]` to `block.number` when an ambassador is added to list.


# 3. Medium severity issues:

## 3.1. Ambassadors Removal

### Description

Any address can call `finalizeRemoval` function member of `Voting` contract to delete an ambassador address, however the owner can call `ownerAddAmbassador` member of `DecentralizationAmbassadors` contract to add the removed address.

### Code snippet

https://github.com/hydrogen-dev/smart-contracts/blob/master/decentralization-ambassadors/Voting.sol#L68#L71

### Recommendation

Add `onlyOwner` modifier.


# 4. Low severity issues:

## 4.1. Nomination Finalization

### Description

The following description can represent a risk depending on the future voting contract to be deployed.
`finalizeNominationVoting` function does not check if the `_ambassador` address is listed in `nominees.members` array.

### Code snippet

https://github.com/hydrogen-dev/smart-contracts/blob/master/decentralization-ambassadors/DA.sol#L167#L173

## 4.2. Ambassadors Possible Abuse

### Description

The future update of the voting system is to leave both contracts completely decentralized, however a fixed amount of tokens is given to every ambassador if the total amount of distributed tokens is not limited or not shared between them, ambassadors can abuse the contracts by adding extra addresses just to get more reward.

### Code snippet

https://github.com/hydrogen-dev/smart-contracts/blob/master/decentralization-ambassadors/DA.sol#L143

https://github.com/hydrogen-dev/smart-contracts/blob/master/decentralization-ambassadors/DA.sol#L211

## 4.3. It is possible to accidentally add `0x0` address to ambassadors list.

### Code snippet
* [Line 98](https://github.com/hydrogen-dev/smart-contracts/blob/608fcaf77c6417855c8b595c799a4eea5609d0a8/decentralization-ambassadors/DA.sol#L98)

### Description

It is possible to accidentally add `0x0` address to ambassadors list. There is no validation in the `insert` function.

### Recommendation

Add `require(other != address(0))`.


# 5. Minor observation:

## 5.1. Decentralized Disbursement Process

### Description

Following the purpose of the contracts described by the devs, check below:

`"Some DAs are working to create a more sophisticated voting contract, which will eventually replace the basic contract. Once the voting contract is updated, the project developers will revoke control of upgrading the voting contract, leaving the disbursement process decentralized"`

In order for the above statement to be true (decentralized disbursement process ), not only the project developers will revoke control of upgrading the voting contract, but they will have to remove any other function giving the necessary administration power to add or remove an ambassador like `ownerAddAmbassador` function member of `DecentralizationAmbassadors`. 

### Code snippet

https://github.com/hydrogen-dev/smart-contracts/blob/master/decentralization-ambassadors/DA.sol#L175#179


# Revealing audit reports:

https://gist.github.com/yuriy77k/4e270681a1c60156851260371afb173c

https://gist.github.com/yuriy77k/2e37fde16326d318a01304fa64568bbb

https://gist.github.com/yuriy77k/58edfcd9090d994f6060df24d8b0bc63

