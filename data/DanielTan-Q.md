# Unstrict Condition

## Lines of code

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668


## Vulnerability details

### Impact
There are input validations in the `stake` function and the `withdraw` function to keep the functions working as expected.

However, the input validation for the `_assetType` is unstrict. Because there are only four types of assets and four corresponding function calls:
```Solidity
	enum AssetType {
		S1_CITIZEN,
		S2_CITIZEN,
		BYTES,
		LP
	}
```
```Solidity
	function stake (
		AssetType _assetType,
		uint256 _timelockId,
		uint256,
		uint256,
		uint256
	) external nonReentrant {
	...
		assembly {
			switch _assetType
				case 0 {
					_stake := _s1
				}
				case 1 {
					_stake := _s2
				}
				case 2 {
					_stake := _b
				}
				case 3 {
					_stake := _lp
				}
				default {}
		}

		// Invoke the correct staking function.
		_stake(timelockOption);
	...
```

Note that the value of the `_assetType` parameter starts from 0 to 3, but the condition `if (uint8(_assetType) > 4) ` allows `_assetType` to be 4. 

Similarly, it also happens in the `withdraw` function:
```Solidity
	function withdraw (
		AssetType _assetType,
		uint256
	) external nonReentrant {
		...
		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
		...
```

### Recommended Mitigation Steps
Recommend updating the condition to validate the `_assetType`:
```Solidity
		if (uint8(_assetType) > 3) {
			revert InvalidAssetType(uint256(_assetType));
		}
```

