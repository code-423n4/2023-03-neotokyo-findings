1. The validation of the asset type being staked in the `stake ()` function uses an incorrect check. The `AssetType` enum has 4 assets types starting at 0. The condition should therefore be `(uint8(_assetType) > 3)` instead of `(uint8(_assetType) > 4)`.

```solidity
		// Validate that the asset being staked is of a valid type.
		if (uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```
This is also an issue in the `withdraw()` function

```solidity
		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```
