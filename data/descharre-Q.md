# Summary
## Low Risk
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|L-01       |Missing 0 address checks in the constructor|2|
|L-02       |`identityCreditYield` returns empty string if one combination is missing|1|
|L-03       |Function shouldn't revert when class is not equal to Hand of Citadel|1|
|L-04       |No guarantee that timelockDuration and timelockMultiplier is always the same|1|
|L-05       |Maximum value of AssetType is 3|2|
|L-06       |Staking gets more expensive the more assets you have staked|1|


## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|N-01       |100 / 100 = 1| 1|
|N-02       |Duplicated code| 2|
|N-03       |Unnecessary if statements| 2|
|N-04       |Constant values such as a call to keccak256(), should be immutable rather than constant| 3|
|N-05       |Missing checks for zero address| 1|

# Details
## Low Risk
## [L-01] Missing 0 address checks in the constructor
The constructor in [BYTES2](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol) is missing 0 address checks. Allowing zero-addresses can lead to contract reverts and force redeployments if there are no setters for such address variables. This counts for `BYTES1` and `S1_CITIZEN`, which are both immutable.

The same applies to the constructor in [NeoTokyoStaker.sol#L588-L606](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L588-L606).

## [L-02] `identityCreditYield` returns empty string if one combination is missing
The identityCreditYields gets supplied in the function [`configureIdentityCreditYields()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1760-L1773). However the admin must supply all possible combinations for citizenRewards and vaultRewards. If one combination is missing the function [`getCreditYield()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L625-L645) will return an empty string. There are 105 combinations so it's definitely possible to miss one.

An example:
IdentityCreditYields put in by the admin:
- identityCreditYield[5]["LOW"] = "LOW"
- identityCreditYield[5]["HIGH"] = "MEDIUM"
- identityCreditYield[10]["VERY HIGH"] = "HIGH"

When user stakes a citizen, `getCreditYield()` gets called. 

Good scenario:
- rewardRate is 5 and and vaultMultiplier is HIGH: `getCreditYield()` returns "MEDIUM"

Bad scenario:
- rewardRate is 4 and vaultMultiplier is HIGH: `getCreditYield()` returns an empty string

When the function returns an empty string, this will result in [`StakedS1Citizen`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L362) having 0 points.

I put this as low because it's an admin mistake if this happens and the identityCreditYields can always get resupplied. However the first few users will be disadvantaged by this a lot. Because this will mean that the staked Citizen will get no rewards. I suggest to have an extra require check in the stake function to check if the returned string is not empty.
```diff
L938:
		// Determine the base worth in points of the S1 Citizen's Identity.
		string memory citizenCreditYield = getCreditYield(
			citizenId,
			citizenVaultId
		);
+       require (!_stringEquals(memory, ""), "Credit Yield is empty string");
		uint256 identityPoints = identityCreditPoints[citizenCreditYield];
```

The same applies to [`vaultCreditMultiplier`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L659-L671) but this has a much lower chance because it only has 6 values.
## [L-03] Function shouldn't revert when class is not equal to Hand of Citadel
If a user attempts to claim a Hand of The Citadel bonus it will put 1 as last parameter of the [`stake()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1196-L1255) function. If the handClaimant parameter is 1 and isn't equal to "Hand of Citadel" the transaction will revert. It's better to get the real vaultMultiplier instead of reverting. This way when a sets the parameter by accident to 1, the transaction will still succeed.

[NeoTokyoStaker.sol#L947-L959](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L947-L959)
```diff
		uint256 vaultMultiplier = 100;
		if (handClaimant == 1) {
			uint256 identityId = citizen.getIdentityIdOfTokenId(citizenId);
			string memory class = IGenericGetter(IDENTITY).getClass(identityId);
			if (_stringEquals(class, "Hand of Citadel")) {
				vaultMultiplier = vaultCreditMultiplier["?"];
-           } else {
+			} else if (vaultId != 0) {
-				revert CitizenIsNotHand(citizenId);
+			    vaultMultiplier = getConfiguredVaultMultiplier(vaultId);
			}

		// Otherwise use the configured Vault multiplier, if any.
		} else if (vaultId != 0) {
			vaultMultiplier = getConfiguredVaultMultiplier(vaultId);
		}
```
## [L-04] No guarantee that timelock and timelockMultiplier is always the same
When you stake LP tokens, the function checks if the current multiplier is equal to the new multiplier. However there is no guarantee that the duration is still the same due to a mistake when configuring the timelock options. This will mean that the staker will have the same multiplier but maybe a longer or shorter duration. This can happen because the duration of the timelock is in its upper 128 bits and the multiplier is in the lower 128 bits. To fix this the duration should also be saved to the `stakerLPPosition` and added into the if check.

[NeoTokyoStaker.sol#L1143-L1150](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1143-L1150)
```soldity
		if (stakerLPPosition[msg.sender].multiplier == 0) {
			stakerLPPosition[msg.sender].multiplier = timelockMultiplier;

		// If a multiplier exists already, we must match it.
		} else if (stakerLPPosition[msg.sender].multiplier != timelockMultiplier) {
			revert MismatchedTimelock();
		}
```
## [L-05] Maximum value of AssetType is 3
When you want to stake or withdraw an asset you have the pass a parameter AssetType. This is an enum with 4 values. The first item of the enum always has index 0. So this enum has a maximum index of 3. However the transaction will only revert if the enum index is bigger than 4. This should be changed to >=4 or >3.
[NeoTokyoStaker.sol#L1205](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1205)
```solidity
		if (uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```
[NeoTokyoStaker.sol#L1668-L1670](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1668-L1670)
```solidity
		if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
			revert InvalidAssetType(uint256(_assetType));
		}
```
## [L-06] Staking gets more expensive the more assets you have staked
When you stake, the function [`stake()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1226) calls [`getReward()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L129). GetReward will by his turn call [`claimReward()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1409-L1454). 

When you claim it will iterate over all your staked assets. The more staked assets you have, the more gas it cost. A whale with 50 staked assets can easily pay double in gas when he calls `stake()`. A solution to this is to not call `getReward()` in the stake function and let the user claim his rewards whenever he want.

## Non critical
## [N-01] 100 / 100 = 1
When you stake a season 2 citizen, the points are calculated as follow: `100 * timelockMultiplier / _DIVISOR`. Divisor is also 100 so it's useless do to all the calculations.

[NeoTokyoStaker.sol#L1022](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1022)
```diff
		unchecked {
-			citizenStatus.points = 100 * timelockMultiplier / _DIVISOR;
+			citizenStatus.points = timelockMultiplier;
			citizenStatus.timelockEndTime = block.timestamp + timelockDuration;

			// Record the caller's staked S2 Citizen.
			_stakerS2Position[msg.sender].push(citizenId);

			// Update the pool point weights for rewards
			pool.totalPoints += citizenStatus.points;
		}
```
## [N-02] Duplicated code
[`getCreditYield()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L638-L641) and [`getConfiguredVaultMultiplier()`](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L659-L671) use the same code to get the vaultMultiplier. A small code cleanup can be done by making a seperate function.
## [N-03] Unnecessary if statements
Code can be made cleaner if you remove these useless if checks
|Code     |Explanation| Function | 
|:----: | :---           |  :----:         |
|[BYTES2.sol#L126-L128](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L126-L128)|With the current setup, daoCommission will always be greater than 0 if reward is greater than 0| `getReward()` |
|[NeoTokyoStaker.sol#L632-L635](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L632-L635)|Citizen will always exist if you can transfer it| `getCreditYield()` |
## [N-04] Constant values such as a call to keccak256(), should be immutable rather than constant
Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.
- [BYTES2.sol#L37](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L37)
- [BYTES2.sol#L40](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L40)
- [NeoTokyoStaker.sol#L206-L220](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L206-L220)
## [N-05] Missing checks for zero address
Some functions are missing a zero address check. This is not an issue but it will do the whole process for a 0 address and will unneccesary waste gas. This can be prevented by having a 0 address check.
- [BYTES2.sol#L114-L129](https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/BYTES2.sol#L114-L129)
