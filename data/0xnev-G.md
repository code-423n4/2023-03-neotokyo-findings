### Gas Optimization Template
| Count | Title | Instances |
|:--:|:-------|:--:|
| [G-01] | Use `delete` instead of default value assignment to clear storage variables | 9 |
| [G-02] | Use nested `if` statements to avoid multiple check combinations using `&&` | 10 |
| [G-03] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with `-=`) | 2 |
| [G-04] | Multiple accesses of a mapping/array should use a local variable cache | 23 |
| [G-05] | Check amount before execution of functions for possible gas savings| 2 |
| [G-06] |  Avoid using stack variable when state variable is only used once | 1 |
| [G-07] | Avoid assigning memory variable when it is only used once | 5 |
| [G-08] | Initialize stack variables outside loop | 9 |

| Total Gas-Optimization Issues | 8 |
|:--:|:--:|

### [G-01] Use `delete` instead of default value assignment to clear storage variables
Context:
```solidity
9 results - 1 file

/NeoTokyoStaker.sol
1459:	function _withdrawS1Citizen () private
1517:		stakedCitizen.stakedBytes = 0;
1518:		stakedCitizen.timelockEndTime = 0;
1519:		stakedCitizen.points = 0;
1520:		stakedCitizen.hasVault = false;
1521:		stakedCitizen.stakedVaultId = 0;

1534:	function _withdrawS2Citizen () private
1582:		stakedCitizen.stakedBytes = 0;
1583:		stakedCitizen.timelockEndTime = 0;
1584:		stakedCitizen.points = 0;

1597:	function _withdrawLP () private
1635:			lpPosition.multiplier = 0;
```
Description: 
While `delete` has no effect on mappings, deleting a particular value of key in mappings is possible through recursion for nested structs and even saves gas 
https://docs.soliditylang.org/en/v0.8.19/types.html?highlight=delete#delete

Reccomendation:
Use delete instead of zero assignment to clear storage variables 

### [G-02] Use nested `if` statements to avoid multiple check combinations using `&&`
Context:
```solidity
3 results - 1 file

/NeoTokyoStaker.sol
875: function _stakeS1Citizen
910:    if (citizenVaultId != 0 && vaultId != 0)
917:    else if (citizenVaultId != 0 && vaultId == 0)
926:    else if (citizenVaultId == 0 && vaultId != 0) 
```
Recommendation:
Replace `&&` used within `if` and `else if` statements with nested `if` statements

### [G-03] `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables (same with `-=`)
Context:
```solidity
16 results - 1 file

/NeoTokyoStaker.sol
875:	function _stakeS1Citizen
977:			pool.totalPoints += citizenStatus.points;

995:	function _stakeS2Citizen
1029:			pool.totalPoints += citizenStatus.points;

1044:	function _stakeBytes
1078:				citizenStatus.stakedBytes += amount;
1079:				citizenStatus.points += bonusPoints;
1080:				pool.totalPoints += bonusPoints;
1099:				citizenStatus.stakedBytes += amount;
1100:				citizenStatus.points += bonusPoints;
1101:				pool.totalPoints += bonusPoints;

1124:	function _stakeLP
1160:			stakerLPPosition[msg.sender].amount += amount;
1161:			stakerLPPosition[msg.sender].points += points;
1164:			pool.totalPoints += points;

1459:	function _withdrawS1Citizen ()
1515:			pool.totalPoints -= stakedCitizen.points;

1534:	function _withdrawS2Citizen ()
1580:			pool.totalPoints -= stakedCitizen.points;

1597:	function _withdrawLP ()
1626:			lpPosition.amount -= amount;
1627:			lpPosition.points -= points;
1630:			pool.totalPoints -= points;
```

Recommendation:
Replace `<x> += <y>` with `<x> = <x> + <y>` for state variables (same for `-=`)


### [G-04] Multiple accesses of a mapping/array should use a local variable cache
Description:
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping’s value in a local storage variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations. Caching an array’s struct avoids recalculating the array offsets into memory

Recommendation:
Cache variable like this
`SomeValue storage someValue = someMap[someIndex]`

Context:

Cache `lastRewardTime[_recipient]` in local storage
```solidity
3 results

/NeoTokyoStaker.sol
1409:	function claimReward
1433:		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
1434:		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
1435:		lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
```

Cache `_stakerS1Position[_staker]` in local storage
```solidity
3 results

/NeoTokyoStaker.sol
710:	function getStakerPositions
716:			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
717:		for (uint256 i; i < _stakerS1Position[_staker].length; )
718:			uint256 citizenId = _stakerS1Position[_staker][i];
```

