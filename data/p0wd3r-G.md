# Check whether the amount in _withdrawLP is equal to 0

There is no check for whether the amount is equal to 0 in `_withdrawLP` function, which leads to various unnecessary gas-consuming operations.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1597