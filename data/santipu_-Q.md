## Wrong check for `_asssetType` in `stake` and `withdraw` function

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1204-L1207
https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1664-L1670

In the `stake` function there is a check to enforce the `_assetType` argument provided by the user to be less than 4. This check is actually wrong and should enforce `_assetType` to be less than 3 because there's only 4 types of asset so the last type will be index 3. 

This causes that if a user provides an `_assetType` of 4 the check passes but it should revert because there's no asset with index 4. 

This same issue also happens in the `withdraw` function, where the check enforces `_assetType` to be 0, 1, 3 or 4. An `_assetType` of value 4 should revert because there's no assset with index 4. 

This issue doesn't affect the core functionality of the functions mentioned but it makes the code work not as intended.

```solidity
// Validate that the asset being staked is of a valid type.
if (uint8(_assetType) > 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```
```solidity
/*
	Validate that the asset being withdrawn is of a valid type. BYTES may not 
	be withdrawn independently of the Citizen that they are staked into.
*/
if (uint8(_assetType) == 2 || uint8(_assetType) > 4) {
	revert InvalidAssetType(uint256(_assetType));
}
```

## Redundant check in `stake` function 

https://github.com/code-423n4/2023-03-neotokyo/blob/main/contracts/staking/NeoTokyoStaker.sol#L1214-L1217

In the `stake` function there's a redundant check that it's never going to revert. The code is the following:

```solidity
// Validate that the asset being staked matches an active pool.
if (_pools[_assetType].rewardWindows[0].startTime >= block.timestamp) {
	revert InactivePool(uint256(_assetType));
}
```

As the core dev of Neo Tokyo stated in the Discord:

_"On reward windows:
When we deploy, each asset pool array of reward windows will be guaranteed to have as its first element a reward window configured with a starting time of 0, as in the test cases."_

https://discord.com/channels/810916927919620096/1083447614508384377/1084308890704945174

Following this statement, the check will never revert because `_pools[_assetType].rewardWindows[0].startTime` will always be zero so it can never be greater or equal than `block.timestamp`. 

#### Mitigation steps
The check should be removed from the code to improve readability, reduce code size and avoid confusions. 