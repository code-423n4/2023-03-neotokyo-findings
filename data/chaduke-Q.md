QA1: There should be a range check for ``VAULT_CAP`` and ``NO_VAULT_CAP`` check.

[https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L588-L606)

[https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851-L1857](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1851-L1857)

QA2: ``_assetTransferFrom()``'s revert will not return correct error message.

The following code in ``_assetTransferFrom()`` will not return correct revert error message, an experiment can easily show that - it will return some obscure bytecode unreadable message. This is because the first word of ``data`` actually stores the number of bytes in ``data``.

```javascript
if (!success) {
			revert(string(data));
		}
```

The correct way is: 
```
if (!success) {
      if(data.length > 0) {
           let len :=  mload(data)
           revert(add(32, data), len);
      }     
}
```

QA3. ``withdraw()`` should not allow ``_assetType == 4`` since it is not defined. 

``withdraw()`` does not exclude ``_assetType == 4``, although it is not defined. 

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1668-L1670

Correction, exclude ``_assetType == 4``:

```diff
- if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+ if (uint8(_assetType) == 2 || uint8(_assetType) >= 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```

QA4. The ``stake()`` function fails to exclude the case ``_assetType == 4``.

https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L1196-L1207

correction: exclude ``_assetType == 4``

```diff
function stake (
		AssetType _assetType,
		uint256 _timelockId,
		uint256,
		uint256,
		uint256
	) external nonReentrant {

		// Validate that the asset being staked is of a valid type.
-		if (uint8(_assetType) > 4) {
+		if (uint8(_assetType) >= 4) {
			revert InvalidAssetType(uint256(_assetType));
		}

```
