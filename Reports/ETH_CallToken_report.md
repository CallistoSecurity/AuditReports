# Call Token Security Audit Report

# 1. Summary

[Call Token](https://gcalliance.io/wp-content/uploads/call-master-june19.zip) smart contract security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

Following files from https://gcalliance.io/wp-content/uploads/call-master-june19.zip

- CALL.sol
- CStore.sol
- ERC1820Client.sol
- IERC664.sol
- IERC777.sol
- IERC777TokensRecipient.sol
- IERC777TokensSender.sol
- ERC664Balances.sol
- SafeGuard.sol
- ERC777.sol
- ERC777ERC20Compat.sol
- ERRC777RemoteBridge.sol

# 3. Findings

**8 issues** were reported:

- 1 medium severity issue.
- 2 low severity issues.
- 4 owner privileges.
- 1 note.

## 3.1. Naming Error

### Severity: note

### Description 

ERC664Balances.sol is based on ERC644 not ERC664 (erc664 does note exist in https://github.com/ethereum/EIPs/issues/664), check [here](https://github.com/ethereum/EIPs/issues/644) for further detail.

## 3.2. Default Operators

### Severity: owner privilege

### Description

Default operators are allowed by default to manage ERC777 tokens holders fund, investors should be aware that their fund can be managed by addresses that are sent at contract creation by the contract owner.

Please note that default operators are define by ERC777 following some specific recommendations.

### Code snippet

token/ERC777.sol:

```
    constructor(
        string memory _name,
        string memory _symbol,
        uint256 _granularity,
        address[] memory _defaultOperators
    ) internal {
        mName = _name;
        mSymbol = _symbol;
        mTotalSupply = 0;
        require(_granularity >= 1, "Granularity must be > 1");
        mGranularity = _granularity;

        mDefaultOperators = _defaultOperators;
        
        for (uint256 i = 0; i < mDefaultOperators.length; i++) { mIsDefaultOperator[mDefaultOperators[i]] = true; }

        setInterfaceImplementation("ERC777Token", address(this));
    }
```

```
    function authorizeOperator(address _operator) external {
        require(_operator != msg.sender, "Cannot authorize yourself as an operator");
        if (mIsDefaultOperator[_operator]) {
            mRevokedDefaultOperator[_operator][msg.sender] = false;
        } else {
            mAuthorizedOperators[_operator][msg.sender] = true;
        }
        emit AuthorizedOperator(_operator, msg.sender);
    }
```

```
    function revokeOperator(address _operator) external {
        require(_operator != msg.sender, "Cannot revoke yourself as an operator");
        if (mIsDefaultOperator[_operator]) {
            mRevokedDefaultOperator[_operator][msg.sender] = true;
        } else {
            mAuthorizedOperators[_operator][msg.sender] = false;
        }
        emit RevokedOperator(_operator, msg.sender);
    }
```

```
    function isOperatorFor(address _operator, address _tokenHolder) public view returns (bool) {
        return (_operator == _tokenHolder // solium-disable-line operator-whitespace
        || mAuthorizedOperators[_operator][_tokenHolder]
        || (mIsDefaultOperator[_operator] && !mRevokedDefaultOperator[_operator][_tokenHolder]));
    }
```
## 3.3. ERC777 Compliance

### Severity: medium

### Description

defaults operators as defined in [EIP777](https://eips.ethereum.org/EIPS/eip-777) must:
- The token contract MUST define default operators at creation time.
- The default operators MUST be invariants. I.e., the token contract MUST NOT add or remove default operators ever.

As described the defaults operators must be invariant, however "CStore" contract contain a function named `setDefaultOperator` that allow addresses that satisfies `onlyModule` to add new default operators to the contract after creation.

Please note that CStore if further used in ERC777 implementation:

- ERC777RemoteBridge.sol
- ERC777ERC20Compat.sol
- CALL.sol

### Code snippet

CStore.sol:

```
    function setDefaultOperator(address _operator) external
    onlyModule
    returns (bool) {
        if(!mIsDefaultOperator[_operator]) {
            mIsDefaultOperator[_operator] = true;
            mDefaultOperators.push(_operator);
        }
        return true;
    }
```

## 3.4. ERC644 Compliance

### Severity: low

### Description

The original implementation of ERC644 ([here](https://github.com/ethereum/EIPs/issues/644)) implement `incTotalSupply` and `decTotalSupply` to be accessed by addresses that satisfies `onlyModule` modifier not `onlyOwner` modifier (please note that `onlyModule` is implemented and used in different functions). 

Please note that this issue is set as low since the concerned function are disabled later on before inherited by CALL.sol, but this issue should be noted to avoid future breach of operability.

### Code snippet

misc/ERC664Balances.sol:

```
    modifier onlyModule() {
        require(modules[msg.sender]);
        _;
    }
    
    function incTotalSupply(uint _val) external onlyOwner returns (bool) {
        totalSupply = totalSupply.add(_val);
        return true;
    }

    function decTotalSupply(uint _val) external onlyOwner returns (bool) {
        totalSupply = totalSupply.sub(_val);
        return true;
    }
```

## 3.5. Total Access to Token Holders funds

### Severity: owner privileges

### Description

As it can be seen in the constructor of CALL token, the owner of `balancesDB` that represent `CStore` contract is set to initial owner that can be an address managed by a human, CStore inherit from ERC664Balance.sol. this will give full access for the owner to multiple function:

- setModule
- setApprove
- decApprove
- setAuthorizedOperator
- setDefaultOperator
- setRevokedDefaultOperator
- move 

The owner does not need access to all these functions, since as decribed in "ERC777 Compliance" issue the default operators must not be changed, and all other functions should be only managed by the investors not by the owner. The above mentioned function give full access to the owner to manage the tokens holders fund without any permission from them.

Investors should be aware that they are not 100% in control of their assets.

### Recommendation

This issue can be avoided by just removing  "balancesDB.transferOwnership(_initialOwner);" line from CALL.sol contructor.

### Code snippet

CALL.sol:

```
constructor(
        string memory _name,
        string memory _symbol,
        uint256 _granularity,
        uint256 _totalSupply,
        address _initialOwner,
        address[] memory _defaultOperators
    )
    public ERC777ERC20Compat(_name, _symbol, _granularity, _totalSupply, _initialOwner, _defaultOperators)
    {
        requireMultiple(_totalSupply);
        require(balancesDB.setModule(address(this), true), "Cannot enable access to the database.");
        balancesDB.transferOwnership(_initialOwner);

        callRecipient(msg.sender, address(0), _initialOwner, _totalSupply, "", "", true);

        emit Minted(msg.sender, _initialOwner, _totalSupply, "", "");
        if (mErc20compatible) { emit Transfer(address(0), _initialOwner, _totalSupply); }
    }

```

CStore.sol:

```
    function setAuthorizedOperator(address _operator, address _tokenHolder, bool _status) external
    
    returns (bool) {
        mAuthorizedOperators[_operator][_tokenHolder] = _status;
        return true;
    }

```
```
    function move(address _from, address _to, uint256 _amount) external
    onlyModule
    returns (bool) {
        balances[_from] = balances[_from].sub(_amount);
        emit BalanceAdj(msg.sender, _from, _amount, "-");
        balances[_to] = balances[_to].add(_amount);
        emit BalanceAdj(msg.sender, _to, _amount, "+");
        return true;
    }
```
```
    function setDefaultOperator(address _operator) external
    onlyModule
    returns (bool) {
        if(!mIsDefaultOperator[_operator]) {
            mIsDefaultOperator[_operator] = true;
            mDefaultOperators.push(_operator);
        }
        return true;
    }
```

```
    function setRevokedDefaultOperator(address _operator, address _tokenHolder, bool _status) external
    onlyModule
    returns (bool) {
    mRevokedDefaultOperator[_operator][_tokenHolder] = _status;
        return true;
    }
```

ERC664Balances.sol:

```
    function setApprove(address _sender, address _spender, uint256 _value) external onlyModule returns (bool) {
        allowed[_sender][_spender] = _value;
        return true;
    }

    function decApprove(address _from, address _spender, uint _value) external onlyModule returns (bool) {
        allowed[_from][_spender] = allowed[_from][_spender].sub(_value);
        return true;
    }

    function setModule(address _acct, bool _set) external onlyOwner returns (bool) {
        modules[_acct] = _set;
        emit ModuleSet(_acct, _set);
        return true;
    }

```


## 3.6. Total Access to Token Holders funds

### Severity: owner privileges

### Description

The owner allow himself to change `balancesDB` in the CALL.sol contract, the balancesDB is an instance of CStore.sol that saves all token holders information if change the holders will loose access to their tokens and the owner will be able to rewrite token holders data.

### Code snippet

CALL.sol:

```
    function changeBalancesDB(address _newDB) public onlyOwner {
        balancesDB = CStore(_newDB);
    }
```

## 3.7. Known vulnerabilities of ERC-20 token

### Severity: low

### Description

Please note that ERC777ERC20Compat.sol readapt ERC777 and ERC644 functions to ERC20, making the token vulnerable to the following known issue. also please note that ERC777 by itself does not completely solve the following descriptions.

1. It is possible to double withdrawal attack. More details [here](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM/edit)
2. Lack of transaction handling mechanism issue. [WARNING!](https://gist.github.com/Dexaran/ddb3e89fe64bf2e06ed15fbd5679bd20) This is a very common issue and it already caused millions of dollars losses for lots of token users! More details [here](https://docs.google.com/document/d/1Feh5sP6oQL1-1NHi-X1dbgT3ch2WdhbXRevDN681Jv4/edit)

## 3.8. Ability to disable/enable ERC20Token

### Severity: owner privileges

### Description

Owner can disable/enable all ERC20 functions (transfer, trnsferFrom, approve).

### Code snippet

CALL.sol, line 40, 49.:

```Solidity
    /**
     * @notice Disables the ERC20 interface. This function can only be called
     * by the owner.
     */
    function disableERC20() public onlyOwner {
        mErc20compatible = false;
        setInterfaceImplementation("ERC20Token", address(0));
    }

    /**
     * @notice Re enables the ERC20 interface. This function can only be called
     *  by the owner.
     */
```

# 4. Conclusion

The Highlighted issues should be fixed before deployment.

# 5. Revealing audit reports

https://gist.github.com/yuriy77k/4fb0bde46c348981e1ec690be43af3f6

https://gist.github.com/yuriy77k/37713f0303f150687ed8999aeb6ebabf

https://gist.github.com/yuriy77k/0f11112f230ac4b727f11ae09751062f