Cache `_stakerS1Position[_recipient]` in local storage
```solidity
2 results

/NeoTokyoStaker.sol
1264:	function getPoolReward
1279:				for (uint256 i; i < _stakerS1Position[_recipient].length; )
1280:					uint256 citizenId = _stakerS1Position[_recipient][i];
```

Cache `_stakerS2Position[_staker]` in local storage
```solidity
3 results

/NeoTokyoStaker.sol
710:	function getStakerPositions
733:			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
734:		for (uint256 i; i < _stakerS2Position[_staker].length; )
735:			uint256 citizenId = _stakerS2Position[_staker][i];
```

Cache `_stakerS2Position[_recipient]` in local storage
```solidity
2 results

/NeoTokyoStaker.sol
1264:	function getPoolReward
1288:				for (uint256 i; i < _stakerS2Position[_recipient].length; ) 
1289:					uint256 citizenId = _stakerS2Position[_recipient][i];
```

Cache `stakerLPPosition[msg.sender]` in local storage
```solidity
6 results

/NeoTokyoStaker.sol
1124:	function _stakeLP
1144:		if (stakerLPPosition[msg.sender].multiplier == 0) {
1145:			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;
1148:		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier)
1158:			stakerLPPosition[msg.sender].timelockEndTime =
1159:				block.timestamp + timelockDuration;
1160:			stakerLPPosition[msg.sender].amount += amount;
1170:			stakerLPPosition[msg.sender].points += points;
```

Cache `stakedS1[_staker]` in local storage 
```solidity
1 result

/NeoTokyoStaker.sol
710:	function getStakerPositions
717:		for (uint256 i; i < _stakerS1Position[_staker].length; )
719:			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
```

Cache `stakedS2[_staker]` in local storage
```solidity
1 results

/NeoTokyoStaker.sol
710:	function getStakerPositions
734:		for (uint256 i; i < _stakerS2Position[_staker].length; )
736:			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
```

Cache `stakedS1[_recipient]` in local storage
```solidity
1 result

/NeoTokyoStaker.sol
1264:	function getPoolReward
1279:				for (uint256 i; i < _stakerS1Position[_recipient].length; ) 
1281:					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
```

Cache `stakedS2[_recipient]` in local storage
```solidity
1 result

/NeoTokyoStaker.sol
1264:	function getPoolReward
1288:				for (uint256 i; i < _stakerS2Position[_recipient].length; )
1290:					StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
```

### [G-05] Check amount before execution of functions for possible gas savings
```solidity
2 results - 1 file

/NeoTokyoStaker.sol
766:	function _assetTransferFrom
796:	function _assetTransfer
```
Description:
Before execution of `transfer` functions, we should check if amount is zero to prevent wastage of gas when `transfer` functions do not do anything upon execution

### [G-06] Avoid using stack variable when state variable is only used once
```solidity
1 result - 1 file

/NeoTokyoStaker.sol
875:	function _stakeS1Citizen
943:		uint256 identityPoints = identityCreditPoints[citizenCreditYield];
```
Description:
If the variable is only accessed once, it is cheaper to directly use the state variable directly and save the 3 gas by avoiding extra stack variable assignment

Recommendation:
Directly use state variable instead of caching it in stack

### [G-07] Avoid assigning memory variable when it is only used once
```solidity
5 result - 1 file

/NeoTokyoStaker.sol
875:	function _stakeS1Citizen
962:		uint256 timelockDuration = _timelock >> 128;
963:		uint256 timelockMultiplier = _timelock & type(uint128).max;

995:	function _stakeS2Citizen
1016:		uint256 timelockDuration = _timelock >> 128;
1017:		uint256 timelockMultiplier = _timelock & type(uint128).max;

1124:	function _stakeLP
1140:		uint256 timelockDuration = _timelock >> 128;
```

Description:
If the variable is only accessed once, it is cheaper to directly use the function argument directly and save the 3 gas by avoiding extra stack variable assignment

Recommendation:
Directly use state variable instead of caching it 

### [G-08] Initialize stack variables outside loop
```solidity
9 results - 1 file

/NeoTokyoStaker.sol
710:	function getStakerPositions
718:			uint256 citizenId = _stakerS1Position[_staker][i];
719:			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
735:			uint256 citizenId = _stakerS2Position[_staker][i];
736:			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];

1264:	function getPoolReward
1280:					uint256 citizenId = _stakerS1Position[_recipient][i];
1281:					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
1289:					uint256 citizenId = _stakerS2Position[_recipient][i];
1290:					StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
1313:				RewardWindow memory window = pool.rewardWindows[i];
```
Description:
Consider initializing the stack variables before the loop to avoid reinitialization on every loop

Recommendation:
Consider initializing the stack variables before the loop to save gas 


