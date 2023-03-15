### Gas optimization 1


Constraints checks for `enum` are added automatically by the solidity compiler, so the following code is redundant and may be removed to save gas:
```
if (uint8(_assetType) > 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205

Similarly, the following code may be simplified from:
```
if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668

To:
```
if (_assetType == AssetType.BYTES) {
	revert InvalidAssetType(uint256(_assetType));
}
```


### Gas optimization 2


The code which removes a position in `_withdrawLP()`...
```
if (lpPosition.amount == 0) {
	lpPosition.multiplier = 0;
}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1635

...misses the removal of `lpPosition.timelockEndTime`:
```
lpPosition.timelockEndTime = 0;
```

Adding it will add an additional gas compensation for the caller.



### Gas optimization 3


Using `bytes32` instead of `string` for mappings can save gas, as `bytes32` is cheaper to store and compare. You can change the `identityCreditYield` and `vaultCreditMultiplier` mappings to use `bytes32` keys:
```
mapping(uint256 => mapping(bytes32 => string)) private identityCreditYield;
mapping(bytes32 => uint256) private vaultCreditMultiplier;
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L326
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L332

Make sure to update the contract code accordingly to work with `bytes32` keys instead of `string`.



### Gas optimization 4


Remove unnecessary `else` in `getStakerPosition()`:

Replace the following code...
```
function getStakerPosition (
	address _staker,
	AssetType _assetType
) external view returns (uint256[] memory) {
	if (_assetType == AssetType.S1_CITIZEN) {
		return _stakerS1Position[_staker];
	} else if (_assetType == AssetType.S2_CITIZEN) {
		return _stakerS2Position[_staker];
	} else {
		revert UnknowablePosition(uint256(_assetType));
	}
}
```
https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L697

by this:
```
function getStakerPosition (
	address _staker,
	AssetType _assetType
) external view returns (uint256[] memory) {
	if (_assetType == AssetType.S1_CITIZEN) {
		return _stakerS1Position[_staker];
	} else if (_assetType == AssetType.S2_CITIZEN) {
		return _stakerS2Position[_staker];
	}
	revert UnknowablePosition(uint256(_assetType));
}
```

