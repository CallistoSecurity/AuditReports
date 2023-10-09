# Native Gaming Security Audit Report

# 1. Summary

[Native Gaming](http://NativeGaming.io) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- /ngbankroll_base.sol
- /ngprivatesale_base.sol
- /ngstaking_base.sol
- /ngcreditline_base.sol
- /eth/ngbankroll.sol
- /eth/ngdeploy.sol
- /eth/ngprivatesale.sol
- /eth/ngstaking.sol
- /eth/ngcashier.sol
- /eth/ngcreditline.sol
- /erc_20/ngbankroll_erc20.sol
- /erc_20/ngcashier_erc20.sol
- /erc_20/ngcreditline_erc20.sol
- /erc_20/ngprivatesale_erc20.sol
- /erc_20/ngstaking_erc20.sol
- /dai/ngdeploy_dai.sol

# 3. Findings

In total, **12 issues** were reported including:

- 1 high severity issues.

- 1 medium severity issues.

- 4 low severity issues.

- 1 notes.

- 5 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

## 3.1. Checking for address(0)

### Severity: low

### Description

NGBankRollBase inherit from DSToken, transfer to address(0) are allowed in DSToken, unless used in any logic transfers to address zero shouldn't be allowed.

Also incoming addresses should be checked for an empty value(`0x0` address) to avoid loss of funds or blocking some functionality in these places:

* `ngbankroll.sol`, lines 24, 35.

* `ngprivatesale_base.sol`, line 107.

* `ngcashier.sol`, lines 73, 101.

* `ngcashier_erc20.sol`, lines 80, 106.

### Code snippet

ds-token/base.sol: 

```
    function transfer(address dst, uint wad) public returns (bool) {
        return transferFrom(msg.sender, dst, wad);
    }
```

ds-token/token.sol: 
```
    function transferFrom(address src, address dst, uint wad)
        public
        stoppable
        returns (bool)
    {
        if (src != msg.sender && _approvals[src][msg.sender] != uint(-1)) {
            require(_approvals[src][msg.sender] >= wad, "ds-token-insufficient-approval");
            _approvals[src][msg.sender] = sub(_approvals[src][msg.sender], wad);
        }

        require(_balances[src] >= wad, "ds-token-insufficient-balance");
        _balances[src] = sub(_balances[src], wad);
        _balances[dst] = add(_balances[dst], wad);

        emit Transfer(src, dst, wad);

        return true;
    }
```

## 3.2. CollectCash NgStacking

### Severity: owner privileges

### Description

Following the contracts implementation "ds-auth/auth.sol" contract will not only allow `ngcashier.sol` contract to call `collectCash` member of `ngstaking.sol` but the contract owner also, meaning that the owner can directly withdraw token from the stakers general balance, leaving them with a negative `dividendsPayoutSnapshots` to be subtracted later on when they withdraw their stake or claim their dividends.

Please note that the same issue is also applicable for the contract handling ERC20 tokens instead of ether for the related contracts.

## 3.3. Ambassador Add/Remove

### Severity: owner privileges

### Description

Ambassadors can be added or removed by the contract owner, an ambassador is eligible for a percentage of the premined tokens (the premined tokens needs ether to be mined that has to be payed by a party that is not known to us, the ambassador itself ot others). Ambassodors has to be aware that they can be removed.

### Code snippet

ngprivatesale_base.sol:

```
    function addAmbassador(address ambassador_, uint256 percent_) public auth {
        require(totalAmbassadorPreminePercent + percent_ <= totalPreminePercent);
        require(ambassadorPreminePercent[ambassador_] == 0);
        ambassadorPreminePercent[ambassador_] = percent_;
        totalAmbassadorPreminePercent += percent_;
        totalAmbassadors++;
    }

    function removeAmbassador(address ambassador_) public auth {
        require(ambassadorPreminePercent[ambassador_] > 0);
        totalAmbassadorPreminePercent -= ambassadorPreminePercent[ambassador_];
        ambassadorPreminePercent[ambassador_] = 0;
        totalAmbassadors--;
    }
```
## 3.4. Pause/Unpause Token

### Severity: owner privileges

### Description

mint/burn/transferFrom/approve and any other function related to them can be stopped/started by the owner since the msg.sender is set to be the owner.

Please note that the same issue is also applicable for the contract handling ERC20 tokens instead of ether for the related contracts.

### Code snippet

ngdeployer.sol:

```
contract NGBankRollFab {
    function newNGBankRoll() public returns (NGBankRollETH) {
        NGBankRollETH ngBankroll = new NGBankRollETH();
        ngBankroll.setOwner(msg.sender);
        return ngBankroll;
    }
}
```

ds-token/token.sol:

```
function approve(address guy) public stoppable returns (bool) {
        return super.approve(guy, uint(-1));
    }

    function approve(address guy, uint wad) public stoppable returns (bool) {
        return super.approve(guy, wad);
    }
    
        function mint(address guy, uint wad) public auth stoppable {
        _balances[guy] = add(_balances[guy], wad);
        _supply = add(_supply, wad);
        emit Mint(guy, wad);
    }
    
    function burn(address guy, uint wad) public auth stoppable {
        if (guy != msg.sender && _approvals[guy][msg.sender] != uint(-1)) {
            require(_approvals[guy][msg.sender] >= wad, "ds-token-insufficient-approval");
            _approvals[guy][msg.sender] = sub(_approvals[guy][msg.sender], wad);
        }

        require(_balances[guy] >= wad, "ds-token-insufficient-balance");
        _balances[guy] = sub(_balances[guy], wad);
        _supply = sub(_supply, wad);
        emit Burn(guy, wad);
    }


    function transferFrom(address src, address dst, uint wad)
        public
        stoppable
        returns (bool)
    {
        if (src != msg.sender && _approvals[src][msg.sender] != uint(-1)) {
            require(_approvals[src][msg.sender] >= wad, "ds-token-insufficient-approval");
            _approvals[src][msg.sender] = sub(_approvals[src][msg.sender], wad);
        }

        require(_balances[src] >= wad, "ds-token-insufficient-balance");
        _balances[src] = sub(_balances[src], wad);
        _balances[dst] = add(_balances[dst], wad);

        emit Transfer(src, dst, wad);

        return true;
    }
```

## 3.5. Investors may not know about the upcoming destruction of the contract

### Severity: owner privileges

### Description

The owner can destroy the contract and get all ETH from it. Although investors have 90 days to sell tokens and receive their funds, they may not know about the upcoming closure. Because there is no emitted event that cage mode has been activated.

### Code snippet

* `ngbankroll_base.sol`, line 56.

### Recommendation

Add some emitting of event to `cage()` function.

## 3.6. Other Owner Privileges

### Severity: owner privileges

### Description

Contract owner allow himself to:

- upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

`ngdeploy.sol` line 81, `ngdeploy_dai.sol` line 92.


- the Private Sale round can be ended by the owner at any moment. 

`ngbankrol_base.sol`, line 75.
```
function setPrivateSaleComplete() public auth {
        privateSaleComplete = true;
}
```

## 3.7. Credit Collateral Lock

### Severity: medium

### Description

The implemented function designed to lock the collaterals does not check if the parameters credit has an owner, meaning that if a wrong credit id is used as input and no owner was set for that credit, the collaterall will be locked until an owner is assigned to that credit id.

Following the implemented `open` function no one can guarantee which credit id is assigned to which address, since credit ids are assigned in order and the `free` function requires the msg.sender to be the credit owner.

### Code snippet

ngcreditline_base.sol:

```
    function open() public note returns (bytes32 credit) {
        require(!cageActivated);
        creditI = add(creditI, 1);
        credit = bytes32(creditI);
        credits[credit].owner = msg.sender;
        emit LogNewCredit(msg.sender, credit);
        return credit;
    }
```

```
    function lock(bytes32 credit, uint tokens) public note {
        require(!cageActivated);
        credits[credit].collateral = add(credits[credit].collateral, tokens);
        ngBankrollBase.pull(msg.sender, tokens);
        ngStakingBase.stake(tokens);
        // at least 0.005 tokens
        require(credits[credit].collateral == 0 ||
        credits[credit].collateral > 0.005 ether);
    }
```

```
    function free(bytes32 credit, uint tokens) public note {
        require(msg.sender == credits[credit].owner);
        credits[credit].collateral = sub(credits[credit].collateral, tokens);
        ngStakingBase.unstake(tokens);
        ngBankrollBase.push(msg.sender, tokens);
        require(safe(credit));
        require(credits[credit].collateral == 0 ||
        credits[credit].collateral > 0.005 ether);
    }
```

## 3.8. Front Running Exploit

### Severity: High

### Description

The Implemented staking logic allow any user to stake at any moment, meaning that if for example a user stake X amount of tokens at a time T1 using `stake` function just before that the team call `distributeTokenDividends` at a time T2, the user will be eligible to claim the dividends distributed even if his staking time `T2-T1 ~= 0. Meaning that the time factor is not included in the staking reward formula.

An attacker can for example trade his tokens on a DEX and if a transaction is propagated to deposit tokens to be distributed using `distributeTokenDividends` or `distributeDividends` by the owner, a front running transaction with a higher gas price can be sent by the attacker to stake his token just before the deposit, the same attacker can later on claim his tokens and reward with a minimum stacking time.

The opposite is also true a staker can withdraw his token if `collectStacking` member of `ngcashier.sol` is about to be called (either if the attacker guess the timing since in general it will be called when the cashier is running out of cash, or a front running attack), thus avoiding his staked tokens balance to be reduced.

Other users are also affected even if this issue is unknown to them since it is unfair to give the same amount of reward to an address that staked its token after.

Following the implemented logic this issue can only be solved by sending more frequent deposit however the gas consumption as presented in issue [Dividends Refill]() will not make such solution possible.

Please note that the same issue is also applicable for the contract handling ERC20 tokens instead of ether for the related contracts.

### Code snippet

ngstaking_base.sol :

```
    function stake(uint _tokenAmount) public {
        increaseInvestorBalance(msg.sender, _tokenAmount);
        ngBankrollBase.pull(msg.sender, _tokenAmount);
        emit Staked(msg.sender, _tokenAmount, block.timestamp);
    }
```

```
   function increaseInvestorBalance(address _src, uint256 _increase) internal {
       investorTokenSnapshot[_src][dividendsPayoutSnapshots.length] =
           add(getInvestorTokenSnapshot(_src), _increase);
       lastTokenBalanceUpdate[_src] = dividendsPayoutSnapshots.length;
   }
```

```
    function unstake(uint _tokenAmount) public {
        require(_tokenAmount <= balanceOf(msg.sender));
        // we require to claim dividends before able to sell
        if (dividendsOf(msg.sender) != 0) {
            claimAllDividendsFor(msg.sender);
        }

        decreaseInvestorBalance(msg.sender, _tokenAmount);
        ngBankrollBase.push(msg.sender, _tokenAmount);

        emit Unstaked(msg.sender, _tokenAmount, block.timestamp);
    }
```
```
    function distributeTokenDividends(uint256 _tokenAmount) public auth {
        require(_tokenAmount > 1 ether); // at least 1 token

        totalSupplySnapshots.push(ngBankrollBase.balanceOf(address(this)));

        ngBankrollBase.pull(msg.sender, _tokenAmount);

        dividendsPayoutSnapshots.push(int256(_tokenAmount));

        emit TokenDistribution(_tokenAmount, block.timestamp);
    }
```
ngcashier.sol:

```
    function collectStaking(uint256 _tokenAmount) public auth {
        require(isUnderLimit());
        ngStaking.collectCash(_tokenAmount);
        snapshotsToday++;
        emit CollectStaking(msg.sender, _tokenAmount, block.timestamp);
    }
```
ngstacking.sol:

```
    function distributeDividends() public payable auth {
        require(ngBankroll.balanceOf(address(this)) > 0);
        require(msg.value >= 0.01 ether);

        totalSupplySnapshots.push(ngBankroll.balanceOf(address(this)));

        // Bankroll purchases tokens as dividends
        uint tokensPurchased = ngBankroll.purchase.value(msg.value)();

        dividendsPayoutSnapshots.push(int256(tokensPurchased));

        emit Distribution(msg.value, tokensPurchased, block.timestamp);
    }
```

```
    function collectCash(uint256 _tokensToSell) public auth {
        require(_tokensToSell > 0);

        totalSupplySnapshots.push(ngBankroll.balanceOf(address(this)));

        uint ethToCollect = ngBankroll.calculateCurrencyReceived(_tokensToSell);

        dividendsPayoutSnapshots.push((-int256(_tokensToSell)));
        ngBankroll.sell(_tokensToSell);
        ngBankroll.withdraw();

        msg.sender.transfer(ethToCollect);

        emit Collection(ethToCollect, _tokensToSell, block.timestamp);
    }
```

# 3.9. Dividends Claim Gas

### Severity: low

### Description

When calling one of the following function implemented in `ngstacking_base.sol`, the transaction might throw for insufficient gas or block gas limit if a user does not claim the stacking dividends for a period where the `dividendsPayoutSnapshots` has gotten long enough since the user last payout claim.
 
`claimAllDividends`
`claimAllDividendsFor`
`unstake`

This issue can be solved by calling `claimDividends` or `claimDividendsFor` multiple times and selecting different ranges every time.

This issue is caused by the for loop that iterate from `startSnapshot` to `endSnapShot` 

## 3.10. Dividends Refill

### Severity: low

### Description

When calling `distributeTokenDividends` to transfers the tokens to be distributed to the stakers, the transferred value of tokens has to be consequent enough so the gas fee to be applied when users call `claimDividendsFor` will be low compared to the distributed tokens for every staker.

This issue is mainly due to the logic implemented when computing the stackers reward or cashier fund (that can be expensive if the tokens to be distributed or taken are repeatedly sent over a short period of time).

### Code snippet

ngstacking_base.sol:

```
    function distributeTokenDividends(uint256 _tokenAmount) public auth {
        require(_tokenAmount > 1 ether); // at least 1 token
        totalSupplySnapshots.push(ngBankrollBase.balanceOf(address(this)));
        ngBankrollBase.pull(msg.sender, _tokenAmount);
        dividendsPayoutSnapshots.push(int256(_tokenAmount));
        emit TokenDistribution(_tokenAmount, block.timestamp);
    }
```

## 3.11. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Please note that NGBankRollBase inherit DSToken that is ERC20 implementation, everyone using ERC20 standard should be aware of the risks.

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.12. Overflow is possible

### Severity: notes

### Description

1. The owner can call `addAmbassador()` with huge value of `percent_` parameter and the following condition will be passed: `require(totalAmbassadorPreminePercent + percent_ <= totalPreminePercent)` due to overflow. Also received huge percent will allow the ambassador to withdraw all tokens available on the balance of this contract after the Privat Sale due to the following condition in `withdrawTokens()` function:
```Solidity
    // for last person additional check
    if (bankrollTokens > ngBankrollBase.balanceOf(address(this))) {
        bankrollTokens = ngBankrollBase.balanceOf(address(this));
    }
```

That is, the huge balance of the Ambassador will not cause an exception.

2. Also theoretical probability of overflow is possible when calculating withdrawal of Private Sale:
```solidity
bankrollTokens += privateSaleInvestment[msg.sender] * totalPrivateSaleTokens / totalPrivateSaleInvestment;
```

### Code snippet

1. `ngprivatesale_base.sol`, line 111.
2. `ngprivatesale_base.sol`, line 61.

# 4. Conclusion

The audited smart contract must not be deployed. Reported issues must be fixed prior to the usage of this contract.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/f884769fbb91237c9c7f271f56bbc55d

https://gist.github.com/yuriy77k/3f986f1cafda162d8b480a323ccc6776

https://gist.github.com/yuriy77k/ff498dbb687d78f8dafc468953a6d2ff
