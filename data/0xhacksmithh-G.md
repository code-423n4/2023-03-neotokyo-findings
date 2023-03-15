### [Gas-01] State Variables should be cached in stack variable rather than re-reading them from storage
*Instances(8)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L716
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L718

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L733-L734

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1279
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1280

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1289
```

### [Gas-02] Function that guaranteed to revert when called by normal users can be marked as ```payable```
*Instances(11)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1851
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1802
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1783
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1737
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1721
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1708
```
```
File: File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L140
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L162
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L173
```

### [Gas-03] Multiple addresses mapping can be combined into a single mapping of an addresses to a ```struct``` where appropriate. 
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.
*Instances(5)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L280
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L319
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L326
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L372
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L405
```

### [Gas-04] Code base making 2 times contract interaction
In contract ```NeoTokyoStaker.sol``` Function ```withdraw()``` calls ```IByteContract(BYTES).getReward(msg.sender);``` during withdrawing.

Then after contract ```BYTES2.sol``` call ```NeoTokyoStaker.sol``` via
```solidity
               (
			uint256 reward,
			uint256 daoCommision
		) = IStaker(STAKER).claimReward(_to); 
```

Instead if we make some logic change we call directly claimReward() inside withdraw() and then pass corresponding parameter to BYTES.sol contract respectively.
*Instances(1)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1673
```
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L120
```



### [Gas-05] Avoid compound assignment operator in state variables 
<x> += <y> costs more than <x> = <x> + <y> for state variables
*Instances(15)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1029
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1078
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1079
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1080
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1099
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1100
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1101
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1160
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1161
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1164
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1515
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1626
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1627
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1630
```

### [Gas-06] Empty block should be removed or ```emit``` something
*Instances(2)*
```
File: contracts/staking/BYTES2.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L189-L194
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L204-L208
```

### [Gas-07] Making ```constant``` variable ```private``` will save gas during deployment.
*Instances(5)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206-L220
```

### [Gas-08] Use ```assembly``` to check for address(0x0) 
Saves 6 gas per instance if using assembly to check for address(0)
```
File: contracts/staking/NeoTokyoStaker.sol
File: contracts/staking/BYTES2.sol
```

### [Gas-09] ```Struct``` can be packed into fewer storage slots
*Instances(7)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L290-L292
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L308-L311
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L360-L363
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L394-L398
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L423-L428
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L462-L468
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L486-L490
```

### [Gas-10] Optimize names to save gas 
public/external function names and public member variable names can be optimized to save gas. See this link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted.
*Instances(Multiple Instances)*
```
File: contracts/staking/NeoTokyoStaker.sol
File: contracts/staking/BYTES2.sol
```

### [Gas-11] May use of ```Switch``` case increase code readability and save gas
*Instances(1)*
```
File: contracts/staking/NeoTokyoStaker.sol
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L693-L699
```


### [Gas-12] Use ```functions``` instead of ```modifier```
```
File: contracts/staking/NeoTokyoStaker.sol
File: contracts/staking/BYTES2.sol
```



### [Gas-13] Setting ```constructor``` to ```payable``` can save gas
Saves ~13 gas per instances.
*Instances(2)*
```
File: contracts/staking/NeoTokyoStaker.sol
File: contracts/staking/BYTES2.sol
```






