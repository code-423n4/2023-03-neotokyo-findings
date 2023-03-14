## 1. Using x += y or x -= y instead of x = x + y or x = x - y can be less gas-efficient

File: NeoTokyoStaker.sol | Line: 977 | pool.totalPoints += citizenStatus.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977
File: NeoTokyoStaker.sol | Line: 1029 | pool.totalPoints += citizenStatus.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1029
File: NeoTokyoStaker.sol | Line: 977 | pool.totalPoints += citizenStatus.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L977
File: NeoTokyoStaker.sol | Line: 1029 | pool.totalPoints += citizenStatus.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1029
File: NeoTokyoStaker.sol | Line: 1078 | citizenStatus.stakedBytes += amount;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1078
File: NeoTokyoStaker.sol | Line: 1099 | citizenStatus.stakedBytes += amount;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1099
File: NeoTokyoStaker.sol | Line: 1079 | citizenStatus.points += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1079
File: NeoTokyoStaker.sol | Line: 1100 | citizenStatus.points += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1100
File: NeoTokyoStaker.sol | Line: 1080 | pool.totalPoints += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1080
File: NeoTokyoStaker.sol | Line: 1101 | pool.totalPoints += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1101
File: NeoTokyoStaker.sol | Line: 1078 | citizenStatus.stakedBytes += amount;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1078
File: NeoTokyoStaker.sol | Line: 1099 | citizenStatus.stakedBytes += amount;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1099
File: NeoTokyoStaker.sol | Line: 1079 | citizenStatus.points += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1079
File: NeoTokyoStaker.sol | Line: 1100 | citizenStatus.points += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1100
File: NeoTokyoStaker.sol | Line: 1080 | pool.totalPoints += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1080
File: NeoTokyoStaker.sol | Line: 1101 | pool.totalPoints += bonusPoints;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1101
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
File: NeoTokyoStaker.sol | Line: 1332 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1332
File: NeoTokyoStaker.sol | Line: 1343 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1343
File: NeoTokyoStaker.sol | Line: 1357 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1357
File: NeoTokyoStaker.sol | Line: 1332 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1332
File: NeoTokyoStaker.sol | Line: 1343 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1343
File: NeoTokyoStaker.sol | Line: 1357 | totalReward += currentRewardRate * timeSinceReward;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1357
File: NeoTokyoStaker.sol | Line: 1515 | pool.totalPoints -= stakedCitizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1515
File: NeoTokyoStaker.sol | Line: 1580 | pool.totalPoints -= stakedCitizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1580
File: NeoTokyoStaker.sol | Line: 1515 | pool.totalPoints -= stakedCitizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1515
File: NeoTokyoStaker.sol | Line: 1580 | pool.totalPoints -= stakedCitizen.points;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1580

## 2. ++i is more gas efficient than i++

This is because the pre-increment operator directly modifies the variable's value, while the post-increment operator requires an additional read and write operation to modify the value.

File: NeoTokyoStaker.sol | Line: 728 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728
File: NeoTokyoStaker.sol | Line: 743 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743
File: NeoTokyoStaker.sol | Line: 1383 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383
File: NeoTokyoStaker.sol | Line: 728 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728
File: NeoTokyoStaker.sol | Line: 743 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743
File: NeoTokyoStaker.sol | Line: 1383 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383
File: NeoTokyoStaker.sol | Line: 728 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728
File: NeoTokyoStaker.sol | Line: 743 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743
File: NeoTokyoStaker.sol | Line: 1284 | i++;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1284
File: NeoTokyoStaker.sol | Line: 1293 | i++;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1293
File: NeoTokyoStaker.sol | Line: 1383 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383
File: NeoTokyoStaker.sol | Line: 728 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728
File: NeoTokyoStaker.sol | Line: 743 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743
File: NeoTokyoStaker.sol | Line: 1284 | i++;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1284
File: NeoTokyoStaker.sol | Line: 1293 | i++;
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1293
File: NeoTokyoStaker.sol | Line: 1383 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383
File: NeoTokyoStaker.sol | Line: 728 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L728
File: NeoTokyoStaker.sol | Line: 743 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L743
File: NeoTokyoStaker.sol | Line: 1383 | unchecked { i++; }
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1383
