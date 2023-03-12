# Division before multiplication incurs unnecessary precision loss
as [NeoTokyoStaker.sol#L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1155)

	uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;