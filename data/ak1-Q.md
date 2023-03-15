Incorrect `timelockMultiplier` is used for stake and withdraw the LP.

The code snippets are,

stake : `timelockMultiplier `

   unchecked {
			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

witdraw

		PoolData storage pool = _pools[AssetType.LP];
		unchecked {
			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;