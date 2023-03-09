# Gas optimisations
## [G1] Multiply by 100 then divide by 100
- [NeoTokyoStaker.sol#L1022](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1022)

	`_DIVISOR` = 100.  
	`citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;`
	Make the following change and save 729 gas on average for calling `stake`
	`citizenStatus.points = timelockMultiplier;`
	
- [NeoTokyoStaker.sol#L1155](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1155)
	`uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;`
	Make the following change and save 729 gas on average for calling `withdrawer`
	`uint256 points = amount * timelockMultiplier / 1e18;`
	
## [G2] Cache `stakerLPPosition[msg.sender]` to save upto 2301 gas
[NeoTokyoStaker.sol#L1157-L1161](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1157-L1161)
```
LPPosition storage position = stakerLPPosition[msg.sender];
// Update the caller's LP token stake.
position.timelockEndTime = block.timestamp + timelockDuration;
position.amount += amount;
position.points += points;
```