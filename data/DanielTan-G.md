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


Similarly, the calculation of `points` in the `_stakeLP` function is shown below:
```Solidity
	function _stakeLP (
		uint256 _timelock
	) private {
			...
			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
			...
	}
```
The calculation of `points` could be replaced with `uint256 points = amount / 1e18 * timelockMultiplier;` to save gas.


Same as the calculation of `points` in the `_withdrawLP` function shown below:
```Solidity
	function _withdrawLP () private {
			...
			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
			...
	}
```
The calculation of `points` could be replaced with `uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;` to save gas.