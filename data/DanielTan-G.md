# Redundant Calculation
In the contract `NeoTokyoStaker`, the `_stakeS2Citizen()` function calculates `points` with the below statement:
```Solidity
	uint256 constant private _DIVISOR = 100;
	function _stakeS2Citizen (
		uint256 _timelock
	) private {
	...
	citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
	...
	}
```
Since the constant variable `_DIVISOR` equals 100, we can directly replace the above calculation for `points` with the below state statement to save gas:
```Solidity
	citizenStatus.points = timelockMultiplier;
```