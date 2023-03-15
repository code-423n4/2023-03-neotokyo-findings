# Gas Optimizations list

| Number | Details                                                                                           | Instances |
| ------ | ------------------------------------------------------------------------------------------------- | --------- |
| 1      | ```x += y```/```x -= y``` COSTS MORE GAS THAN ```x = x + y```/```x = x - y``` FOR STATE VARIABLES | 16        |
| 2      | CAN DECLARE VARIABLE OUTSIDE LOOP TO SAVE GAS                                                     | 12        |
| 3      | ```revert()``` STATEMENTS THAT CHECK INPUT ARGUMENTS SHOULD BE AT THE TOP OF THE FUNCTION         | 1         |
| 4      | BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE                       | 1         |
| 5      | TERNARY OPERATION NOT REQUIRED                                                                    | 2         |
| 6      | UNNECESSARY CAST TO UINT8                                                                         | 2         |

# Gas Optimizations
## [G-01]  ```x += y```/```x -= y``` COSTS MORE GAS THAN ```x = x + y```/```x = x - y``` FOR STATE VARIABLES
Using the addition operator instead of plus-equals saves some gas for state variables.

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```js
977: 			pool.totalPoints += citizenStatus.points; 

1029: 			pool.totalPoints += citizenStatus.points; 

1078: 				citizenStatus.stakedBytes += amount; 

1079: 				citizenStatus.points += bonusPoints;

1080: 				pool.totalPoints += bonusPoints;

1099: 				citizenStatus.stakedBytes += amount;

1100: 				citizenStatus.points += bonusPoints;

1101: 				pool.totalPoints += bonusPoints;

1160: 			stakerLPPosition[msg.sender].amount += amount;

1161: 			stakerLPPosition[msg.sender].points += points;

1164: 			pool.totalPoints += points;

1298: 					points += stakerLPPosition[_recipient].points; 

1515: 			pool.totalPoints -= stakedCitizen.points; 

1626: 			lpPosition.amount -= amount; 

1627: 			lpPosition.points -= points; 

1630: 			pool.totalPoints -= points; 
```

## [G-02] CAN DECLARE VARIABLE OUTSIDE LOOP TO SAVE GAS 
Declaring the stack variable outside the loop will save gas that would otherwise be spent on declaring the variable over and over again.  

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```js
718: 			uint256 citizenId = _stakerS1Position[_staker][i]; outside loop

735: 			uint256 citizenId = _stakerS2Position[_staker][i];outside loop

1280: 					uint256 citizenId = _stakerS1Position[_recipient][i]; outside loop

1281: 					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId]; outside loop

1289: 					uint256 citizenId = _stakerS2Position[_recipient][i];outside loop

1290: 					StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];outside loop

1313: 				RewardWindow memory window = pool.rewardWindows[i]; outside loop

1320: 					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward; outside loop

1331: 								uint256 timeSinceReward = block.timestamp - lastPoolRewardTime; outside loop

1342: 								uint256 timeSinceReward = window.startTime - lastPoolRewardTime; outside loop

1355: 									uint256 timeSinceReward = 

1378: 						uint256 timeSinceReward = block.timestamp - lastPoolRewardTime; 
```

## [G-03] ```revert()``` STATEMENTS THAT RESULT FROM CHECKING INPUT ARGUMENTS SHOULD BE AT THE TOP OF THE FUNCTION
By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas*) in a function that may ultimately revert in the unhappy case.

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```JS
1220: 		uint256 timelockOption = timelockOptions[_assetType][_timelockId]; 
1221: 		if (timelockOption == 0) {
1222: 			revert InvalidTimelockOption(uint256(_assetType), _timelockId);
1223: 		}
```

## [G-04] BEFORE SOME FUNCTIONS, WE SHOULD CHECK SOME VARIABLES FOR POSSIBLE GAS SAVE
Before burning/minting/transfer, we should check for amount being 0 so the function doesnt run when its not gonna do anything.

[contracts\staking\BYTES2.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol)
```js
101: 		IByteContract(BYTES1).burn(msg.sender, _amount);
102: 		_mint(msg.sender, _amount);
```

## [G-05] TERNARY OPERATION NOT REQUIRED
It would be slightly cheaper to declare the variable and simply use an if statement, instead of using ternary operation. 

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```js
639: 		string memory vaultMultiplier = (_vaultId != 0)
640: 			? vault.getCreditMultiplier(_vaultId)
641: 			: ""; 

665: 		string memory vaultMultiplier = (_vaultId != 0)
666: 			? vault.getCreditMultiplier(_vaultId)
667: 			: ""; 
```

## [G-06] UNNECESSARY CAST TO UINT8
Some gas can be saved by casting to uint256 while checking instead of uint8. 

[contracts\staking\NeoTokyoStaker.sol](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol)
```js
1205: 		if (uint8(_assetType) > 4) { 

1668: 		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) { 
```