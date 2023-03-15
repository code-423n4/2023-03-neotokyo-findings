## Summary

### Gas Optimizations
| |Issue|Instances| |
|-|:-|:-:|:-:|
| [G&#x2011;01] | Avoid compound assignment operator in state variables | 22 |

### [G&#x2011;01]  Avoid compound assignment operator in state variables
Using compound assignment operators for state variables (like State += X or State -= X …) it’s more expensive than using operator assignment (like State = State + X or State = State - X …).
There are 22 instances of this issue.

```solidity
File: contracts/staking/NeoTokyoStaker.sol

977			pool.totalPoints += citizenStatus.points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L977)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1029			pool.totalPoints += citizenStatus.points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1029)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1078				citizenStatus.stakedBytes += amount;
1079				citizenStatus.points += bonusPoints;
1080				pool.totalPoints += bonusPoints;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1078-L1080)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1099				citizenStatus.stakedBytes += amount;
1100				citizenStatus.points += bonusPoints;
1101				pool.totalPoints += bonusPoints;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1099-L1101)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1160			stakerLPPosition[msg.sender].amount += amount;
1161			stakerLPPosition[msg.sender].points += points;
1162
1163			// Update the pool point weights for rewards.
1164			pool.totalPoints += points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1160-L1164)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1283						points += s1Citizen.points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1283)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1292						points += s2Citizen.points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1292)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1298					points += stakerLPPosition[_recipient].points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1298)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1332				       totalReward += currentRewardRate * timeSinceReward;	
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1332)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1343				      totalReward += currentRewardRate * timeSinceReward;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1343)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1357				      totalReward += currentRewardRate * timeSinceReward;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1357)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1515			pool.totalPoints -= stakedCitizen.points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1515)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1580			pool.totalPoints -= stakedCitizen.points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1580)

```solidity
File: contracts/staking/NeoTokyoStaker.sol

1626			lpPosition.amount -= amount;
1627			lpPosition.points -= points;
1628
1639			// Update the pool point weights for rewards.
1630			pool.totalPoints -= points;
```
[Link to code](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1626-L1630)
