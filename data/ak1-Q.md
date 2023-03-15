Incorrect `timelockMultiplier` is used for stake and withdraw the LP.

The code snippets are,

stake : `timelockMultiplier `

                unchecked {
		     uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

witdraw : `lpPosition.multiplier`

		PoolData storage pool = _pools[AssetType.LP];
		unchecked {
			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

Not sure this is an issue, but still wanted to flag this.

Due to time constraints, not able to deep dive into this one.