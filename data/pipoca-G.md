## 1. Using x += y or x -= y instead of x = x + y or x = x - y can be less gas-efficient

File: NeoTokyoStaker.sol | Line: 977 | pool.totalPoints += citizenStatus.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977
File: NeoTokyoStaker.sol | Line: 1078 | citizenStatus.stakedBytes += amount;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1078
File: NeoTokyoStaker.sol | Line: 1079 | citizenStatus.points += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1079
File: NeoTokyoStaker.sol | Line: 1080 | pool.totalPoints += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1080
File: NeoTokyoStaker.sol | Line: 1160 | stakerLPPosition[msg.sender].amount += amount;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1160
File: NeoTokyoStaker.sol | Line: 1161 | stakerLPPosition[msg.sender].points += points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1161
File: NeoTokyoStaker.sol | Line: 1164 | pool.totalPoints += points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1164
File: NeoTokyoStaker.sol | Line: 1283 | points += s1Citizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1283
File: NeoTokyoStaker.sol | Line: 1292 | points += s2Citizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1292
File: NeoTokyoStaker.sol | Line: 1298 | points += stakerLPPosition[_recipient].points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1298
File: NeoTokyoStaker.sol | Line: 1332 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1332
File: NeoTokyoStaker.sol | Line: 1343 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1343
File: NeoTokyoStaker.sol | Line: 1357 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1357
File: NeoTokyoStaker.sol | Line: 1515 | pool.totalPoints -= stakedCitizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1515


## 2. ++i is more gas efficient than i++

This is because the pre-increment operator directly modifies the variable's value, while the post-increment operator requires an additional read and write operation to modify the value.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1284

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1293

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383

## 3. Caching Array Length in For Loops for Gas Savings

By storing the length of the array in a local variable before the loop, the loop header can reference this variable instead of accessing the length of the array multiple times


File: NeoTokyoStaker.sol | Line: 734 | for (uint256 i; i < _stakerS2Position[_staker].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L734

File: NeoTokyoStaker.sol | Line: 1279 | for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1279

File: NeoTokyoStaker.sol | Line: 1288 | for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1288

File: NeoTokyoStaker.sol | Line: 1499 | for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1499

## 4. Use bytes(x) instead of string where possible to save gas

File: NeoTokyoStaker.sol | Line: 639 | string memory vaultMultiplier = (_vaultId != 0)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L639
File: NeoTokyoStaker.sol | Line: 665 | string memory vaultMultiplier = (_vaultId != 0)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L665
File: NeoTokyoStaker.sol | Line: 639 | string memory vaultMultiplier = (_vaultId != 0)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L639
File: NeoTokyoStaker.sol | Line: 665 | string memory vaultMultiplier = (_vaultId != 0)
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L665
File: NeoTokyoStaker.sol | Line: 939 | string memory citizenCreditYield = getCreditYield(
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L939
File: NeoTokyoStaker.sol | Line: 949 | string memory class = IGenericGetter(IDENTITY).getClass(identityId);
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L949













