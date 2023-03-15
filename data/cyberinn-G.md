# [G-01] x += y costs more gas than x = x + y for state variables

## Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

## Findings

Total: 3


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
