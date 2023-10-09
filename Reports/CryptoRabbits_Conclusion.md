# 1. Summary

[CryptoRabbits](https://etherscan.io/address/0x35ea9df0b7e2e450b1d129a6f81276103b84f3dc#code) security audit report performed by [Callisto Security Audit Department](https://github.com/EthereumCommonwealth/Auditing)

# 2. In scope

https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab

the same as
https://etherscan.io/address/0x35ea9df0b7e2e450b1d129a6f81276103b84f3dc#code

# 3. Findings

In total, **11 issues** were reported including:

- 1 high severity issues.

- 3 medium severity issues.

- 5 low severity issues.

- 2 minor observation.


## 3.1. Blockchain Data is Public

### Severity: low

### Description

Not using the `public` specifier for `privAddress` and `addressAdmixture` do not make the addresses not readable, and using special getters to read the addresses with `onlyOwner` modifier do not make not readable by an external attacker.

The contract addresses can be easily found by just reading the input parameters of `setPriv` and `setAdmixture`, or directly reading the addresses from the blockchain.

Contract bytecode can be converted to opcodes by a possible attacker to find a possible exploit.

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L32

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L31

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L70#L72

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L73#L75

## 3.2. Genes Modification

### Severity: High

### Description

`setPriv` and `setAdmixture` functions are set with `public` specifier without any modifier restricting the access to them, any attacker can directly call these functions to set `privAddress` and `addressAdmixture` to new contract addresses that he deployed. This issue will allow an attacker to manipulate the output of the following functions:

```
contract PrivateRabbitInterface {
    function getNewRabbit(address from)  public view returns (uint);
    function mixDNK(uint dnkmother, uint dnksire, uint genome)  public view returns (uint);
    function isUIntPrivate() public pure returns (bool);
}

contract AdmixtureInterface {
    function getAdmixture(uint m, uint w)  public view returns (uint procentAdmixture, uint admixture);
}

```

For example, if an attacker can succeed to analyze the DNK (DNA) of each bunny and understand how it is set to give the special characteristics for each bunny, he will be able to set a customized DNK value at his will (allowing himself a better bunny that he can sell or propose as a sire to get a higher ether reward). 
Previous breeding games genes have been cracked, but here they can be exploited directly, not setting the restriction to `onlyOwner` will allow a direct exploit. 

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L378#L381

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L382#L385

## 3.3. Approve/transferFrom Mechanism

### Severity: medium

### Description

`approve` and `transferFrom` logic should allow a bunny tokenID owner to approve the transfer for another address, however in the contract approve doesn't not change any state variables, instead `transferFrom` allow white listed addresses to transfer bunny tokenId from owner account to a destination address without permission from the owner. 
The developers should clarify why they used approve/transferFrom for this purpose since it clearly doesn't follow the mechanism logic and will mislead the users.

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L407#L411

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L467#L478

## 3.4. `onlyWhitelisted`/`onlyOwner` Issue

### Severity: medium

### Description

When using `transferOwnership` function to change `ownerCEO` address, the new address should be white listed otherwise `addAddressesToWhitelist` function will throw even if it is set with `onlyOwner` modifier since it calls `addAddressToWhitelist` that allow only whitelisted addresses.

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L52

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L141#L147

### Recommendation

Add the following function to `Whitelist` contract to override `transferOwnership` function member of `Ownable` contract:
```
    function transferOwnership(address add) public onlyOwner {
        require(add != address(0));
	whitelist[ownerCEO] = false;
 	whitelist[add] = true;
	super.transferOwnership(add);
	
    }
```

## 3.5. Percentage Conversion 

### Severity: low

### Description

When computing X% from a value, the value should first be multiplied, then divided otherwise the final result will be truncated following the denominator value. 

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L576

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L577

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L820

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L435

## 3.6. Gift

### Severity: low

### Description

Only whitelisted addresses can transfer their bunnies if they own any that is not blocked, `gift` function call `transferFrom` that accept only whitelisted addresses. Following the contract logic whitelisted addresses are for administrators only, therefore users without administration privileges cannot use `gift`.

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L933#L938

## 3.7. Unnecessary Condition

### Severity: minor observation

### Description

`count` is always higher than zero since if it was zero the loop won't execute, and `count` cannot be equal to `(i-1)` since `i` is always lower than `count`, even in case of underflow of `(i-1)` if `i=0` the condition will always be true.

### Code snippet

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L420

https://gist.github.com/RideSolo/c70d71e03845ea587458e21f04783867#file-cryptorabbits-sol-L913

## 3.8. The name of the function distorts the meaning.

### Severity: low

### Description

These functions return the opposite value they names are talking about:

* [`getGiffBlock`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L580) returns `!giffblock[_bunnyid]`.

* [`isPauseSave`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L481) returns `!pauseSave`.

* [`isPromoPause`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L485) returns `!promoPause`.

This can lead not only to the developer's error, but also to misinformation of a player when calling the function directly.

## 3.9. The mother of newborn rabbit loses the list of her "mothers".

### Severity: medium

### Code snippet

* [CryptoRabbits.sol, line 512](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L512)

### Description

During the compilation of an array of "mothers" of a newborn rabbit, the `rabbitMother` for his mother is erased. Accordingly, when repeated pairing this female the reward to her "mothers" is lost.

## 3.10. Zero address checking require. 

### Severity: low

### Description

In functions [`setPriv`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L378) and [`setAdmixture`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L382) there are possibility of setting equal to zero address.

### Recommendation

Add checking for zero address.
```solidity
require(_to != address(0));
```

## 3.11. Function takes wrong argument. 

### Severity: minor observation

### Description

In function [`getMotherCount`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L685) and [`getRabbitMother`](https://gist.github.com/yuriy77k/3b1a3953705e5a75a8d2c1be381f67ab#file-cryptorabbits-sol-L695) there should be passed child, but passed mother.

### Recommendation

Function should take child as argument, not mother.

# 4. Conclusion

A high severity issue was highlighted and should be fixed. The contract contains multiple other severity issues that need to be handled by the project team.


# 5. Revealing audit reports

https://gist.github.com/yuriy77k/7d80093ac7041933f6e7416f59477f4d

https://gist.github.com/yuriy77k/ff880105debbbd75c18958cd9a35d5b7

https://gist.github.com/yuriy77k/8f6ad0c5d1cf2cec22e2d717c0f32a12
