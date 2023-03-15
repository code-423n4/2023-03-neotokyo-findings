https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668
\_assetType has only 4 distinct values 0123, If there are no modifications, When assetType is equal to 4, the code will continue to execute and failed with error " Calling a zero initialized variable of internal function type"

Delete unused variables and functions: Unused variables and functions like \_withdrawS1Citizen, \_withdrawS2Citizen, and \_withdrawLP can be removed to reduce redundant code

```
-if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+if (uint8(_assetType) == 2 || uint8(_assetType) > 3) {
	revert InvalidAssetType(uint256(_assetType));
}


function () _withdraw;
assembly {
	switch _assetType
		case 0 {
			_withdraw := _withdrawS1Citizen
		}
		case 1 {
			_withdraw := _withdrawS2Citizen
		}
		case 3 {
			_withdraw := _withdrawLP
		}
}
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205
\_assetType has only 4 distinct values 0123, If there are no modifications, When assetType is equal to 4, the code will continue to execute and failed with error " Calling a zero initialized variable of internal function type"

```

		- if (uint8(_assetType) > 4) {
		+ if (uint8(_assetType) > 3) {
		  	revert InvalidAssetType(uint256(_assetType));
		 }



		// Store references to each available staking function.
		- function (uint256) _s1 = _stakeS1Citizen;
		- function (uint256) _s2 = _stakeS2Citizen;
		- function (uint256) _b = _stakeBytes;
		- function (uint256) _lp = _stakeLP;

		// Select the proper staking function based on the asset type being staked.
		function (uint256) _stake;
		assembly {
			switch _assetType
				case 0 {
					_stake := _stakeS1Citizen
				}
				case 1 {
					_stake := _stakeS2Citizen
				}
				case 2 {
					_stake := _stakeBytes
				}
				case 3 {
					_stake := _stakeLP
				}
		-	    default {}
		}

```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588#L606
constructor function lack of a zero address check

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L711
address \_staker lacks of a zero address check

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1410
check \_recipient is a valid address

```solidity
require(_recipient!=address(0),"zero address")
```
