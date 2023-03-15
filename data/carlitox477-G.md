# PoolData struct can package daoTax and rewardCount
Given that a daoTax of 100% means that daoTax should be equal to `100_00`; and that the chances of a pool to have more than `2^128` rewards is highly unlikely, We can use two `uint128` variables in order to save one slot. This would mean:

```diff
    struct PoolData {
		uint256 totalPoints;
-   	uint256 daoTax;
-		uint256 rewardCount;
+       uint128daoTax;
+		uint128 rewardCount;
		mapping ( uint256 => RewardWindow ) rewardWindows; // Hold emition start date and its rate
	}
```

# StakedS1Citizen struct can package timelockEndTime and hasVault
Given that `timelockEndTime` is meant to be a timestamp, a slot can be saved  by modifying struct element order and type:
```diff
	struct StakedS1Citizen {
		uint256 stakedBytes;
-		uint256 timelockEndTime;
		uint256 points;
		uint256 stakedVaultId;
		bool hasVault;
+       uint40 timelockEndTime;
	}
```

# LPPosition struct can package timelockEndTime and multiplier
Given that `timelockEndTime` is meant to be a timestamp, and that multiplier is meant to be lower than `2^128`.a slot can be saved  by modifying struct element order and type:
```diff
	struct LPPosition {
		uint256 amount;
-		uint256 timelockEndTime;
		uint256 points;
		uint256 multiplier;
+       uint128 multiplier;
+       uint40 timelockEndTime;
	}
```


# StakedS1CitizenOutput struct can package timelockEndTime and hasVault
Given that `timelockEndTime` is meant to be a timestamp, a slot can be saved  by modifying struct element order and type:
```diff
    struct StakedS1CitizenOutput {
		uint256 citizenId;
		uint256 stakedBytes;
-		uint256 timelockEndTime;
		uint256 points;
		uint256 stakedVaultId;
		bool hasVault;
+       uint40 timelockEndTime;
	}
```

# getStakerPositions(address) can use storage pointer to save gas inside for loop
The use of storage pointer instead of direct access can save gas in for loops.

```diff
	function getStakerPositions (
		address _staker
	) external view returns (StakerPosition memory) {

		// Compile the S1 Citizen details.
+		uint256[] storage stakerS1Position = _stakerS1Position[_staker];
+		uint256 stakerS1PositionLen = stakerS1Position.length
		StakedS1CitizenOutput[] memory stakedS1Details =
			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
-		for (uint256 i; i < _stakerS1Position[_staker].length; ) {
+		for (uint256 i; i < stakerS1PositionLen; ) {
-			uint256 citizenId = _stakerS1Position[_staker][i];
+			uint256 citizenId = stakerS1Position[i];
			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
			stakedS1Details[i] = StakedS1CitizenOutput({
				citizenId: citizenId,
				stakedBytes: citizenDetails.stakedBytes,
				timelockEndTime: citizenDetails.timelockEndTime,
				points: citizenDetails.points,
				hasVault: citizenDetails.hasVault,
				stakedVaultId: citizenDetails.stakedVaultId
			});
			unchecked { i++; }
		}

		// Compile the S2 Citizen details.
+		uint256[] storage stakerS2Position = _stakerS2Position[_staker]
+		uint256 stakerS2PositionLen = stakerS2Position.length
		StakedS2CitizenOutput[] memory stakedS2Details =
-			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
+			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
+		mapping(uint256 => StakedS2Citizen) storage _stakedS2 = stakedS2[_staker];
-		for (uint256 i; i < _stakerS2Position[_staker].length; ) {
+		for (uint256 i; i < stakerS2PositionLen; ) {
-			uint256 citizenId = _stakerS2Position[_staker][i];
+			uint256 citizenId = stakerS2Position[i];
-			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
+			StakedS2Citizen memory citizenDetails = _stakedS2[citizenId];
			stakedS2Details[i] = StakedS2CitizenOutput({
				citizenId: citizenId,
				stakedBytes: citizenDetails.stakedBytes,
				timelockEndTime: citizenDetails.timelockEndTime,
				points: citizenDetails.points
			});
			unchecked { i++; }
		}

		// Return the final output position struct.
		return StakerPosition({
			stakedS1Citizens: stakedS1Details,
			stakedS2Citizens: stakedS2Details,
			stakedLPPosition: stakerLPPosition[_staker]
		});
	}
```

# getPoolReward(AssetType,address) can use storage pointer to save gas inside for loop
The use of storage pointer instead of direct access can save gas in for loops.

```diff

	if (_assetType == AssetType.S1_CITIZEN) {
+		uint256[] storage stakerS1Positions = _stakerS1Position[_recipient];
+		mapping(uint256 => StakedS1Citizen) storage _stakedS1 = stakedS1[_recipient];
+		uint256 stakerS1PositionsLen = stakerS1Positions.length
-		for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
+		for (uint256 i; i < stakerS1PositionsLen; ) {
-			uint256 citizenId = _stakerS1Position[_recipient][i];
+			uint256 citizenId = stakerS1Positions[i];
-			StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
+			StakedS1Citizen memory s1Citizen = _stakedS1[citizenId];
			unchecked {
				points += s1Citizen.points;
				i++;
			}
		}
	} else if (_assetType == AssetType.S2_CITIZEN) {
+		uint256[] storage stakerS2Positions = _stakerS2Position[_recipient];
+		mapping(uint256 => StakedS2Citizen) storage _stakedS2 = stakedS2[_recipient];
+		uint256 stakerS2PositionsLen = stakerS2Positions.length
-		for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
+		for (uint256 i; i < stakerS2PositionsLen; ) {
-			uint256 citizenId = _stakerS2Position[_recipient][i];
+			uint256 citizenId = stakerS2Positions[i];
-			StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
+			StakedS2Citizen memory s2Citizen = _stakedS2[citizenId];
			unchecked {
				points += s2Citizen.points;
				i++;
			}
		}
```

# PoolConfigurationInput struct can package daoTax and rewardCount
Given that a daoTax of 100% means that daoTax should be equal to `100_00`; and that AssetType is uint8, we can use a `uint128` variable in order to save one slot. This would mean:
```diff
    struct PoolConfigurationInput {
		AssetType assetType;
-   	uint256 daoTax;
+       uint128 daoTax;
		RewardWindow[] rewardWindows;
	}
```


# NeoTokyoStaker.configurePools use storage pointer in loop to avoid redundant access
`_pools[_inputs[i].assetType]` can be save in a storage variable to avoid looking for its reference multiple times in the loop. This means

```diff
    for (uint256 i; i < _inputs.length; ) {
        uint256 poolRewardWindowCount = _inputs[i].rewardWindows.length;
+       PoolData storage _pool = _pools[_inputs[i].assetType]
-       _pools[_inputs[i].assetType].rewardCount = poolRewardWindowCount;
+       _pool.rewardCount = poolRewardWindowCount;
-       _pools[_inputs[i].assetType].daoTax = _inputs[i].daoTax;
+       _pool.daoTax = _inputs[i].daoTax;

        // Set the pool reward window details by populating the mapping.
        uint256 lastTime;
        for (uint256 j; j < poolRewardWindowCount; ) {
-           _pools[_inputs[i].assetType].rewardWindows[j] =
+           _pool.rewardWindows[j] =
                _inputs[i].rewardWindows[j];

            // Revert if an invalid pool configuration is supplied.
            if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
                revert RewardWindowTimesMustIncrease();
            }
            lastTime = _inputs[i].rewardWindows[j].startTime;
            unchecked { j++; }
        }
        unchecked { ++i; }
    }
```