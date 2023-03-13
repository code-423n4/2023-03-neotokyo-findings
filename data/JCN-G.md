## Summary
A majority of functions that underwent direct changes for optimziations did not have individual tests that showed gas usage and therefore gas savings were explained via opcodes and EVM gas costs (with the exception of the first issue).

The gas savings in the first issue are substantial and therefore `stake()` and `withdraw()` were benchmarked as POCs for the optimizations that were done in storage and in the `claimReward()` & `getPoolReward()` functions.

**Notes**: 
- Some code snippets may be truncated to save space. Code snippets may also be accompanied by `@audit` tags in comments to aid in explaining the issue.
- The `*` next to the gas savings of an issue indicates that some instances are found within loops, meaning the actual gas savings will be greater depending on the number of iterations in the loop.
- The last issue is soley meant to offer clarification for the issue caught by the c4udit tool.

### Gas Optimizations
| Number |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G-01](#refactor-mapping-to-save-over-44k-gas-for-new-users-that-stake-10k-gas-for-recurring-users-that-stake-and-10k-gas-for-users-that-withdraw) | Refactor mapping to save over 44k gas for new users that stake, 10k gas for recurring users that stake, and 10k gas for users that withdraw | - | 54200 |
| [G-02](#state-variables-can-be-packed-to-use-fewer-storage-slots) | State variables can be packed to use fewer storage slots | 1 | 2000 |
| [G-03](#state-variables-can-be-cached-instead-of-re-reading-them-from-storage) | State variables can be cached instead of re-reading them from storage | 27 | *2700 |
| [G-04](#avoid-emitting-constants) | Avoid emitting constants | 5 | 1875 |
| [G-05](#multiple-accesses-of-a-mappingarray-should-use-a-storage-pointer) | Multiple accesses of a mapping/array should use a storage pointer | 25 | *1000 |
| [G-06](#x--yx---y-costs-more-gas-than-x--x--yx--x---y-for-state-variables) | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables | 13 |  260 |
| [G-07](#use-storage-instead-of-memory-for-structsarrays) | Use storage instead of memory for structs/arrays | - | - |

## Refactor mapping to save over 44k gas for new users that stake, 10k gas for recurring users that stake, and 10k gas for users that withdraw
The `lastRewardTime` nested mapping is only modified in the `claimReward()` function and all asset types for the user are given the same value: `block.timestamp` (i.e. 3 different storage slots undergo an SSTORE with the **same value**). The `lastRewardTime` mapping is then accessed in the `getPoolReward()` function and the same values are read for each `AssetType` (i.e. 3 different storage slots, which hold the **same value**, undergo an SLOAD). Due to this flow, the nested mapping is unecessary and a regular mapping would suffice in recording a single `lastRewardTime` for the user since the time will always be the same for ALL assets, as per the `claimReward()` function.

Refactoring the `lastRewardTime` mapping will stop a user from incurring two `Gsset (20000 gas)` and two `Gcoldsload (2100 gas)` when they call `stake()` for the first time, and two `Gsreset (2900 gas)` and two `Gcoldsload (2100 gas)` each time they call `withdraw()` and each subsequent time they call `stake()`.

**Note that the flow in which a new user calls `stake()` and then `withdraw()` will result in ~54,200 gas savings between these two function calls + ~10k gas for each subsequent call to `stake()`/`withdraw()`**.

Since the `claimReward()` and `getPoolReward()` functions do not have individual tests that show gas usage, the `stake()` and `withdraw()` functions will be shown below as a POC for this gas optimization (both `stake()` and `withdraw()` result in the invocation of `claimReward()` and `getPoolReward()`).

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L319

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1310

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1433-L1435

### Gas Savings for `stake()`, obtained via protocol's tests: Avg 26202 gas

**Note that the average gas savings do not reflect the actual savings for this function since `stake()` will see the greatest savings when called by a new user. Observe the difference in the `Max` column, which will illustrate the savings a user will see when calling `stake()` for the first time (i.e. avoiding two `Gsset (20000 gas)` and two `Gcoldsload (2100 gas)`: 44537 gas.**

**In addition, the `MIN` column shows the gas savings of the `stake()` function when called by a recurring user (i.e. avoiding two `Gsreset (2900 gas)` and two `Gcoldsload (2100 gas)`): 10337 gas.**
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  154353  |  423836  |  277026  |    71   |
| After  |  144016  |  379299  |  250824  |    71   |

### Gas Savings for `withdraw()`, obtained via protocol's tests: Avg 9883 gas

**Note that the gas savings for this function will be similar to the savings recurring users experience for the `stake()` function, since in a normal flow the user would call `stake()` first before they call `withdraw()`.**
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  102046  |  267114  |  187080  |    18   |
| After  |  93794   |  256750  |  177197  |    18   |

```solidity
File: contracts/staking/NeoTokyoStaker.sol
319: 		mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;

1310: 		uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType]; // @audit: `Gcoldsload (2100 gas)`, called 3 times in `claimReward()` for each AssetType

1433:		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp; // @audit: 1st `Gsset (20000 gas)` for new users, `Gsreset (2900 gas) for recurring users
1434:		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp; // @audit: 2nd `Gsset (20000 gas)` for new users, `Gsreset (2900 gas) for recurring users
1435:		lastRewardTime[_recipient][AssetType.LP] = block.timestamp; // @audit: 3rd `Gsset (20000 gas)` for new users, `Gsreset (2900 gas) for recurring users
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..51df065 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -316,7 +316,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        mapping ( AssetType => PoolData ) private _pools;

        /// Track the last time a caller was granted their rewards for each asset.
-       mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
+       mapping ( address => uint256 ) public lastRewardTime;

        /**
                This admin-configurable double-mapping allows us to deduce the Identity
@@ -1307,7 +1307,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                                applicable time period.
                        */
                        uint256 totalReward;
-                       uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
+                       uint256 lastPoolRewardTime = lastRewardTime[_recipient];
                        uint256 windowCount = pool.rewardCount;
                        for (uint256 i; i < windowCount; ) {
                                RewardWindow memory window = pool.rewardWindows[i];
@@ -1430,9 +1430,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                );

                // Record the current time as the beginning time for checking rewards.
-               lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
+               lastRewardTime[_recipient] = block.timestamp;

                // Calculate total reward and tax.
                uint256 totalReward;
```

## State variables can be packed to use fewer storage slots
The EVM works with 32 byte words. Variables less than 32 bytes can be declared next to eachother in storage and this will pack the values together into a single 32 byte storage slot (if the values combined are <= 32 bytes). If the variables packed together are retrieved together in functions we will effectively save ~2000 gas with every subsequent SLOAD for that storage slot. This is due to us incurring a `Gwarmaccess (100 gas)` versus a `Gcoldsload (2100 gas)`.

Gas savings: `2000`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L232-L511

### We are able to pack `lpLocked` and `LP` into one storage slot to save 1 SLOT (~2000 gas).
```solidity
File: contracts/staking/NeoTokyoStaker.sol
231:	/// The address of the LP token contract.
232:	address public LP;
...
510:	/// Whether or not setting the LP token contract address is locked.
511:	bool public lpLocked;
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..075cb0c 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -228,6 +228,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        /// The address of the assembled Neo Tokyo S2 Citizen contract.
        address immutable public S2_CITIZEN;

+       /// Whether or not setting the LP token contract address is locked.
+       bool public lpLocked;
+
        /// The address of the LP token contract.
        address public LP;

@@ -507,8 +510,6 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                LPPosition stakedLPPosition;
        }

-       /// Whether or not setting the LP token contract address is locked.
-       bool public lpLocked;

        /**
                This struct records an input to the staker's `configurePools` function.
```

## State variables can be cached instead of re-reading them from storage
Caching of a state variable replaces each `Gwarmaccess (100 gas)` with a much cheaper stack read.

Total Instances: `27`

Gas savings: `27 * 100 = 2700` 

***Gas savings will actually be greater since storage slot access is occuring within loops in some instances**

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L710-L752

### Cache `_stakerS1Position[_staker].length` and `_stakerS2Position[_staker].length` to save 2 SLOADs 

*More SLOADs would actually be saved since storage slot access is occuring within a loop.*
```solidity
File: staking/NeoTokyoStaker.sol
710:	function getStakerPositions (
711:		address _staker
712:	) external view returns (StakerPosition memory) {
713:
714:		// Compile the S1 Citizen details.
715:		StakedS1CitizenOutput[] memory stakedS1Details =
716:			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length); // @audit: 1st sload
717:		for (uint256 i; i < _stakerS1Position[_staker].length; ) { // @audit: 2nd sload + on every iteration
718:			uint256 citizenId = _stakerS1Position[_staker][i];
719:			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
720:			stakedS1Details[i] = StakedS1CitizenOutput({
721:				citizenId: citizenId,
722:				stakedBytes: citizenDetails.stakedBytes,
723:				timelockEndTime: citizenDetails.timelockEndTime,
724:				points: citizenDetails.points,
725:				hasVault: citizenDetails.hasVault,
726:				stakedVaultId: citizenDetails.stakedVaultId
727:			});
728:			unchecked { i++; }
729:		}
730:
731:		// Compile the S2 Citizen details.
732:		StakedS2CitizenOutput[] memory stakedS2Details =
733:			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length); // @audit: 1st sload
734:		for (uint256 i; i < _stakerS2Position[_staker].length; ) { // @audit: 2nd sload + on every iteration
735:			uint256 citizenId = _stakerS2Position[_staker][i];
736:			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
737:			stakedS2Details[i] = StakedS2CitizenOutput({
738:				citizenId: citizenId,
739:				stakedBytes: citizenDetails.stakedBytes,
740:				timelockEndTime: citizenDetails.timelockEndTime,
741:				points: citizenDetails.points
742:			});
743:			unchecked { i++; }
744:		}
745:
746:		// Return the final output position struct.
747:		return StakerPosition({
748:			stakedS1Citizens: stakedS1Details,
749:			stakedS2Citizens: stakedS2Details,
750:			stakedLPPosition: stakerLPPosition[_staker]
751:		});
752:	}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..8d8ccc4 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -712,9 +712,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) external view returns (StakerPosition memory) {

                // Compile the S1 Citizen details.
+               uint256 s1Length = _stakerS1Position[_staker].length;
                StakedS1CitizenOutput[] memory stakedS1Details =
-                       new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
-               for (uint256 i; i < _stakerS1Position[_staker].length; ) {
+                       new StakedS1CitizenOutput[](s1Length);
+               for (uint256 i; i < s1Length; ) {
                        uint256 citizenId = _stakerS1Position[_staker][i];
                        StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
                        stakedS1Details[i] = StakedS1CitizenOutput({
@@ -729,9 +730,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // Compile the S2 Citizen details.
+               uint256 s2Length = _stakerS2Position[_staker].length;
                StakedS2CitizenOutput[] memory stakedS2Details =
-                       new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
-               for (uint256 i; i < _stakerS2Position[_staker].length; ) {
+                       new StakedS2CitizenOutput[](s2Length);
+               for (uint256 i; i < s2Length; ) {
                        uint256 citizenId = _stakerS2Position[_staker][i];
                        StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
                        stakedS2Details[i] = StakedS2CitizenOutput({
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L967-L978

### Cache the value from the expression given to `citizenStatus.points` and use that stack variable in other expression to save 1 SLOAD. 

**For example: `stackVar = (expression)` => `citizenStatus.points = stackVar` => `pool.totalPoints += stackVar`.**
```solidity
File: contracts/staking/NeoTokyoStaker.sol
967:		unchecked {
968:			citizenStatus.points = // @audit: can cache expression value as stack variable
969:				identityPoints * vaultMultiplier * timelockMultiplier /
970:				_DIVISOR / _DIVISOR;
971:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
972:
973:			// Record the caller's staked S1 Citizen.
974:			_stakerS1Position[msg.sender].push(citizenId);
975:
976:			// Update the pool point weights for rewards
977:			pool.totalPoints += citizenStatus.points; // @audit: unecessary sload, use cached value from expression
978:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..0469c3f 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -964,17 +964,19 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S1_CITIZEN];
+               uint256 points;
                unchecked {
-                       citizenStatus.points =
+                       points =
                                identityPoints * vaultMultiplier * timelockMultiplier /
                                _DIVISOR / _DIVISOR;
+                       citizenStatus.points = points;
                        citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

                        // Record the caller's staked S1 Citizen.
                        _stakerS1Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints += points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1021-L1030

### Optimization is identical to the one above. Saves 1 SLOAD.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1021:		unchecked {
1022:			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR; // @audit: can cache expression value as stack variable
1023:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
1024:
1025:			// Record the caller's staked S2 Citizen.
1026:			_stakerS2Position[msg.sender].push(citizenId);
1027:
1028:			// Update the pool point weights for rewards
1029:			pool.totalPoints += citizenStatus.points; // @audit: unecessary sload, use cached value from expression
1030:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..7f03c33 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1018,15 +1018,17 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S2_CITIZEN];
+               uint256 points;
                unchecked {
-                       citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
+                       points = 100 * timelockMultiplier / _DIVISOR;
+                       citizenStatus.points = points;
                        citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

                        // Record the caller's staked S2 Citizen.
                        _stakerS2Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints += points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1044-L1102

### Cache `citizenStatus.stakedBytes` to save 4 SLOADs.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1060:		if (seasonId == 1) {
1061:			StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
1062:			uint256 cap = VAULT_CAP;
1063:			if (!citizenStatus.hasVault) {
1064:				cap = NO_VAULT_CAP;
1065:			}
1066:			if (citizenStatus.stakedBytes + amount > cap) { // @audit: 1st sload
1067:				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap); // @aduit: 2nd sload
1068:			}
1069:
1070:			// Validate that the caller actually staked the Citizen.
1071:			if (citizenStatus.timelockEndTime == 0) {
1072:				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
1073:			}
1074:
1075:			PoolData storage pool = _pools[AssetType.S1_CITIZEN];
1076:			unchecked {
1077:				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
1078:				citizenStatus.stakedBytes += amount; // @audit: 3rd sload
1079:				citizenStatus.points += bonusPoints;
1080:				pool.totalPoints += bonusPoints;
1081:			}
1082:
1083:		// Handle staking BYTES into an S2 Citizen.
1084:		} else if (seasonId == 2) {
1085:			StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
1086:			uint256 cap = NO_VAULT_CAP;
1087:			if (citizenStatus.stakedBytes + amount > cap) { // @audit: 1st sload
1088:				revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap); // @audit: 2nd sload
1089:			}
1090:
1091:			// Validate that the caller actually staked the Citizen.
1092:			if (citizenStatus.timelockEndTime == 0) {
1093:				revert CannotStakeIntoUnownedCitizen(citizenId, seasonId);
1094:			}
1095:
1096:			PoolData storage pool = _pools[AssetType.S2_CITIZEN];
1097:			unchecked {
1098:				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
1099:				citizenStatus.stakedBytes += amount; // @audit: 3rd sload
1100:				citizenStatus.points += bonusPoints;
1101:				pool.totalPoints += bonusPoints;
1102:			}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..93a190d 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1059,12 +1059,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Handle staking BYTES into an S1 Citizen.
                if (seasonId == 1) {
                        StakedS1Citizen storage citizenStatus = stakedS1[msg.sender][citizenId];
+                       uint256 s1StakedBytes = citizenStatus.stakedBytes;
                        uint256 cap = VAULT_CAP;
                        if (!citizenStatus.hasVault) {
                                cap = NO_VAULT_CAP;
                        }
-                       if (citizenStatus.stakedBytes + amount > cap) {
-                               revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
+                       if (s1StakedBytes + amount > cap) {
+                               revert AmountExceedsCap(s1StakedBytes + amount, cap);
                        }

                        // Validate that the caller actually staked the Citizen.
@@ -1075,7 +1076,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        PoolData storage pool = _pools[AssetType.S1_CITIZEN];
                        unchecked {
                                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
-                               citizenStatus.stakedBytes += amount;
+                               citizenStatus.stakedBytes = s1StakedBytes + amount;
                                citizenStatus.points += bonusPoints;
                                pool.totalPoints += bonusPoints;
                        }
@@ -1083,9 +1084,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Handle staking BYTES into an S2 Citizen.
                } else if (seasonId == 2) {
                        StakedS2Citizen storage citizenStatus = stakedS2[msg.sender][citizenId];
+                       uint256 s2StakedBytes = citizenStatus.stakedBytes;
                        uint256 cap = NO_VAULT_CAP;
-                       if (citizenStatus.stakedBytes + amount > cap) {
-                               revert AmountExceedsCap(citizenStatus.stakedBytes + amount, cap);
+                       if (s2StakedBytes + amount > cap) {
+                               revert AmountExceedsCap(s2StakedBytes + amount, cap);
                        }

                        // Validate that the caller actually staked the Citizen.
@@ -1096,7 +1098,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        PoolData storage pool = _pools[AssetType.S2_CITIZEN];
                        unchecked {
                                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
-                               citizenStatus.stakedBytes += amount;
+                               citizenStatus.stakedBytes = s2StakedBytes + amount;
                                citizenStatus.points += bonusPoints;
                                pool.totalPoints += bonusPoints;
                        }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1137-L1174

### Cache `LP` and `stakerLPPosition[msg.sender].multiplier` to save 2 SLOADs.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1137:		_assetTransferFrom(LP, msg.sender, address(this), amount); // @audit: 1st sload for `LP`
1138:
1139:		// Decode the timelock option's duration and multiplier.
1140:		uint256 timelockDuration = _timelock >> 128;
1141:		uint256 timelockMultiplier = _timelock & type(uint128).max;
1142:
1143:		// If this is a new stake of this asset, initialize the multiplier details.
1144:		if (stakerLPPosition[msg.sender].multiplier == 0) { // @audit: 1st sload for `stakerLPPosition[msg.sender].multiplier`
1145:			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;
1146:
1147:		// If a multiplier exists already, we must match it.
1148:		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) { // @audit: 2nd sload for `stakerLPPosition[msg.sender].multiplier`
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
1162:
1163:			// Update the pool point weights for rewards.
1164:			pool.totalPoints += points;
1165:		}
1166:
1167:		// Emit an event recording this LP staking.
1168:		emit Stake(
1169:			msg.sender,
1170:			LP, // @audit: 2nd sload for `LP`
1171:			_timelock,
1172:			amount
1173:		);
1174:	}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..3c7343e 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1134,18 +1134,20 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        contract. This transfer will fail if the caller does not hold enough
                        tokens.
                */
-               _assetTransferFrom(LP, msg.sender, address(this), amount);
+               address lp = LP;
+               _assetTransferFrom(lp, msg.sender, address(this), amount);

                // Decode the timelock option's duration and multiplier.
                uint256 timelockDuration = _timelock >> 128;
                uint256 timelockMultiplier = _timelock & type(uint128).max;

                // If this is a new stake of this asset, initialize the multiplier details.
-               if (stakerLPPosition[msg.sender].multiplier == 0) {
+               uint256 multiplier = stakerLPPosition[msg.sender].multiplier;
+               if (multiplier == 0) {
                        stakerLPPosition[msg.sender].multiplier = timelockMultiplier;

                // If a multiplier exists already, we must match it.
-               } else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
+               } else if (multiplier != timelockMultiplier) {
                        revert MismatchedTimelock();
                }

@@ -1167,7 +1169,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Emit an event recording this LP staking.
                emit Stake(
                        msg.sender,
-                       LP,
+                       lp,
                        _timelock,
                        amount
                );
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1264-L1396

### Cache `pool.totalPoints` to save 1 SLOAD.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1273:		PoolData storage pool = _pools[_assetType];
1274:		if (pool.totalPoints != 0) { // @audit: 1st sload
...
1276:			// Calculate the total number of points accrued to the `_recipient`.
...
1386:			// Return final shares.
1387:			unchecked {
1388:				uint256 share = points * _PRECISION / pool.totalPoints * totalReward; // @audit: 2nd sload
1389:				uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
1390:				share /= _PRECISION;
1391:				daoShare /= _PRECISION;
1392:				return ((share - daoShare), daoShare);
1393:			}
1394:		}
1395:		return (0, 0);
1396:	}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..9431d53 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1271,7 +1271,8 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        this case, do not attempt to grant any rewards so as to prevent reversion.
                */
                PoolData storage pool = _pools[_assetType];
-               if (pool.totalPoints != 0) {
+               uint256 totalPoints = pool.totalPoints;
+               if (totalPoints != 0) {

                        // Calculate the total number of points accrued to the `_recipient`.
                        uint256 points;
@@ -1385,7 +1387,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                        // Return final shares.
                        unchecked {
-                               uint256 share = points * _PRECISION / pool.totalPoints * totalReward;
+                               uint256 share = points * _PRECISION / totalPoints * totalReward;
                                uint256 daoShare = share * pool.daoTax / (100 * _DIVISOR);
                                share /= _PRECISION;
                                daoShare /= _PRECISION;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1459-L1529

### Cache `stakedCitizen.timelockEndTime`, `stakedCitizen.stakedBytes`, `stakedCitizen.stakedVaultId` and `oldPosition.length` to save 6 SLOADs.

*More SLOADs would actually be saved since storage slot access is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1459:	function _withdrawS1Citizen () private {
1460:		uint256 citizenId;
1461:		assembly {
1462:			citizenId := calldataload(0x24)
1463:		}
1464:
1465:		// Validate that the caller has cleared their asset timelock.
1466:		StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];
1467:		if (block.timestamp < stakedCitizen.timelockEndTime) { // @audit: 1st sload
1468:			revert TimelockNotCleared(stakedCitizen.timelockEndTime); // @audit: 2nd sload
1469:		}
1470:
1471:		// Validate that the caller actually staked this asset.
1472:		if (stakedCitizen.timelockEndTime == 0) { // @audit: 3rd sload
1473:			revert CannotWithdrawUnownedS1(citizenId);
1474:		}
1475:		
1476:		// Return any staked BYTES.
1477:		if (stakedCitizen.stakedBytes > 0) { // @audit: 1st sload
1478:			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes); // @audit: 2nd sload
1479:		}
1480:		
1481:		// Return any non-component Vault if one is present.
1482:		if (stakedCitizen.stakedVaultId != 0) { // @audit: 1st sload
1483:			_assetTransferFrom(
1484:				VAULT,
1485:				address(this),
1486:				msg.sender,
1487:				stakedCitizen.stakedVaultId // @audit: 2nd sload
1488:			);
1489:		}
1490:
1491:		// Return the S1 Citizen.
1492:		_assetTransferFrom(S1_CITIZEN, address(this), msg.sender, citizenId);
1493:
1494:		/*
1495:			Check each citizen ID to find its index and remove the token from the
1496:			staked item array of its old position.
1497:		*/
1498:		uint256[] storage oldPosition = _stakerS1Position[msg.sender];
1499:		for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) { // @audit: 1st sload + on every iteration
1500:
1501:			// Remove the element at the matching index.
1502:			if (citizenId == oldPosition[stakedIndex]) {
1503:				if (stakedIndex != oldPosition.length - 1) { // @audit: 2nd sload + on every iteration
1504:					oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1]; // @audit: 3rd sload + on every iteration
1505:				}
1506:				oldPosition.pop();
1507:				break;
1508:			}
1509:			unchecked { stakedIndex++; }
1510:		}
```
```diff
diff --git a/./contracts/staking/NeoTokyoStaker.sol b/./contracts/staking/NeoTokyoStakerNew.sol
index a54d218..2fa62b4 100644
--- a/./contracts/staking/NeoTokyoStaker.sol
+++ b/./contracts/staking/NeoTokyoStakerNew.sol
@@ -1464,27 +1464,30 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Validate that the caller has cleared their asset timelock.
                StakedS1Citizen storage stakedCitizen = stakedS1[msg.sender][citizenId];
-               if (block.timestamp < stakedCitizen.timelockEndTime) {
-                       revert TimelockNotCleared(stakedCitizen.timelockEndTime);
+               uint256 timelockEndTime = stakedCitizen.timelockEndTime;
+               if (block.timestamp < timelockEndTime) {
+                       revert TimelockNotCleared(timelockEndTime);
                }

                // Validate that the caller actually staked this asset.
-               if (stakedCitizen.timelockEndTime == 0) {
+               if (timelockEndTime == 0) {
                        revert CannotWithdrawUnownedS1(citizenId);
                }

                // Return any staked BYTES.
-               if (stakedCitizen.stakedBytes > 0) {
-                       _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
+               uint256 stakedBytes = stakedCitizen.stakedBytes;
+               if (stakedBytes > 0) {
+                       _assetTransfer(BYTES, msg.sender, stakedBytes);
                }

                // Return any non-component Vault if one is present.
-               if (stakedCitizen.stakedVaultId != 0) {
+               uint256 stakedVaultId = stakedCitizen.stakedVaultId;
+               if (stakedVaultId != 0) {
                        _assetTransferFrom(
                                VAULT,
                                address(this),
                                msg.sender,
-                               stakedCitizen.stakedVaultId
+                               stakedVaultId
                        );
                }

@@ -1496,12 +1499,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        staked item array of its old position.
                */
                uint256[] storage oldPosition = _stakerS1Position[msg.sender];
-               for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
+               uint256 length = oldPosition.length;
+               for (uint256 stakedIndex; stakedIndex < length; ) {

                        // Remove the element at the matching index.
                        if (citizenId == oldPosition[stakedIndex]) {
-                               if (stakedIndex != oldPosition.length - 1) {
-                                       oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
+                               if (stakedIndex != length - 1) {
+                                       oldPosition[stakedIndex] = oldPosition[length - 1];
                                }
                                oldPosition.pop();
                                break;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1534-L1592

### Cache `stakedCitizen.timelockEndTime`, `stakedCitizen.stakedBytes`, and `oldPosition.length` as stack variables to save 5 SLOADs.

*More SLOADs would actually be saved since storage slot access is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1542:		if (block.timestamp < stakedCitizen.timelockEndTime) { // @audit: 1st sload
1543:			revert TimelockNotCleared(stakedCitizen.timelockEndTime); // @audit: 2nd sload
1544:		}
1545:
1546:		// Validate that the caller actually staked this asset.
1547:		if (stakedCitizen.timelockEndTime == 0) { // @audit: 3rd sload
1548:			revert CannotWithdrawUnownedS2(citizenId);
1549:		}
1550:
1551:		// Return any staked BYTES.
1552:		if (stakedCitizen.stakedBytes > 0) { // @audit: 1st sload
1553:			_assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes); // @audit: 2nd sload
1554:		}
1555:
1556:		// Return the S2 Citizen.
1557:		_assetTransferFrom(S2_CITIZEN, address(this), msg.sender, citizenId);
1558:
1559:		/*
1560:			Check each citizen ID to find its index and remove the token from the
1561:			staked item array of its old position.
1562:		*/
1563:		uint256[] storage oldPosition = _stakerS2Position[msg.sender];
1564:		for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) { // @audit: 1st sload + on every iteration
1565:
1566:			// Remove the element at the matching index.
1567:			if (citizenId == oldPosition[stakedIndex]) {
1568:				if (stakedIndex != oldPosition.length - 1) { // @audit: 2nd sload + on every iteration
1569:					oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1]; // @audit: 3rd sload + on every iteration
1570:				}
1571:				oldPosition.pop();
1572:				break;
1573:			}
1574:			unchecked { stakedIndex++; }
1575:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..f975466 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1539,18 +1539,20 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Validate that the caller has cleared their asset timelock.
                StakedS2Citizen storage stakedCitizen = stakedS2[msg.sender][citizenId];
-               if (block.timestamp < stakedCitizen.timelockEndTime) {
-                       revert TimelockNotCleared(stakedCitizen.timelockEndTime);
+               uint256 timelockEndTime = stakedCitizen.timelockEndTime;
+               if (block.timestamp < timelockEndTime) {
+                       revert TimelockNotCleared(timelockEndTime);
                }

                // Validate that the caller actually staked this asset.
-               if (stakedCitizen.timelockEndTime == 0) {
+               if (timelockEndTime == 0) {
                        revert CannotWithdrawUnownedS2(citizenId);
                }

                // Return any staked BYTES.
-               if (stakedCitizen.stakedBytes > 0) {
-                       _assetTransfer(BYTES, msg.sender, stakedCitizen.stakedBytes);
+               uint256 stakedBytes = stakedCitizen.stakedBytes;
+               if (stakedBytes > 0) {
+                       _assetTransfer(BYTES, msg.sender, stakedBytes);
                }

                // Return the S2 Citizen.
@@ -1561,12 +1563,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        staked item array of its old position.
                */
                uint256[] storage oldPosition = _stakerS2Position[msg.sender];
-               for (uint256 stakedIndex; stakedIndex < oldPosition.length; ) {
+               uint256 length = oldPosition.length;
+               for (uint256 stakedIndex; stakedIndex < length; ) {

                        // Remove the element at the matching index.
                        if (citizenId == oldPosition[stakedIndex]) {
-                               if (stakedIndex != oldPosition.length - 1) {
-                                       oldPosition[stakedIndex] = oldPosition[oldPosition.length - 1];
+                               if (stakedIndex != length - 1) {
+                                       oldPosition[stakedIndex] = oldPosition[length - 1];
                                }
                                oldPosition.pop();
                                break;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1605-L1644

### Cache `lpPosition.timelockEndTime`, `lpPosition.amount`, and `LP` as stack variables to save 5 SLOADs.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1605:		if (block.timestamp < lpPosition.timelockEndTime) { // @audit: 1st slod
1606:			revert TimelockNotCleared(lpPosition.timelockEndTime); // @audit: 2nd sload
1607:		}
1608:
1609:		// Validate that the caller has enough staked LP tokens to withdraw.
1610:		if (lpPosition.amount < amount) { // @audit 1st sload for `lpPosition.amount`
1611:			revert NotEnoughLPTokens(amount, lpPosition.amount); // @audit: 2nd sload for `lpPosition.amount`
1612:		}
1613:
1614:		/*
1615:			Attempt to transfer the LP tokens held in escrow by this staking contract 
1616:			back to the caller.
1617:		*/
1618:		_assetTransfer(LP, msg.sender, amount); // @audit: 1st sload for `LP`
1619:
1620:		// Update caller staking information and asset data.
1621:		PoolData storage pool = _pools[AssetType.LP];
1622:		unchecked {
1623:			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
1624:
1625:			// Update the caller's LP token stake.
1626:			lpPosition.amount -= amount; // @audit: 3rd sload for `lpPosition.amount`
1627:			lpPosition.points -= points;
1628:
1629:			// Update the pool point weights for rewards.
1630:			pool.totalPoints -= points;
1631:		}
1632:
1633:		// If all LP tokens are withdrawn, we must clear the multiplier.
1634:		if (lpPosition.amount == 0) { // @audit 4th sload for `lpPosition.amount`
1635:			lpPosition.multiplier = 0;
1636:		}
1637:
1638:		// Emit an event recording this LP withdraw.
1639:		emit Withdraw(
1640:			msg.sender,
1641:			LP, // @audit: 2nd sload for `LP`
1642:			amount
1643:		);
1644:	}
```
```diff
diff --git a/./contracts/staking/NeoTokyoStaker.sol b/./contracts/staking/NeoTokyoStakerNew.sol
index a54d218..a7c34a9 100644
--- a/./contracts/staking/NeoTokyoStaker.sol
+++ b/./contracts/staking/NeoTokyoStakerNew.sol
@@ -1602,20 +1602,23 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                // Validate that the caller has cleared their asset timelock.
                LPPosition storage lpPosition = stakerLPPosition[msg.sender];
-               if (block.timestamp < lpPosition.timelockEndTime) {
-                       revert TimelockNotCleared(lpPosition.timelockEndTime);
+               uin256 timelockEndTime = lpPosition.timelockEndTime;
+               if (block.timestamp < timelockEndTime) {
+                       revert TimelockNotCleared(timelockEndTime);
                }

                // Validate that the caller has enough staked LP tokens to withdraw.
-               if (lpPosition.amount < amount) {
-                       revert NotEnoughLPTokens(amount, lpPosition.amount);
+               uint256 lpAmount = lpPosition.amount;
+               if (lpAmount < amount) {
+                       revert NotEnoughLPTokens(amount, lpAmount);
                }

                /*
                        Attempt to transfer the LP tokens held in escrow by this staking contract
                        back to the caller.
                */
-               _assetTransfer(LP, msg.sender, amount);
+               address lp = LP;
+               _assetTransfer(lp, msg.sender, amount);

                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.LP];
@@ -1623,7 +1626,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

                        // Update the caller's LP token stake.
-                       lpPosition.amount -= amount;
+                       lpPosition.amount = lpAmount - amount;
                        lpPosition.points -= points;

                        // Update the pool point weights for rewards.
@@ -1631,14 +1634,14 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // If all LP tokens are withdrawn, we must clear the multiplier.
-               if (lpPosition.amount == 0) {
+               if (lpAmount == 0) {
                        lpPosition.multiplier = 0;
                }

                // Emit an event recording this LP withdraw.
                emit Withdraw(
                        msg.sender,
-                       LP,
+                       lp,
                        amount
                );
        }
```

## Avoid emitting constants.
A log topic (declared with `indexed`) has a gas cost of `Glogtopic (375 gas)`. The [`Stake`](https://github.com/code-423n4/2023-03-https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L567neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L539) and [`Withdraw`]() events' second indexed parameter is a constant for a majority of events emitted (with the exception of the events emitted in the `_stakeLP()` and `_withdrawLP()` functions) and is unecessary to emit since the value will never change. Alternatively, you can avoid incurring the `Glogtopic (375 gas)` per call to any function that emits `Stake`/`Withdraw` (with the exception of `_stakeLP()` and `_withdrawLP()`) by creating separate events for each staking/withdraw function and opt out of using the current `indexed asset` topic in each event. This way you can still query the different staking/withdraw events and will save 375 gas for each staking/withdraw function (with the exception of `_stakeLP()` and `_withdrawLP()`). 

*Note that the events emitted in the [`_stakeLP()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1168) and [`withdrawLP()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1639) functions are not considered for this issue since the second indexed parameter is for the `LP` storage variable, which can be changed via the `configureLP()` function.*

Instances: 5

Gas Savings: `5 * 375 = 1875`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L981-L986

*`S1_CITIZEN` is a constant*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
981:		emit Stake(
982:			msg.sender,
983:			S1_CITIZEN,
984:			_timelock,
985:			citizenId
986:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1033-L1038

*`S2_CITIZEN` is a constant*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1033:		emit Stake(
1034:			msg.sender,
1035:			S2_CITIZEN,
1036:			_timelock,
1037:			citizenId
1038:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1110-L1115

*`BYTES` is a constant*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1110:		emit Stake(
1111:			msg.sender,
1112:			BYTES,
1113:			(seasonId << 128) + citizenId,
1114:			amount
1115:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1524-L1528
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1524:		emit Withdraw(
1525:			msg.sender,
1526:			S1_CITIZEN,
1527:			citizenId
1528:		);
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1587-L1591
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1587:		emit Withdraw(
1589:			msg.sender,
1590:			S2_CITIZEN,
1591:			citizenId
1592:		);
```

## Multiple accesses of a mapping/array should use a storage pointer
Caching a mapping's value in a storage pointer when the value is accessed multiple times saves ~40 gas per access due to not having to perform the same offset calculation every time. Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it.

To achieve this, declare a storage pointer for the variable and use it instead of repeatedly fetching the reference in a map or an array. As an example, instead of repeatedly calling `_stakerS1Position[_staker]`, save its reference via a storage pointer: `uint256[] storage s1Array = _stakerS1Position[_staker]` and use the pointer instead.

Total instances: 25

Gas savings: `25 * 40 = 1000`

***Gas savings will actually be greater since storage slot offset calculation is occuring within loops in some instances**

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L715-L744

### Cache storage pointers for `_stakerS1Position[_staker]`, `_stakerS2Position[_staker]`, `stakedS1[_staker]`, and `stakedS2[_staker]`.

*More gas would actually be saved since storage slot offset calculation is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
715:		StakedS1CitizenOutput[] memory stakedS1Details =
716:			new StakedS1CitizenOutput[](_stakerS1Position[_staker].length); // @audit: 1st access
717:		for (uint256 i; i < _stakerS1Position[_staker].length; ) { // @audit: 2nd access + on every iteration
718:			uint256 citizenId = _stakerS1Position[_staker][i]; // @audit: 3rd access + on every iteration
719:			StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId]; // @audit: accessed on every iteration
720:			stakedS1Details[i] = StakedS1CitizenOutput({
721:				citizenId: citizenId,
722:				stakedBytes: citizenDetails.stakedBytes,
723:				timelockEndTime: citizenDetails.timelockEndTime,
724:				points: citizenDetails.points,
725:				hasVault: citizenDetails.hasVault,
726:				stakedVaultId: citizenDetails.stakedVaultId
727:			});
728:			unchecked { i++; }
729:		}
730:
731:		// Compile the S2 Citizen details.
732:		StakedS2CitizenOutput[] memory stakedS2Details =
733:			new StakedS2CitizenOutput[](_stakerS2Position[_staker].length); // @audit: 1st access
734:		for (uint256 i; i < _stakerS2Position[_staker].length; ) { // @audit: 2nd access + on every iteration
735:			uint256 citizenId = _stakerS2Position[_staker][i]; // @audit: 3rd access + on every iteration
736:			StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId]; // @audit: accessed on every iteration
737:			stakedS2Details[i] = StakedS2CitizenOutput({
738:				citizenId: citizenId,
739:				stakedBytes: citizenDetails.stakedBytes,
740:				timelockEndTime: citizenDetails.timelockEndTime,
741:				points: citizenDetails.points
742:			});
743:			unchecked { i++; }
744:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..e078701 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -712,11 +712,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
        ) external view returns (StakerPosition memory) {

                // Compile the S1 Citizen details.
+               uint256[] storage s1Array = _stakerS1Position[_staker];
+               mapping (uint256 => StakedS1Citizen) storage s1CitizenMap = stakedS1[_staker];
                StakedS1CitizenOutput[] memory stakedS1Details =
-                       new StakedS1CitizenOutput[](_stakerS1Position[_staker].length);
-               for (uint256 i; i < _stakerS1Position[_staker].length; ) {
-                       uint256 citizenId = _stakerS1Position[_staker][i];
-                       StakedS1Citizen memory citizenDetails = stakedS1[_staker][citizenId];
+                       new StakedS1CitizenOutput[](s1Array.length);
+               for (uint256 i; i < s1Array.length; ) {
+                       uint256 citizenId = s1Array[i];
+                       StakedS1Citizen memory citizenDetails = s1CitizenMap[citizenId];
                        stakedS1Details[i] = StakedS1CitizenOutput({
                                citizenId: citizenId,
                                stakedBytes: citizenDetails.stakedBytes,
@@ -729,11 +731,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // Compile the S2 Citizen details.
+               uint256[] storage s2Array = _stakerS2Position[_staker];
+               mapping (uint256 => StakedS2Citizen) storage s2CitizenMap = stakedS2[_staker];
                StakedS2CitizenOutput[] memory stakedS2Details =
-                       new StakedS2CitizenOutput[](_stakerS2Position[_staker].length);
-               for (uint256 i; i < _stakerS2Position[_staker].length; ) {
-                       uint256 citizenId = _stakerS2Position[_staker][i];
-                       StakedS2Citizen memory citizenDetails = stakedS2[_staker][citizenId];
+                       new StakedS2CitizenOutput[](s2Array.length);
+               for (uint256 i; i < s2Array.length; ) {
+                       uint256 citizenId = s2Array[i];
+                       StakedS2Citizen memory citizenDetails = s2CitizenMap[citizenId];
                        stakedS2Details[i] = StakedS2CitizenOutput({
                                citizenId: citizenId,
                                stakedBytes: citizenDetails.stakedBytes,
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1144-L1165

### Cache storage pointer for `stakerLPPosition[msg.sender]`.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1144:		if (stakerLPPosition[msg.sender].multiplier == 0) { // @audit: 1st access
1145:			stakerLPPosition[msg.sender].multiplier = timelockMultiplier; // @audit: 2nd access
1146:
1147:		// If a multiplier exists already, we must match it.
1148:		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) { // @audit: 3rd access
1149:			revert MismatchedTimelock();
1150:		}
1151:
1152:		// Update caller staking information and asset data.
1153:		PoolData storage pool = _pools[AssetType.LP];
1154:		unchecked {
1155:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
1156:
1157:			// Update the caller's LP token stake.
1158:			stakerLPPosition[msg.sender].timelockEndTime = // @audit: 4th access
1159:				block.timestamp + timelockDuration;
1160:			stakerLPPosition[msg.sender].amount += amount; // @audit: 5th & 6th access
1161:			stakerLPPosition[msg.sender].points += points; // @audit: 7th & 8th access
1162:
1163:			// Update the pool point weights for rewards.
1164:			pool.totalPoints += points;
1165:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..7993a96 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1141,11 +1141,12 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                uint256 timelockMultiplier = _timelock & type(uint128).max;

                // If this is a new stake of this asset, initialize the multiplier details.
-               if (stakerLPPosition[msg.sender].multiplier == 0) {
-                       stakerLPPosition[msg.sender].multiplier = timelockMultiplier;
+               LPPosition storage stakerPosition = stakerLPPosition[msg.sender];
+               if (stakerPosition.multiplier == 0) {
+                       stakerPosition.multiplier = timelockMultiplier;

                // If a multiplier exists already, we must match it.
-               } else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
+               } else if (stakerPosition.multiplier != timelockMultiplier) {
                        revert MismatchedTimelock();
                }

@@ -1155,10 +1156,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;

                        // Update the caller's LP token stake.
-                       stakerLPPosition[msg.sender].timelockEndTime =
+                       stakerPosition.timelockEndTime =
                                block.timestamp + timelockDuration;
-                       stakerLPPosition[msg.sender].amount += amount;
-                       stakerLPPosition[msg.sender].points += points;
+                       stakerPosition.amount += amount;
+                       stakerPosition.points += points;

                        // Update the pool point weights for rewards.
                        pool.totalPoints += points;
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1210-L1217

### Cache storage pointer for `_pools[_assetType]`.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1210:		if (_pools[_assetType].rewardCount == 0) { // @audit: 1st access
1211:			revert UnconfiguredPool(uint256(_assetType));
1212:		}
1213:
1214:		// Validate that the asset being staked matches an active pool.
1215:		if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) { // @audit: 2nd access
1216:			revert InactivePool(uint256(_assetType));
1217:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..b81a227 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1207,12 +1207,13 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                }

                // Validate that the asset being staked matches a configured pool.
-               if (_pools[_assetType].rewardCount == 0) {
+               PoolData storage pool = _pools[_assetType];
+               if (pool.rewardCount == 0) {
                        revert UnconfiguredPool(uint256(_assetType));
                }

                // Validate that the asset being staked matches an active pool.
-               if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
+               if (pool.rewardWindows[0].startTime >= block.timestamp) {
                        revert InactivePool(uint256(_assetType));
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1277-L1368

### Cache storage pointers for `_stakerS1Position[_recipient]`, `_stakerS2Position[_recipient]`, `stakedS1[_recipient]`, `stakedS2[_recipient]`, and `pool.rewardWindows`.

*More gas would actually be saved since storage slot offset calculation is occuring within a loop.*
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1277:			uint256 points;
1278:			if (_assetType == AssetType.S1_CITIZEN) {
1279:				for (uint256 i; i < _stakerS1Position[_recipient].length; ) { // @audit: 1st access + on every iteration
1280:					uint256 citizenId = _stakerS1Position[_recipient][i]; // @audit: 2nd access + on every iteration
1281:					StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId]; // @audit: accessed on every iteration
1282:					unchecked {
1283:						points += s1Citizen.points;
1284:						i++;
1285:					}
1286:				}
1287:			} else if (_assetType == AssetType.S2_CITIZEN) {
1288:				for (uint256 i; i < _stakerS2Position[_recipient].length; ) { // @audit: 1st access + on every iteration
1289:					uint256 citizenId = _stakerS2Position[_recipient][i]; // @audit: 2nd access + on every iteration
1290:					StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId]; // @audit: accessed on every iteration
1291:					unchecked {
1292:						points += s2Citizen.points;
1293:						i++;
1294:					}
1295:				}
1296:			} else if (_assetType == AssetType.LP) {
1297:				unchecked {
1298:					points += stakerLPPosition[_recipient].points;
1299:				}
1300:			} else {
1301:				revert InvalidAssetType(uint256(_assetType));
1302:			}
1303:
1304:			/*
1305:				Determine the reward for the `_recipient` based on their points total. 
1306:				Iterate through the entire array of pool reward windows to find the 
1307:				applicable time period.
1308:			*/
1309:			uint256 totalReward;
1310:			uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
1311:			uint256 windowCount = pool.rewardCount;
1312:			for (uint256 i; i < windowCount; ) {
1314:				RewardWindow memory window = pool.rewardWindows[i]; // @audit: 1st access + on every iteration
1315:
1316:				/*
1317:					If the last reward time is less than the starting time of this 
1318:					window, then the reward was accrued in the previous window.
1319:				*/
1320:				if (lastPoolRewardTime < window.startTime) {
1321:					uint256 currentRewardRate = pool.rewardWindows[i - 1].reward; // @audit: 2nd access + on every iteration
1322:
1323:					/*
1324:						Iterate forward to the present timestamp over any unclaimed reward 
1325:						windows.
1326:					*/
1327:					for (uint256 j = i; j < windowCount; ) {
1328:
1329:						// If the current time falls within this window, complete.
1330:						if (block.timestamp <= window.startTime) {
1331:							unchecked {
1332:								uint256 timeSinceReward = block.timestamp - lastPoolRewardTime;
1333:								totalReward += currentRewardRate * timeSinceReward;	
1334:							}
1335:
1336:							// We have no forward goto and thus include this bastardry.
1337:							i = windowCount;
1338:							break;
1339:
1340:						// Otherwise, accrue the remainder of this window and iterate.
1341:						} else {
1342:							unchecked {
1343:								uint256 timeSinceReward = window.startTime - lastPoolRewardTime;
1344:								totalReward += currentRewardRate * timeSinceReward;
1345:							}
1346:							currentRewardRate = window.reward;
1347:							lastPoolRewardTime = window.startTime;
1348:
1349:							/*
1350:								Handle the special case of overrunning the final window by 
1351:								fulfilling the prior window and then jumping forward to use the 
1352:								final reward window.
1353:							*/
1354:							if (j == windowCount - 1) {
1355:								unchecked {
1356:									uint256 timeSinceReward =
1357:										block.timestamp - lastPoolRewardTime;
1358:									totalReward += currentRewardRate * timeSinceReward;
1359:								}
1360:	
1361:								// We have no forward goto and thus include this bastardry.
1362:								i = windowCount;
1363:								break;
1364:	
1365:							// Otherwise, iterate.
1366:							} else {
1367:								window = pool.rewardWindows[j + 1]; // @audit: 3rd access + on every iteration
1368:							}
1369:						}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..0a58815 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1276,18 +1276,22 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        // Calculate the total number of points accrued to the `_recipient`.
                        uint256 points;
                        if (_assetType == AssetType.S1_CITIZEN) {
-                               for (uint256 i; i < _stakerS1Position[_recipient].length; ) {
-                                       uint256 citizenId = _stakerS1Position[_recipient][i];
-                                       StakedS1Citizen memory s1Citizen = stakedS1[_recipient][citizenId];
+                               uint256[] storage s1Array = _stakerS1Position[_recipient];
+                               mapping (uint256 => StakedS1Citizen) storage s1CitizenMap = stakedS1[_recipient];
+                               for (uint256 i; i < s1Array.length; ) {
+                                       uint256 citizenId = s1Array[i];
+                                       StakedS1Citizen memory s1Citizen = s1CitizenMap[citizenId];
                                        unchecked {
                                                points += s1Citizen.points;
                                                i++;
                                        }
                                }
                        } else if (_assetType == AssetType.S2_CITIZEN) {
-                               for (uint256 i; i < _stakerS2Position[_recipient].length; ) {
-                                       uint256 citizenId = _stakerS2Position[_recipient][i];
-                                       StakedS2Citizen memory s2Citizen = stakedS2[_recipient][citizenId];
+                               uint256[] storage s2Array = _stakerS2Position[_recipient];
+                               mapping (uint256 => StakedS2Citizen) storage s2CitizenMap = stakedS2[_recipient];
+                               for (uint256 i; i < s2Array.length; ) {
+                                       uint256 citizenId = s2Array[i];
+                                       StakedS2Citizen memory s2Citizen = s2CitizenMap[citizenId];
                                        unchecked {
                                                points += s2Citizen.points;
                                                i++;
@@ -1309,15 +1313,16 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 totalReward;
                        uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
                        uint256 windowCount = pool.rewardCount;
+                       mapping (uint256 => RewardWindow) storage rewardWindowsMap = pool.rewardWindows;
                        for (uint256 i; i < windowCount; ) {
-                               RewardWindow memory window = pool.rewardWindows[i];
+                               RewardWindow memory window = rewardWindowsMap[i];

                                /*
                                        If the last reward time is less than the starting time of this
                                        window, then the reward was accrued in the previous window.
                                */
                                if (lastPoolRewardTime < window.startTime) {
-                                       uint256 currentRewardRate = pool.rewardWindows[i - 1].reward;
+                                       uint256 currentRewardRate = rewardWindowsMap[i - 1].reward;

                                        /*
                                                Iterate forward to the present timestamp over any unclaimed reward
@@ -1363,7 +1368,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {

                                                        // Otherwise, iterate.
                                                        } else {
-                                                               window = pool.rewardWindows[j + 1];
+                                                               window = rewardWindowsMap[j + 1];
                                                        }
                                                }
                                                unchecked { j++; }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1432-L1435

### Cache storage pointer for `lastRewardTime[_recipient]`.
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1432:		// Record the current time as the beginning time for checking rewards.
1433:		lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp; // @audit: 1st access
1434:		lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp; // @audit: 2nd access
1435:		lastRewardTime[_recipient][AssetType.LP] = block.timestamp; // @audit: 3rd access
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..4621b10 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1430,9 +1430,10 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                );

                // Record the current time as the beginning time for checking rewards.
-               lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
-               lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
+               mapping (AssetType => uint256) storage recipient = lastRewardTime[_recipient];
+               recipient[AssetType.S1_CITIZEN] = block.timestamp;
+               recipient[AssetType.S2_CITIZEN] = block.timestamp;
+               recipient[AssetType.LP] = block.timestamp;

                // Calculate total reward and tax.
                uint256 totalReward;
```

## `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables

Total Instances: 13

Gas Savings: `13 * 20 = 260`

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L875-L987

```solidity
File: contracts/staking/NeoTokyoStaker.sol
967:		unchecked {
968:			citizenStatus.points =
969:				identityPoints * vaultMultiplier * timelockMultiplier /
970:				_DIVISOR / _DIVISOR;
971:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
972:
973:			// Record the caller's staked S1 Citizen.
974:			_stakerS1Position[msg.sender].push(citizenId);
975:
976:			// Update the pool point weights for rewards
977:			pool.totalPoints += citizenStatus.points;
978:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..f7c4a13 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -974,7 +974,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        _stakerS1Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints = pool.totalPoints + citizenStatus.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1021-L1030

```solidity
File: contracts/staking/NeoTokyoStaker.sol
1021:		unchecked {
1022:			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
1023:			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;
1024:
1025:			// Record the caller's staked S2 Citizen.
1026:			_stakerS2Position[msg.sender].push(citizenId);
1027:
1028:			// Update the pool point weights for rewards
1029:			pool.totalPoints += citizenStatus.points;
1030:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..e940f8e 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1026,7 +1026,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        _stakerS2Position[msg.sender].push(citizenId);

                        // Update the pool point weights for rewards
-                       pool.totalPoints += citizenStatus.points;
+                       pool.totalPoints = pool.totalPoints + citizenStatus.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1097-L1102
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1097:			unchecked {
1098:				uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
1099:				citizenStatus.stakedBytes += amount;
1100:				citizenStatus.points += bonusPoints;
1101:				pool.totalPoints += bonusPoints;
1102:			}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..af3d3de 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1096,9 +1096,9 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        PoolData storage pool = _pools[AssetType.S2_CITIZEN];
                        unchecked {
                                uint256 bonusPoints = (amount * 100 / _BYTES_PER_POINT);
-                               citizenStatus.stakedBytes += amount;
-                               citizenStatus.points += bonusPoints;
-                               pool.totalPoints += bonusPoints;
+                               citizenStatus.stakedBytes = citizenStatus.stakedBytes + amount;
+                               citizenStatus.points = citizenStatus.points + bonusPoints;
+                               pool.totalPoints = pool.totalPoints + bonusPoints;
                        }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1154-L1165
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1154:		unchecked {
1155:			uint256 points = amount * 100 / 1e18 * timelockMultiplier / _DIVISOR;
1156:
1157:			// Update the caller's LP token stake.
1158:			stakerLPPosition[msg.sender].timelockEndTime =
1159:				block.timestamp + timelockDuration;
1160:			stakerLPPosition[msg.sender].amount += amount;
1161:			stakerLPPosition[msg.sender].points += points;
1162:
1163:			// Update the pool point weights for rewards.
1164:			pool.totalPoints += points;
1165:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..7f52897 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1157,11 +1157,11 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        // Update the caller's LP token stake.
                        stakerLPPosition[msg.sender].timelockEndTime =
                                block.timestamp + timelockDuration;
-                       stakerLPPosition[msg.sender].amount += amount;
-                       stakerLPPosition[msg.sender].points += points;
+                       stakerLPPosition[msg.sender].amount = stakerLPPosition[msg.sender].amount + amount;
+                       stakerLPPosition[msg.sender].points = stakerLPPosition[msg.sender].points + points;

                        // Update the pool point weights for rewards.
-                       pool.totalPoints += points;
+                       pool.totalPoints = pool.totalPoints + points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1514-L1516
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1514:		unchecked {
1515:			pool.totalPoints -= stakedCitizen.points;
1516:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..89a1f6d 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1512,7 +1512,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S1_CITIZEN];
                unchecked {
-                       pool.totalPoints -= stakedCitizen.points;
+                       pool.totalPoints = pool.totalPoints - stakedCitizen.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1579-L1581
```solidity
File: contracts/staking/NeoTokyoStaker.sol
1579:		unchecked {
1580:			pool.totalPoints -= stakedCitizen.points;
1581:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/./contracts/staking/NeoTokyoStaker.sol
index a54d218..f93562f 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1577,7 +1577,7 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                // Update caller staking information and asset data.
                PoolData storage pool = _pools[AssetType.S2_CITIZEN];
                unchecked {
-                       pool.totalPoints -= stakedCitizen.points;
+                       pool.totalPoints = pool.totalPoints - stakedCitizen.points;
                }
```

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1622-L1631
```solidity
File: contracts/staking/NeoTokyoStaking.sol
1622:		unchecked {
1623:			uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;
1624:
1625:			// Update the caller's LP token stake.
1626:			lpPosition.amount -= amount;
1627:			lpPosition.points -= points;
1628:
1629:			// Update the pool point weights for rewards.
1630:			pool.totalPoints -= points;
1631:		}
```
```diff
diff --git a/contracts/staking/NeoTokyoStaker.sol b/contracts/staking/NeoTokyoStaker.sol
index a54d218..2513548 100644
--- a/contracts/staking/NeoTokyoStaker.sol
+++ b/contracts/staking/NeoTokyoStaker.sol
@@ -1623,11 +1623,11 @@ contract NeoTokyoStaker is PermitControl, ReentrancyGuard {
                        uint256 points = amount * 100 / 1e18 * lpPosition.multiplier / _DIVISOR;

                        // Update the caller's LP token stake.
-                       lpPosition.amount -= amount;
-                       lpPosition.points -= points;
+                       lpPosition.amount = lpPosition.amount - amount;
+                       lpPosition.points = lpPosition.points - points;

                        // Update the pool point weights for rewards.
-                       pool.totalPoints -= points;
+                       pool.totalPoints = pool.totalPoints - points;
                }
```

## Use storage instead of memory for structs/arrays
If you are not returning an entire struct (all fields) in a function then it is more gas efficient to use a storage pointer rather than using a memory pointer (which copies all the values in the struct from storage into memory). If you use a memory pointer and do not access all the struct fields in your function, then you are performing unecessary sloads. With this in mind, only the [4th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L719), [6th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L736), [13th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281), [14th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290), and [15th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313) instances caught by the `c4udit tool` are instances where a struct is being copied from storage into memory and therefore have the hightest potential for gas savings. Below are some clarifications regarding those instances:

- Modifying the [4th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L719) and [6th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L736) instances to use a storage pointer will not yield any substantial gas savings since all of the struct values are accessed (meaning the same amount of sloads would occur if you used a storage pointer) in the function. In addition, all the struct values are also being returned in memory. 

- Modifying the [15th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1313) instance to use a storage pointer would actually be less efficient in some cases since the struct values can potentially be read more than once during the function call. When using a memory pointer, the values would be loaded (MLOAD) from memory each time. When using a storage pointer, the values would be loaded (SLOAD) from storage each time. If you were to use a storage pointer in this instance you should consider caching the `window.startTime` and `window.reward` values to avoid re-reading those values from storage. 

- Modifying the [13th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1281) and [14th](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1290) instances to use a storage pointer will result in substantial gas savings due to the fact that only **one** struct value is intended to be read during each loop. Although the function only reads one struct value from memory, the result of using a memory pointer is that ALL of the struct values are loaded from storage (5 unecessary SLOADs for the `StakedS1Citizen` struct and 2 unecessary SLOADs from the `StakedS2Citizen` struct) into memory. Using a storage pointer instead of a memory pointer for these two instances would result in the following gas savings:

### Gas savings for `stake()`, obtained via protocol's tests: Avg 4463 gas
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  154353  |  423836  |  277026  |    71   |
| After  |  149743  |  423836  |  272563  |    71   |

### Gas Savings for `withdraw()`, obtained via protocol's tests: Avg 5346 gas
|        |    Min   |    Max   |   Avg   | # calls  |
| ------ | -------- | -------- | ------- | -------- |
| Before |  102046  |  267114  |  187080  |    18   |
| After  |  102046  |  257972  |  181734  |    18   |