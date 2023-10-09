# Native Gaming V2 Security Audit Report

# 1. Summary

[Native Gaming V2](https://www.nativegaming.io/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

- all audited contracts were provided privately, please note that in the goal of performing a good audit all ds-contracts have been audited also.

# 3. Findings

In total, **10 issues** were reported including:

- 4 low severity issues.

- 2 notes.

- 4 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

No critical security issues were found.


## 3.1. Possible Transfer to a Zero address

### Severity: note

### Description

Stock Pool Wallet should be set in the contract constructor to avoid sending tokens to address(0).

### Code snippet

ngprivatesale_base.sol:

```Solidity
    function withdrawStockPool() public auth {
        require(totalAmbassadors == 0); // all ambassadors withdrawn

        uint256 tokensToWithdraw = stockPoolTokens;
        stockPoolTokens = 0;

        // in case withdrawn last
        if (tokensToWithdraw > ngBankrollBase.balanceOf(address(this))) {
            tokensToWithdraw = ngBankrollBase.balanceOf(address(this));
        }

        ngBankrollBase.push(stockPoolWallet, tokensToWithdraw);
    }
```
As it can be seen here , the address is not set in the constructor:

ngprivatesale_base.sol

```Solidity
    constructor(NGBankRollBase ngBankroll_,
            uint256 totalPremineInvestment_) public {
        ngBankrollBase = ngBankroll_;
        totalPremineInvestment = totalPremineInvestment_;
    }
```
but later using a different function.

## 3.2. transfers to Address(0)

### Severity: low

### Description

NGBankRollBase inherit from DSToken, transfer to address(0) are allowed in DSToken, unless used in any logic transfers to address zero shouldn't be allowed.

### Code snippet

ds-token/base.sol: 

```Solidity
    function transfer(address dst, uint wad) public returns (bool) {
        return transferFrom(msg.sender, dst, wad);
    }
```

ds-token/token.sol: 
```Solidity
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

## 3.3. Owner Privileges

### Severity: owner privileges

### Description

Contract owner allow himself to:


1. the Private Sale round can be ended by the owner at any moment.

ngbankrol_base.sol, line 86.

        function setPrivateSaleComplete() public auth {
        privateSaleComplete = true;
        }

2. upgrade contract and implement any logic in the new contract. And even if the new contract will be audited, at any time possible to change the address of the new contract again to not audited and insecure.

ngdeploy.sol line 82, ngdeploy_dai.sol line 94,
ngdeploy_wbtc.sol line 93.


3. Following the contracts implementation "ds-auth/auth.sol" contract will not only allow `ngcashier.sol` contract to call `collectCash` member of `ngstaking.sol` but the contract owner also, meaning that the owner can directly withdraw token from the stakers general balance, leaving them with a negative `dividendsPayoutSnapshots` to be subtracted later on when they withdraw their stake or claim their dividends.
Please note that the same issue is also applicable for the contract handling ERC20 tokens instead of ether for the related contracts.

4.  mint/burn/transferFrom/approve and any other function related to them can be stopped/started by the owner since the msg.sender is set to be the owner.
Please note that the same issue is also applicable for the contract handling ERC20 tokens instead of ether for the related contracts.

### Code snippet

ngdeployer.sol:

```Solidity
contract NGBankRollFab {
    function newNGBankRoll() public returns (NGBankRollETH) {
        NGBankRollETH ngBankroll = new NGBankRollETH();
        ngBankroll.setOwner(msg.sender);
        return ngBankroll;
    }
}
```

ds-token/token.sol:

```Solidity
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

## 3.4. Checking for address(0)

### Severity: low

### Description

Also incoming addresses should be checked for an empty value(0x0 address) to avoid loss of funds or blocking some functionality.

### Code snippet

ngconcierge.sol line 23.

        function setConcierge(address _newConcierge) public auth {
        require(concierge != _newConcierge);
        address oldConcierge = concierge;
        concierge = _newConcierge;
        emit ConciergeUpdate(oldConcierge, concierge);
        }


## 3.5. Dividends Claim Gas

### Severity: low

### Description

When calling one of the following function implemented in `ngstacking_base.sol`, the transaction might throw for insufficient gas or block gas limit if a user does not claim the stacking dividends for a period where the `dividendsPayoutSnapshots` has gotten long enough since the user last payout claim.
 
`claimAllDividends`
`claimAllDividendsFor`
`executeUnstake`

This issue can be solved by calling `claimDividends` or `claimDividendsFor` multiple times and selecting different ranges every time.

This issue is caused by the for loop that iterate from `startSnapshot` to `endSnapShot` 

## 3.6. Dividends Refill

### Severity: note

### Description

When calling `distributeTokenDividends` to transfers the tokens to be distributed to the stakers, the transferred value of tokens has to be consequent enough so the gas fee to be applied when users call `claimDividendsFor` will be low compared to the distributed tokens for every staker.

This issue is mainly due to the logic implemented when computing the stackers reward or cashier fund (that can be expensive if the tokens to be distributed or taken are repeatedly sent over a short period of time).

### Code snippet

ngstacking_base.sol:

```Solidity
    function distributeTokenDividends(uint256 _tokenAmount) public auth {
        require(_tokenAmount >= 1 ether); // at least 1 token

        totalSupplySnapshots.push(ngBankrollBase.balanceOf(address(this)));

        ngBankrollBase.pull(msg.sender, _tokenAmount);

        dividendsPayoutSnapshots.push(int256(_tokenAmount));

        emit TokenDistribution(_tokenAmount, block.timestamp);
    }
```

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Please note that NGBankRollBase inherit DSToken that is ERC20 implementation, everyone using ERC20 standard should be aware of the risks.

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)# 4. Conclusion

# Conclusion

The audited smart contract can be deployed. Only low severity issues were found during the audit.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/fa5e3900ae7268f7e8a05da7927af472

https://gist.github.com/yuriy77k/aa6f70708c04dc8c89ffaf7a5385e263

https://gist.github.com/yuriy77k/21bf4ab7abe4bde7393e126d4a6c779f
