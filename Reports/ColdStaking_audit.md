### If customer add token to staking, he [lost all rewards for past staking periods](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L51). 

Will be good to add ```claim()``` before ```staker[msg.sender].init_block = block.number;```


### [function withdraw_stake()](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L76-L84) and [function claim()](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L86)

No need to use modifier mutex() if rewrite functions like this:

```
    function withdraw_stake() public only_staker
    {       
        staking_pool = staking_pool.sub(staker[msg.sender].weight);
        uint _weight = staker[msg.sender].weight;
        staker[msg.sender].weight = 0;
        msg.sender.transfer(_weight);
        WithdrawStake(msg.sender, _weight);
    }
```

.transfer did not give enough gas for recall this function, also **only_staker** modifier did not run this function with ```staker[msg.sender].weight = 0;```

```
    function claim() public only_staker
    {
        require(block.number >= staker[msg.sender].init_block.add(round_interval));

        uint256 _reward = stake_reward(msg.sender);
        staker[msg.sender].init_block = block.number;
        msg.sender.transfer(_reward);

        Claim(msg.sender, _reward);
    }
```

.transfer did not give enough gas for recall this function, also ```require(block.number >= staker[msg.sender].init_block.add(round_interval));``` block recall


### Customer get less reward if claim it once after 2 or more periods, then he claim it after each 1 period.

[function staker_weight_stake](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L105-L108) returns a smaller result than a longer period. 
So (stake_reward for 2 periods) <  (stake_reward for 1 period + stake_reward for 1 period) and so on. 


### [function report_abuse(address _addr)](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L110) does not work correctly.

I think it must transfer all ```staker[_addr].weight``` to staker with more then max_delay staking time. But it [transfer calling staker weight](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L115)

It may looks like:

```
    function report_abuse(address _addr) public only_staker
    {
        assert(staker[_addr].weight > 0);
        assert(block.number > staker[_addr].init_block.add(max_delay));
        uint _weight = staker[_addr].weight;
        staker[_addr].weight = 0;
        _addr.transfer(_weight);
    }
```

also no need modifier mutex().

### [function staker_time_stake(address _addr)](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L101) return only whole round intervals.

> 75 days: (current block — init block/round interval) = **2.5** for Staker_1.  Staker_1 claims 0.5 * 2.5 * (1,000/(2,000+1,500)) = 1,875 CLO.  (261 CLO remain in the staking contract).

[Example 2](https://news.callisto.network/callisto-network-cold-staking-protocol/?) So this calculation is incorrect, because can not be **2.5** round interval.


### Modifier [mutex()](https://github.com/EthereumCommonwealth/Cold-staking/blob/82e0c3eade3b65f67656381f7778dead01647701/ColdStaking.sol#L130) is gas-expensive (using much storage space).

If you still need mutex(), better to use this one: https://github.com/OpenZeppelin/openzeppelin-solidity/issues/145
