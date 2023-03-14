# Summary
|ID     | Finding|  Gas saved | Instances|
|:----: | :---           |  :----:         |:----:         |
|G-01      |Save to memory first when you plan to use it later| 40 |  2 |
|G-02      |if or require statements that can revert should be at the top of the function| - |  2 |
|G-03      |Using double if/require statement instead of && consumes less gas| 15 |  4 |
|G-04      |Unnecessary if statements| 30 |  2 |
|G-05      |IGenericGetter can be made immutable| 40 |  2 |
|G-06      |LastRewardTime doesn't need to be a mapping| 26000 |  1 |
|G-07      |Don't call getReward when staking| - |  1 |
# Details
## [G-01] Save to memory first when you plan to use it later
When you use a variable twice, save it first to memory so you can read twice from memory instead of storage. This saves 1 sload which is 100 gas. With the other calculations substracted this saves on average 40 gas. The more you need to read from memory, the more gas you will save.

[NeoTokyoStaker.sol#L967-L978](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L967-L978)
```diff
		unchecked {
-			citizenStatus.points =
-				identityPoints * vaultMultiplier * timelockMultiplier /
-				_DIVISOR / _DIVISOR;
+			uint256 points = identityPoints * vaultMultiplier * timelockMultiplier /
+				_DIVISOR / _DIVISOR;
+			citizenStatus.points = points;
			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

			// Record the caller's staked S1 Citizen.
			_stakerS1Position[msg.sender].push(citizenId);

			// Update the pool point weights for rewards
-			pool.totalPoints += citizenStatus.points;
+			pool.totalPoints += points;
		}
```
The same can also be done for staking a season 2 citizen [NeoTokyoStaker.sol#L1154-L1165](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1154-L1165)
## [G-02] if or require statements that can revert should be at the top of the function
This is to prevent the function from doing all sort of calculations when it's destined to revert and hereby save gas.
|Code     |Explanation| Function |
|:----: | :---           |  :----:         |
|[NeoTokyoStaker.sol#L1070-L1073](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1070-L1073)|Checking time lock end time should be done directly after getting the citizenStatus from storage| `_stakeBytes()` |
|[NeoTokyoStaker.sol#L1092-L1094](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1092-L1094)|Checking time lock end time should be done directly after getting the citizenStatus from storage| `_stakeBytes()` |
## [G-03] Using double if/require statement instead of && consumes less gas
Saves around 15 gas for require statements and 40 for if statements
```diff
-	if (j != 0 && _inputs[i].rewardWindows[j].startTime <= lastTime) {
+   if (j!=0) {
+       if ( _inputs[i].rewardWindows[j].startTime <= lastTime){
            revert RewardWindowTimesMustIncrease();
-       } 
    }
```
Also used at:
- [NeoTokyoStaker.sol#L910](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L910)
- [NeoTokyoStaker.sol#L917](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L917)
- [NeoTokyoStaker.sol#L926](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L926)
## [G-04] Unnecessary if statements
|Code     |Explanation| Function | Gas saved |
|:----: | :---           |  :----:         |:----:         |
|[BYTES2.sol#L126-L128](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L126-L128)|With the current setup, daoCommission will always be greater than 0 if reward is greater than 0| `getReward()` | 20 |
|[NeoTokyoStaker.sol#L632-L635](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L632-L635)|Citizen will always exist if you can transfer it| `stake()` | 10 |
## [G-05] IGenericGetter can be made immutable

```diff
+  	IGenericGetter immutable public vault;
+	IGenericGetter immutable public citizen;

L588
    constructor (
		address _bytes,
		address _s1Citizen,
		address _s2Citizen,
		address _lpToken,
		address _identity,
		address _vault,
		uint256 _vaultCap,
		uint256 _noVaultCap
	) {
		BYTES = _bytes;
		S1_CITIZEN = _s1Citizen;
		S2_CITIZEN = _s2Citizen;
		LP = _lpToken;
		IDENTITY = _identity;
		VAULT = _vault;
		VAULT_CAP = _vaultCap;
		NO_VAULT_CAP = _noVaultCap;
+		vault = IGenericGetter(VAULT);
+		citizen = IGenericGetter(S1_CITIZEN);
	}
```
The rest of the IGenericGetter initializations can be removed. Around 40 gas saved with this optimization. It also makes the code a bit cleaner.
## [G-06] LastRewardTime doesn't need to be a mapping
When a user claims the reward, [`lastRewardTime`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1433-L1435) is set the same for all asset types. So a nested mapping is unnecessary and it can be a single mapping. 

This optimization saves two cold loads because we now access 3 times the same value so we only have to perform one cold load and then 2 hot loads. Also 2 sloads are saved because we only need to write the current timestamp to storage once. 

When I run the tests:
- `stake()`: avg gas saved: 26202
- `withdraw()`: avg gas saved: 9884

```diff
L319:
-	mapping ( address => mapping ( AssetType => uint256 )) public lastRewardTime;
+	mapping ( address => uint256) public lastRewardTime;

L1310:
-	uint256 lastPoolRewardTime = lastRewardTime[_recipient][_assetType];
+	uint256 lastPoolRewardTime = lastRewardTime[_recipient];

L1433:
-	lastRewardTime[_recipient][AssetType.S1_CITIZEN] = block.timestamp;
-	lastRewardTime[_recipient][AssetType.S2_CITIZEN] = block.timestamp;
-	lastRewardTime[_recipient][AssetType.LP] = block.timestamp;
+   lastRewardTime[_recipient]= block.timestamp;
```
## [G-07] Don't call `getReward()` when staking
It's extremely expensive if you always call [`getReward()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1226) when you stake. This includes a lot of iterations and 2 mints to the caller and the DAO. Almost 80k gas can be saved when you let the user call `getReward()` whenever he wants and not do it when he stakes an asset.
