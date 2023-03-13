### Q-01 Incorrect _assetType validation 
[NeoTokyoStaker.sol#L1205-L1207](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1205-L1207)
 enum AssetType only has valid valuse of 0 to 3
```solidity
	if (uint8(_assetType) > 4) { // @audit should revert > 3
		revert InvalidAssetType(uint256(_assetType));
	}

```

### Q-02 Incorrect _assetType validation 
[NeoTokyoStaker.sol#L1668-L1670](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668-L1670)
Incorrect _assetType validation. enum AssetType only has valid valuse of 0 to 3
```solidity
	if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {  // @audit  should revert when 2 || > 3
		revert InvalidAssetType(uint256(_assetType));
	}
````