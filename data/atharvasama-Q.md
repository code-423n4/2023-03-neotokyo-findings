# LOW RISK AND NON-CRITICAL ISSUES

## [01] CHECK FOR ```address(0)```
The ```getReward``` function in BYTES2 does not check if ```address _to``` is 0. Similarly ```claimReward``` function in NeoTokyoStaker does not check if the ```address _recipient``` is 0 either.

[contracts\staking\BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol)
```js
114: 	function getReward ( 
115: 		address _to 
116: 	) external { 
117: 		(
118: 			uint256 reward,
119: 			uint256 daoCommision
120: 		) = IStaker(STAKER).claimReward(_to);
```

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```js
1409: 	function claimReward (
1410: 		address _recipient
1411: 	) external returns (uint256, uint256) {
```

## [02] CHANGE ORDER OF OPERANDS TO AVOID OVERFLOW
Even though it is impossible for an overflow because of the maximum amount of tokens in circulation, but the order of operands could be changed for safety measures. 

[contracts\staking\BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol)
```js
152: 		unchecked { 
153: 			treasuryShare = _amount * 2 / 3; 
154: 		}
```
can be refactored to 
```js
152: 		unchecked { 
153: 			treasuryShare =  _amount / 3 * 2;
154: 		}
```

## [03] ```AssetType``` CHECK MISSES OUT ON INVALID VALUE
```AssetType``` has ids from 0-3 each representing S1 Citizen, S2 citizen, BYTES and LP tokens respectively. ```uint8(_assetType) > 4``` will allow an id of 4 since the expression will be false. Condition should be refactored to ```(uint8(_assetType) > 3)```.

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```js
1205: 		if (uint8(_assetType) > 4) { 
1206: 			revert InvalidAssetType(uint256(_assetType));
1207: 		}

1668: 		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) { 
1669: 			revert InvalidAssetType(uint256(_assetType));
1670: 		}
```