## Summary

**Gas Optimizations**

### [G-01] The storage updates for **`stakerLPPosition[msg.sender]`** can be combined into a single statement, which can save gas costs.

[1] Instead of updating each field separately, you can create a temporary variable and assign it to the storage struct: **`StakerLPPosition storage pos = stakerLPPosition[msg.sender]; pos.timelockEndTime = block.timestamp + timelockDuration; pos.amount += amount; pos.points += points;`**

```solidity
File: /contracts/staking/NeoTokyoStaker.sol	

	1124: function _stakeLP (
...
	1143:		// If this is a new stake of this asset, initialize the multiplier details.
	1144:		if (stakerLPPosition[msg.sender].multiplier == 0) {
	1145:			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;
	1146:
	1147:		// If a multiplier exists already, we must match it.
	1148:		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
	1149:			revert MismatchedTimelock();
	1150:		}
	1151:
	1152:		// Update caller staking information and asset data.
	1153:		PoolData storage pool = _pools[AssetType.LP];
	1154:		unchecked {
	1155:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
	1156:
	1157:			// Update the caller's LP token stake.
	1158:			stakerLPPosition[msg.sender].timelockEndTime =
	1159:				block.timestamp + timelockDuration;
	1160:			stakerLPPosition[msg.sender].amount += amount;
	1161:			stakerLPPosition[msg.sender].points += points;

```

- [NeoTokyoStaker.sol#L1124](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1124)

**Recommended Mitigation Steps:**

```diff
File: /contracts/staking/NeoTokyoStaker.sol	

	1124: function _stakeLP (
...
	1143:		// If this is a new stake of this asset, initialize the multiplier details.
+ 1143:   StakerLPPosition storage pos = stakerLPPosition[msg.sender];
+	1145:		if (pos.multiplier == 0) {
+	1146:			pos.multiplier = timelockMultiplier;
+	1147:
+	1148:		// If a multiplier exists already, we must match it.
+	1149:		} else if (pos.multiplier != timelockMultiplier) {
+	1150:			revert MismatchedTimelock();
+	1151:		}
+	1152:
+	1153:		// Update caller staking information and asset data.
+	1154:		PoolData storage pool = _pools[AssetType.LP];
+	1155:		unchecked {
+	1156:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
+	1157:
+	1158:			// Update the caller's LP token stake.
+	1159:			pos.timelockEndTime =
+	1160:				block.timestamp + timelockDuration;
+	1161:			pos.amount += amount;
+	1162:			pos.points += points;

```

### [2] cached `NeoTokyoStaker.sol.stake():_pools[assetType]`

```solidity
File: /contracts/staking/NeoTokyoStaker.sol
	1196: function stake (
	1209:		// Validate that the asset being staked matches a configured pool.
	1210:		if (_pools[_assetType].rewardCount == 0) {
	1211:			revert UnconfiguredPool(uint256(_assetType));
	1212:		}
	1213:
	1214:		// Validate that the asset being staked matches an active pool.
	1215:		if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
	1216:			revert InactivePool(uint256(_assetType));
	1217:		}
```

```diff
	1196: function stake (
+ 1208: PoolData storage pool = _pools[_assetType];
	1209:		// Validate that the asset being staked matches a configured pool.
+	1210:		if (pool.rewardCount == 0) {
	1211:			revert UnconfiguredPool(uint256(_assetType));
	1212:		}
	1213:
	1214:		// Validate that the asset being staked matches an active pool.
+	1215:		if (pool.rewardWindows[0].startTime >= block.timestamp) {
	1216:			revert InactivePool(uint256(_assetType));
	1217:		}
```

- [NeoTokyoStaker.sol#L1196](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196)

### [G-02] Using `delete` instead of setting state variable/mapping to `0` saves gas

*There are 8 instances of this issue:*

```solidity
File: /staking/NeoTokyoStaker.sol
	1517: stakedCitizen.stakedBytes = 0;
	1518:	stakedCitizen.timelockEndTime = 0;
	1519:	stakedCitizen.points = 0;
...
	1521:	stakedCitizen.stakedVaultId = 0;
...
	1582: stakedCitizen.stakedBytes = 0;
	1583:	stakedCitizen.timelockEndTime = 0;
	1584:	stakedCitizen.points = 0;
...
	1635: lpPosition.multiplier = 0;
```

- [NeoTokyoStaker.sol#L1517](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517)

### [G-03] **Use nested if and, avoid multiple check combinations**

**Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.**

*There are 1 instances of this issue:*

```solidity
File: /contracts/staking/NeoTokyoStaker.sol
1834: if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {	
```

- [NeoTokyoStaker.sol#L1834](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1834)

****Recomendation Code****

```diff
File: /contracts/staking/NeoTokyoStaker.sol
-1834: if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {	
+ if (j != 0 ) {
+  if(_inputs[i].rewardWindows[j].startTime <= lastTime) {	
+  }
+ }

```