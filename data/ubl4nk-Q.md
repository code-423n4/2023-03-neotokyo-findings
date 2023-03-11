According to the definition of ```AssetType``` in ```NeoTokyoStaker.sol```:
 (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L266)
```
enum AssetType {
       S1_CITIZEN, <--- 0
       S2_CITIZEN, <--- 1
       BYTES,      <--- 2
       LP          <--- 3
}
```
There is one mistake while checking the type of asset (line https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205)
```
if (uint8(_assetType) > 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```
The asset type can not be 4, our maximum _assetType number can be 3 not 4.
The code can be modified as follows:

```
if (uint8(_assetType) > 3) {
	revert InvalidAssetType(uint256(_assetType));
}
```

OR

```
if (uint8(_assetType) >= 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```
