# Gas Optimizations Report

|         | Issue                                                                           | Instances |
| ------- | :------------------------------------------------------------------------------ | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables |     5     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:5

[contracts/staking/NeoTokyoStaker.sol#L1283](https://github.com/code-423n4/2023-03-neotokyo/tree/main//contracts/staking/NeoTokyoStaker.sol#L1283)

```solidity
1283:    points += s1Citizen.points;
```

[contracts/staking/NeoTokyoStaker.sol#L1292](https://github.com/code-423n4/2023-03-neotokyo/tree/main//contracts/staking/NeoTokyoStaker.sol#L1292)

```solidity
1292:    points += s2Citizen.points;
```

[contracts/staking/NeoTokyoStaker.sol#L1332](https://github.com/code-423n4/2023-03-neotokyo/tree/main//contracts/staking/NeoTokyoStaker.sol#L1332)

```solidity
1332:    totalReward += currentRewardRate * timeSinceReward;
```

[contracts/staking/NeoTokyoStaker.sol#L1343](https://github.com/code-423n4/2023-03-neotokyo/tree/main//contracts/staking/NeoTokyoStaker.sol#L1343)

```solidity
1343:    totalReward += currentRewardRate * timeSinceReward;
```

[contracts/staking/NeoTokyoStaker.sol#L1357](https://github.com/code-423n4/2023-03-neotokyo/tree/main//contracts/staking/NeoTokyoStaker.sol#L1357)

```solidity
1357:    totalReward += currentRewardRate * timeSinceReward;
```
