# UNUS SED LEO token Security Audit Report

# 1. Summary

[UNUS SED LEO token](https://coinmarketcap.com/currencies/unus-sed-leo/) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

* https://etherscan.io/address/0x2af5d2ad76741191d15dfe7bf6ac92d4bd912ca3#code (LEO token contract)
* https://etherscan.io/address/0xf17ebb3a24dc6d6b56d38adf0df499c1cd9e5672#code (LEO token contract controller at the moment of the security audit)

# 3. Findings

In total, **7 issues** were reported including:

- 3 low severity issues.

- 2 owner privileges (the ability of an owner to manipulate contract, may be risky for investors).

- 2 minor observation / non-security issue

No critical security issues were found.

## 3.1 proxyPayment function syntax

### Severity: minor observation / non-security issue

### Code snippet

```js
    function proxyPayment(address _owner) public payable returns(bool allowed) {
        allowed = false;
    }
```

### Description

Use of different syntax for returning functions.
  
### Recommendation

It is recommended to use the same syntax for all code fragments in order to facilitate readability. If the other fragments use the `return false;` syntax , then it is recommended to use the same syntax in this function too.

## 3.2 Balances in the future

### Severity: Low

### Code snippet

```js

    function balanceOfAt(address _owner, uint _blockNumber) public view
        returns (uint) {
        if ((balances[_owner].length == 0)
            || (balances[_owner][0].fromBlock > _blockNumber)) {
            if (address(parentToken) != address(0)) {
                return parentToken.balanceOfAt(_owner, min(_blockNumber, parentSnapShotBlock));
            } else {
                return 0;
            }
        } else {
            return getValueAt(balances[_owner], _blockNumber);
        }
    }

```

```js

    function totalSupplyAt(uint _blockNumber) public view returns(uint) {
        if ((totalSupplyHistory.length == 0)
            || (totalSupplyHistory[0].fromBlock > _blockNumber)) {
            if (address(parentToken) != address(0)) {
                return parentToken.totalSupplyAt(min(_blockNumber, parentSnapShotBlock));
            } else {
                return 0;
            }
        } else {
            return getValueAt(totalSupplyHistory, _blockNumber);
        }
    }

```

```js

    function getValueAt(Checkpoint[] storage checkpoints, uint _block
    ) view internal returns (uint) {
        if (checkpoints.length == 0) return 0;
        if (_block >= checkpoints[checkpoints.length-1].fromBlock)
            return checkpoints[checkpoints.length-1].value;
        if (_block < checkpoints[0].fromBlock) return 0;

        // Binary search of the value in the array
        uint min = 0;
        uint max = checkpoints.length-1;
        uint mid = 0;
        while (max > min) {
            mid = (max + min + 1)/ 2;
            if (checkpoints[mid].fromBlock<=_block) {
                min = mid;
            } else {
                max = mid-1;
            }
        }
        return checkpoints[min].value;
    }

```

### Description

Balances may be requested for blocks that have not yet been mined. In chis case the contract will return the actual balance of the queried address while it is not a correct information because the contract does not know the balance of the queried address in the future.

This can not harm the LEO token contract, but this may confuse third party services that work with `totalSupplyAt` or `balanceOfAt` functions.

## 3.3 Void receiveApproval function

### Severity: Low

### Code snippet

```js

    function approveAndCall(address _spender, uint256 _amount, bytes memory _extraData
    ) public returns (bool success) {
        require(approve(_spender, _amount));

        ApproveAndCallFallBack(_spender).receiveApproval(
            msg.sender,
            _amount,
            address(this),
            _extraData
        );

        return true;
    }

```

```js

contract ApproveAndCallFallBack {
    function receiveApproval(address from, uint256 _amount, address _token, bytes memory _data) public;
}

```

### Description

It would be better if the receiveApproval function returns `true` on successful execution. Otherwise, permissive fallback function may handle the receiveApproval invocation without causing the transaction to fail even if the receiver contract does not implement `receiveApproval` function.

## 3.4 TokenFactory is not upgradeable

### Severity: minor observation / non-security issue

### Description

The default minime token is designed to by upgradeable, but the TokenFactory contract which is responsible for the upgrading process does not allow for implementation of new features/functions as it is only deploying the same version of the MinimeToken contract.

The address of TokenFactory can not be updated in the deployed contract.

## 3.5 Owner may control the emission manually

### Severity: Owner privileges / non-security issue

### Code snippet

```js

    /// @notice `onlyOwner` can upgrade the controller contract
    /// @param _newControllerAddress The address that will have the token control logic
    function upgradeController(address _newControllerAddress) public onlyOwner {
        tokenContract.changeController(_newControllerAddress);
        emit UpgradedController(_newControllerAddress);
    }

```

```js

    function generateTokens(address _owner, uint _amount
    ) public onlyController returns (bool) {
        uint curTotalSupply = totalSupply();
        require(curTotalSupply + _amount >= curTotalSupply); // Check for overflow
        uint previousBalanceTo = balanceOf(_owner);
        require(previousBalanceTo + _amount >= previousBalanceTo); // Check for overflow
        updateValueAtNow(totalSupplyHistory, curTotalSupply + _amount);
        updateValueAtNow(balances[_owner], previousBalanceTo + _amount);
        emit Transfer(address(0), _owner, _amount);
        return true;
    }

```

```js

    function destroyTokens(address _owner, uint _amount
    ) onlyController public returns (bool) {
        uint curTotalSupply = totalSupply();
        require(curTotalSupply >= _amount);
        uint previousBalanceFrom = balanceOf(_owner);
        require(previousBalanceFrom >= _amount);
        updateValueAtNow(totalSupplyHistory, curTotalSupply - _amount);
        updateValueAtNow(balances[_owner], previousBalanceFrom - _amount);
        emit Transfer(_owner, address(0), _amount);
        return true;
    }

```

### Description

Owner can upgrade the `controller` contract without any restrictions.

Theoretically, the `controller` contract  can be upgraded so that it can mint new tokens or burn any tokens at any address. If this happens, transactions will be available for viewing to everyone.

## 3.6. Zero address checking

### Severity: low

### Code snippet

```js
    function changeController(address _newController) public onlyController {
        emit ControlTransferred(controller, _newController);
        controller = _newController;
    }
```

### Description

There are no zero address checking in functions `changeController` in line 76.

## 3.7. Owner privileges

### Severity: Owner privileges / non-security issue

### Description

Owner can disable transfers at any time he wants.

## 4. Conclusion

The reviewed smart-contract does not contain any medium or high severity security vulnerabilities or other issues. This contract can be used without any serious risk from technical side.

- LEO token is compatible with [ERC20 token standard](https://en.wikipedia.org/wiki/ERC-20).

- LEO token is **NOT** prone to [common ERC20 security vulnerabilities](https://medium.com/@dexaran820/erc20-token-standard-critical-problems-3c10fd48657b).

- LEO token is **NOT** prone to [re-approval attack](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit).
  
# 5. Revealing audit reports

https://gist.github.com/Dexaran/eefd2937ee3859f85e9aaaeed2c79a1a

https://gist.github.com/yuriy77k/05b3feee64c2b73be4fdbac74d4421d4

https://gist.github.com/yuriy77k/0772c6a85317d2fce4de2aaff2ea087f
