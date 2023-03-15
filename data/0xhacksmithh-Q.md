### [Low-01] When There are more than 2 Input arrays as function parameter, There should be a check whether these are of same length or not.
```solidity
	function configureTimelockOptions (
		AssetType _assetType,
		uint256[] memory _timelockIds,
		uint256[] memory _encodedSettings 
	) external hasValidPermit(bytes32(uint256(_assetType)), CONFIGURE_TIMELOCKS) {
		for (uint256 i; i < _timelockIds.length; ) {
			timelockOptions[_assetType][_timelockIds[i]] = _encodedSettings[i];
			unchecked { ++i; }
		}
	}
```
There should be a check which ensure ```_timelockIds``` and ```_encodedSettings``` are of same length
*Instances(4)*
```
File: 
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737-L1746
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760-L1773
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783-L1791
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802-L1808
```

### [Low-02] Before making low level call, ensure target contract exist or not.
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L773
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L802
```

### [Low-03] Functions that may lead to DOS(Denial of Service)
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L710-L752
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1264-L1396
```


### [Low-04] Precision loss
Due to rounding and multiplication before division, Code base suffer precision loss in multiple cases
*Instances(10)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L968-L970
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1098
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1388
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1389
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1390
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1391
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1623
```

### [Low-05] Anyone can call ```claimReward()``` for others. Basically there is no access control present here
*Instances(1)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1409-L1411
```

### [Low-06] ```getStakerPosition()``` may get confused with ```getStakerPositions()```
When getStakerPositions() gives `The position of the `_staker` across all asset types.```
where, ```getStakerPosition()``` gives `The list of token IDs of a particular Citizen type that have been staked by `_staker`.`

So during frontend integretion these two function may used by mistakely. 

Recommended to make some name change.
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L689
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L710
```

### [Low-07] ```default``` from Switch case should ```revert``` Otherwise ```withdraw()``` will get successful with invalid ```_assetType``` parameter.
*Instances(1)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1682-L1694
```



### [NC-01] Lack of event emission for critical parameter change
*Instances(10)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1714
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1722
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1743
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760-L1773
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783-L1791
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802-L1808
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1851
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162-L166
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173-L177
```

### [NC-02] Floating Pragma
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2
```


### [NC-03] For Modern and more readable code, update ```import``` usages
*Instances(5)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L6-L8
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L7-L9
```


### [NC-04] ```constructor``` lack zero address checks
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-L85
```


### [NC-05] Mandatory checks for safety 

```solidity
string memory vaultMultiplier = (_vaultId != 0)
```
No checks present whether vaultId is valid or not

```solidity
_pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;
```
No upperbound check for daoTax, while setting it

```solidity
vaultCreditMultiplier[_vaultCreditMultipliers[i]] = _multipliers[i];
```
same no upperbound check here as well

*Instances(7)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L639
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L665
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L290-L292

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1825
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1807
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L75-85
```



### [NC-06] CONSTANT redefine elsewhere 
Use a single file for all system-wide CONSTANTS
*Instances(Multiple instances)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L191-L220
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37-L40
```


### [NC-07] Use ```private``` ```constant``` consistently
*Instances(5)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206-L220
```


### [NC-08] ```constant``` should be value rather than a expression
*Instances(1)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L203
```



### [NC-09] CONSTANT values such as a call to ```keccake()``` should use ```immutable``` rather than ```constant```
*Instances(9)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L191
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L194
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L209
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L214
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L217
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L220
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37-L40
```


### [NC-10] Lock Pragma to specific version
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L2
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L2
```


### [NC-11] Showing actual values (how result comes) in NATSPEC comments makes checking and reading code easier 
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L191
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L194
```


### [NC-12] No need for duplicate ```imports```
```IERC20``` comes automatically with ```ERC20```. 
So need to import it separately.

*Instances(1)*
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L4-L5
```


### [NC-13] Conflicting comment
Function NatSpec says ```@param _bytes The address of the BYTES 2.0 ERC-20 token contract.```
but its actually BYTES 1.0
*Instances(1)*
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L70
```

### [NC-14] Presence of Unnamed parameter in Function
*Instances(4)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1045
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1661
```

### [NC-15] Use of magic numbers
*Instances(1)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1098
```

### [NC-16] There should be a check which ensure updated value should not equal to old one.
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1855
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1856
```