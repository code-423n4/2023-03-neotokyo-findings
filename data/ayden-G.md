https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1066#L1068
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1087#L1089

save 61 ~ 141 gas

```solidity

- if (citizenStatus.stakedBytes + amount > cap) {
-  	 revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
- }

+ uint256 afterSkatedBytes;
+ unckeck{
+   afterSkatedBytes = citizenStatus.stakedBytes + amount;
+ }

+ if (afterSkatedBytes > cap) {
+  	 revert AmountExceedsCap(afterSkatedBytes, cap);
+ }

```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L710#L752
for loop with a length variable instead of the for loop with the array length

```javascript
	function getStakerPositions (
		address _staker
	) external view returns (StakerPosition memory) {

		// Compile the S1 Citizen details.
		StakedS1CitizenOutput[] memory stakedS1Details =
			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
+		uint256 lenS1 = _stakerS1Position[_staker].length;
		for (uint256 i; i < lenS1; ) {
			uint256 citizenId = _stakerS1Position[_staker][i];
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
		StakedS2CitizenOutput[] memory stakedS2Details =
			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
+		uint256 lenS2 = _stakerS2Position[_staker].length;
		for (uint256 i; i < lenS2; ) {
			uint256 citizenId = _stakerS2Position[_staker][i];
			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
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

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1264#L1396
check AssetType is valid value first save more gas

```solidity
+require(
+ _assetType == AssetType.S1_CITIZEN ||
+ _assetType == AssetType.S2_CITIZEN ||
+ _assetType == AssetType.LP,
+"Invalid asset type"
+);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1517#L1521
Use delete instead of set the value to zero

When updating the information of stakedCitizen, the delete keyword can be used to remove stakedCitizen.stakedBytes instead of setting it to zero. Using the delete keyword can prevent data stored in the contract from being exposed to potential attackers and improve the security of the contract.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1819#L1842
Multiple calls to \_inputs[i].assetType within the for loop may result in some redundant calculations. It could be beneficial to store it in a local variable and reuse it in the code.

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L983
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1035
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1112
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1170
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1526
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1589
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1641
immutable address is constant value after deployd and it's not necessary to emit this value

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1834

```solidity
-  if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {  //@audit split the &&
-	  revert RewardWindowTimesMustIncrease();
-  }

+  if (j != 0) {  //@audit split the &&
	  if(_inputs[i].rewardWindows[j].startTime <= lastTime){
+ 		revert RewardWindowTimesMustIncrease();
+	  }
+  }
```