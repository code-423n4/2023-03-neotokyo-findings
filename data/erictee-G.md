### [G-01] ```X += Y``` costs more gas than ```X = X + Y``` for state variables.


#### Impact
Consider changing ```X += Y``` to ```X = X + Y``` to save gas.


#### Findings:
```
staking/NeoTokyoStaker.sol:L977			pool.totalPoints += citizenStatus.points;

staking/NeoTokyoStaker.sol:L1029		pool.totalPoints += citizenStatus.points;

staking/NeoTokyoStaker.sol:L1078		citizenStatus.stakedBytes += amount;

staking/NeoTokyoStaker.sol:L1079		citizenStatus.points += bonusPoints;

staking/NeoTokyoStaker.sol:L1080		pool.totalPoints += bonusPoints;

staking/NeoTokyoStaker.sol:L1099		citizenStatus.stakedBytes += amount;

staking/NeoTokyoStaker.sol:L1100		citizenStatus.points += bonusPoints;

staking/NeoTokyoStaker.sol:L1101		pool.totalPoints += bonusPoints;

staking/NeoTokyoStaker.sol:L1160		stakerLPPosition[msg.sender].amount += amount;

staking/NeoTokyoStaker.sol:L1161		stakerLPPosition[msg.sender].points += points;

staking/NeoTokyoStaker.sol:L1164		pool.totalPoints += points;

staking/NeoTokyoStaker.sol:L1283		points += s1Citizen.points;

staking/NeoTokyoStaker.sol:L1292		points += s2Citizen.points;

staking/NeoTokyoStaker.sol:L1298		points += stakerLPPosition[_recipient].points;

staking/NeoTokyoStaker.sol:L1332		totalReward += currentRewardRate * timeSinceReward;	

staking/NeoTokyoStaker.sol:L1343		totalReward += currentRewardRate * timeSinceReward;

staking/NeoTokyoStaker.sol:L1357		totalReward += currentRewardRate * timeSinceReward;

staking/NeoTokyoStaker.sol:L1515		pool.totalPoints -= stakedCitizen.points;

staking/NeoTokyoStaker.sol:L1580		pool.totalPoints -= stakedCitizen.points;

staking/NeoTokyoStaker.sol:L1626		lpPosition.amount -= amount;

staking/NeoTokyoStaker.sol:L1627		lpPosition.points -= points;

staking/NeoTokyoStaker.sol:L1630		pool.totalPoints -= points;

```


