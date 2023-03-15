# Findings Summary

| ID     | Title                        | Severity      |
| ------ | ---------------------------- | ------------- |
| [L-01] | InvalidAssetType range       | Low           |
| [L-02] | Instant reward calculation   | Low           |
| [L-03] | Call non-contract address    | Low           |

# Detailed Findings

# [L-01] InvalidAssetType range

## Description

It should trigger the error `InvalidAssetType` when input `_assetType == 4`

## Recommendations

The _assetType of stake and withdraw should be checked to be greater than 3 instead of 4.

```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..ee195a5 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1202,7 +1202,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
 	) external nonReentrant {
 
 		// Validate that the asset being staked is of a valid type.
-		if (uint8(_assetType) > 4) {
+		if (uint8(_assetType) > 3) {
 			revert InvalidAssetType(uint256(_assetType));
 		}
 
@@ -1665,7 +1665,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
 			Validate that the asset being withdrawn is of a valid type. BYTES may not 
 			be withdrawn independently of the Citizen that they are staked into.
 		*/
-		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
+		if (uint8(_assetType) == 2 || uint8(_assetType) > 3) {
 			revert InvalidAssetType(uint256(_assetType));
 		}
```

# [L-02] Instant reward calculation

## Description

Instant reward calculations can be manipulated, the calculation of reward depends on [totalPoints](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#LL1388C37-L1388C37), the attacker can sandwich `getPoolReward`, first stake to increase the totalPoints, resulting in user loss.

## Recommendations

Cache the value of the previous block and use it.

# [L-03] Call non-contract address

## Description

[_assetTransferFrom](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L766) and [_assetTransfer](https://github.com/code-423n4/2023-03-neotokyo/blob/dfa5887062e47e2d0c801ef33062d44c09f6f36e/contracts/staking/NeoTokyoStaker.sol#L796) did not check the non-contract address, resulting in the call will succeed by default.

## Recommendations

Check whether the call address is a contract
